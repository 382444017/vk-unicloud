---
type: "manual"
---

# VK-Pay 万能支付插件 - 回调处理和前端组件

## 🔄 支付回调处理

### 回调地址配置

```javascript
// config.js 中配置回调地址
"notifyUrl": {
  "mp-测试空间ID": "https://fc-mp-测试空间ID.next.bspapp.com/http/vk-pay",
  "mp-正式空间ID": "https://fc-mp-正式空间ID.next.bspapp.com/http/vk-pay"
}
```

### 回调处理逻辑

VK-Pay会根据订单类型自动调用对应的回调处理文件：

```javascript
// vk-pay/service/pay-notify/recharge.js - 充值订单回调
module.exports = {
  main: async (event) => {
    let { data, userInfo, util } = event;
    let { vk } = util;

    // 获取回调数据
    let { out_trade_no, user_id, recharge_balance } = data;

    // 处理充值逻辑
    await vk.baseDao.updateById({
      dbName: "uni-id-users",
      id: user_id,
      dataJson: {
        balance: vk.db.command.inc(recharge_balance)
      }
    });

    // 记录充值日志
    await vk.baseDao.add({
      dbName: "recharge_logs",
      dataJson: {
        user_id,
        amount: recharge_balance,
        out_trade_no,
        create_date: new Date().getTime()
      }
    });

    return { code: 0, msg: "充值成功" };
  }
};
```

### 订单类型回调文件

- `recharge.js` - 充值订单回调处理
- `goods.js` - 商品订单回调处理  
- `vip.js` - 会员订单回调处理
- `custom.js` - 自定义订单回调处理

### 商品订单回调示例

```javascript
// vk-pay/service/pay-notify/goods.js
module.exports = {
  main: async (event) => {
    let { data, userInfo, util } = event;
    let { vk } = util;

    let { out_trade_no, user_id, goods_id, quantity } = data;

    // 更新库存
    await vk.baseDao.updateById({
      dbName: "goods",
      id: goods_id,
      dataJson: {
        stock: vk.db.command.inc(-quantity),
        sales: vk.db.command.inc(quantity)
      }
    });

    // 创建订单记录
    await vk.baseDao.add({
      dbName: "goods_orders",
      dataJson: {
        user_id,
        goods_id,
        quantity,
        out_trade_no,
        status: "paid",
        create_date: new Date().getTime()
      }
    });

    return { code: 0, msg: "商品订单处理成功" };
  }
};
```

### 会员订单回调示例

```javascript
// vk-pay/service/pay-notify/vip.js
module.exports = {
  main: async (event) => {
    let { data, userInfo, util } = event;
    let { vk } = util;

    let { out_trade_no, user_id, vip_type, vip_days } = data;

    // 计算会员到期时间
    const expireDate = new Date();
    expireDate.setDate(expireDate.getDate() + vip_days);

    // 更新用户会员信息
    await vk.baseDao.updateById({
      dbName: "uni-id-users",
      id: user_id,
      dataJson: {
        vip_type: vip_type,
        vip_expire_date: expireDate.getTime(),
        is_vip: true
      }
    });

    // 记录会员购买日志
    await vk.baseDao.add({
      dbName: "vip_purchase_logs",
      dataJson: {
        user_id,
        vip_type,
        vip_days,
        out_trade_no,
        create_date: new Date().getTime()
      }
    });

    return { code: 0, msg: "会员订单处理成功" };
  }
};
```

## 📱 前端支付组件

### 基础支付组件使用

```vue
<template>
  <view>
    <!-- 支付组件 -->
    <vk-pay-button
      :order="orderInfo"
      @success="onPaySuccess"
      @fail="onPayFail"
      @cancel="onPayCancel"
    >
      立即支付
    </vk-pay-button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      orderInfo: {
        out_trade_no: "ORDER_" + Date.now(),
        total_fee: 100,
        subject: "商品名称",
        type: "goods",
        custom: {
          user_id: "user_001",
          goods_id: "goods_001",
          quantity: 1
        }
      }
    }
  },

  methods: {
    // 支付成功回调
    onPaySuccess(res) {
      console.log("支付成功", res);
      uni.showToast({
        title: "支付成功",
        icon: "success"
      });
      
      // 跳转到成功页面
      uni.navigateTo({
        url: '/pages/order/success?order_no=' + this.orderInfo.out_trade_no
      });
    },

    // 支付失败回调
    onPayFail(res) {
      console.log("支付失败", res);
      uni.showToast({
        title: res.msg || "支付失败",
        icon: "none"
      });
    },

    // 支付取消回调
    onPayCancel() {
      console.log("用户取消支付");
      uni.showToast({
        title: "支付已取消",
        icon: "none"
      });
    }
  }
}
</script>
```

### 手动调用支付

```javascript
// 引入支付工具
import vkPay from '@/uni_modules/vk-uni-pay/js_sdk/vk-pay.js';

export default {
  methods: {
    async handlePay() {
      try {
        uni.showLoading({
          title: '创建订单中...',
          mask: true
        });

        // 创建支付订单
        let paymentRes = await vk.callFunction({
          url: 'pay/createPayment',
          data: {
            provider: 'wxpay',
            out_trade_no: 'ORDER_' + Date.now(),
            total_fee: 100,
            subject: '测试商品',
            type: 'goods',
            custom: {
              user_id: this.userInfo._id,
              goods_id: this.goodsId,
              quantity: 1
            }
          }
        });

        uni.hideLoading();

        if (paymentRes.code === 0) {
          uni.showLoading({
            title: '调起支付中...',
            mask: true
          });

          // 调起支付
          let payRes = await vkPay.pay({
            provider: paymentRes.provider,
            orderInfo: paymentRes.orderInfo
          });

          uni.hideLoading();

          if (payRes.code === 0) {
            uni.showToast({
              title: '支付成功',
              icon: 'success'
            });
            
            // 处理支付成功逻辑
            await this.handlePaymentSuccess(paymentRes.out_trade_no);
          } else {
            uni.showToast({
              title: payRes.msg || '支付失败',
              icon: 'none'
            });
          }
        } else {
          uni.showToast({
            title: paymentRes.msg || '创建订单失败',
            icon: 'none'
          });
        }
      } catch (err) {
        uni.hideLoading();
        console.error('支付失败:', err);
        uni.showToast({
          title: '支付失败，请重试',
          icon: 'none'
        });
      }
    },

    async handlePaymentSuccess(orderNo) {
      // 查询订单状态确认
      let checkRes = await vk.callFunction({
        url: 'pay/queryPayment',
        data: {
          out_trade_no: orderNo
        }
      });

      if (checkRes.code === 0 && checkRes.data.trade_state === 'SUCCESS') {
        // 跳转到成功页面
        uni.redirectTo({
          url: '/pages/order/success?order_no=' + orderNo
        });
      }
    }
  }
}
```

### 支付状态查询组件

```vue
<template>
  <view class="payment-status">
    <view v-if="loading" class="loading">
      <uni-loading></uni-loading>
      <text>查询支付状态中...</text>
    </view>
    
    <view v-else-if="orderStatus === 'SUCCESS'" class="success">
      <uni-icons type="checkmark" size="60" color="#07C160"></uni-icons>
      <text class="status-text">支付成功</text>
      <text class="amount">￥{{ orderAmount / 100 }}</text>
      <button @click="handleSuccess">完成</button>
    </view>
    
    <view v-else-if="orderStatus === 'NOTPAY'" class="notpay">
      <uni-icons type="close" size="60" color="#FF3B30"></uni-icons>
      <text class="status-text">未支付</text>
      <button @click="handleRepay">重新支付</button>
    </view>
    
    <view v-else class="error">
      <uni-icons type="info" size="60" color="#FF9500"></uni-icons>
      <text class="status-text">支付状态未知</text>
      <button @click="handleRefresh">刷新状态</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      loading: true,
      orderStatus: '',
      orderAmount: 0,
      orderNo: ''
    }
  },
  
  onLoad(options) {
    this.orderNo = options.order_no;
    this.queryOrderStatus();
  },
  
  methods: {
    async queryOrderStatus() {
      this.loading = true;
      try {
        const res = await vk.callFunction({
          url: 'pay/queryPayment',
          data: {
            out_trade_no: this.orderNo
          }
        });
        
        if (res.code === 0) {
          this.orderStatus = res.data.trade_state;
          this.orderAmount = res.data.total_fee;
        }
      } catch (error) {
        console.error('查询订单状态失败:', error);
      } finally {
        this.loading = false;
      }
    },
    
    handleSuccess() {
      uni.navigateBack();
    },
    
    handleRepay() {
      // 重新支付逻辑
      uni.redirectTo({
        url: `/pages/order/payment?order_no=${this.orderNo}`
      });
    },
    
    handleRefresh() {
      this.queryOrderStatus();
    }
  }
}
</script>

<style>
.payment-status {
  padding: 40rpx;
  text-align: center;
}

.loading, .success, .notpay, .error {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20rpx;
}

.status-text {
  font-size: 36rpx;
  font-weight: bold;
}

.amount {
  font-size: 48rpx;
  color: #07C160;
  font-weight: bold;
}

button {
  margin-top: 40rpx;
  width: 200rpx;
}
</style>
```

## 🔄 回调安全验证

### 签名验证

```javascript
// 回调签名验证
function verifyCallbackSignature(data, signature, notifyKey) {
  // 生成签名
  const generatedSignature = crypto
    .createHash('md5')
    .update(JSON.stringify(data) + notifyKey)
    .digest('hex');
  
  return generatedSignature === signature;
}

// 在回调处理中使用
module.exports = {
  main: async (event) => {
    const { data, signature } = event;
    const notifyKey = "your_notify_key";
    
    if (!verifyCallbackSignature(data, signature, notifyKey)) {
      return { code: 400, msg: "签名验证失败" };
    }
    
    // 处理回调逻辑...
  }
};
```

### 重复回调处理

```javascript
// 防止重复回调处理
async function handleCallbackWithIdempotency(callbackId, callbackHandler) {
  // 检查是否已经处理过该回调
  const existingLog = await vk.baseDao.find({
    dbName: "callback_logs",
    where: { callback_id: callbackId }
  });
  
  if (existingLog) {
    return existingLog.result; // 返回之前的结果
  }
  
  // 处理回调
  const result = await callbackHandler();
  
  // 记录回调日志
  await vk.baseDao.add({
    dbName: "callback_logs",
    dataJson: {
      callback_id: callbackId,
      result: result,
      create_date: new Date().getTime()
    }
  });
  
  return result;
}
```

## 🔗 相关文档

- [插件介绍和安装](vk-pay-introduction.md)
- [支付配置详解](vk-pay-configuration.md)
- [支付API使用](vk-pay-api-usage.md)
- [多商户和特殊支付](vk-pay-advanced.md)
- [错误处理和最佳实践](vk-pay-error-handling.md)