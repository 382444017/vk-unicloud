# 表单验证规则

## 介绍

表单验证规则用于对前端提交的表单数据进行验证，确保数据的完整性和正确性。

## 基本用法

### 在云函数中使用表单验证

```javascript
'use strict';
exports.main = async (event, context) => {
  // 引入vk-unicloud
  const vk = require('vk-unicloud');
  
  // 定义验证规则
  const rules = {
    username: {
      type: "string",
      required: true,
      min: 3,
      max: 20,
      message: "用户名长度必须在3-20个字符之间"
    },
    email: {
      type: "string",
      required: true,
      format: "email",
      message: "请输入有效的邮箱地址"
    },
    password: {
      type: "string",
      required: true,
      min: 6,
      max: 20,
      message: "密码长度必须在6-20个字符之间"
    },
    age: {
      type: "number",
      required: false,
      min: 18,
      max: 100,
      message: "年龄必须在18-100岁之间"
    }
  };

  try {
    // 验证数据
    await vk.formRules.validate(event.data, rules);
    
    // 验证通过，继续处理业务逻辑
    return {
      code: 0,
      msg: "验证通过"
    };
  } catch (error) {
    // 验证失败，返回错误信息
    return {
      code: -1,
      msg: error.message
    };
  }
};
```

## 验证规则类型

### string类型验证

```javascript
const stringRules = {
  name: {
    type: "string",
    required: true,
    min: 2,
    max: 50,
    message: "姓名长度必须在2-50个字符之间"
  },
  description: {
    type: "string",
    required: false,
    max: 500,
    message: "描述不能超过500个字符"
  }
};
```

### number类型验证

```javascript
const numberRules = {
  price: {
    type: "number",
    required: true,
    min: 0,
    max: 1000000,
    message: "价格必须在0-1000000之间"
  },
  quantity: {
    type: "number",
    required: true,
    min: 1,
    max: 1000,
    message: "数量必须在1-1000之间"
  }
};
```

### array类型验证

```javascript
const arrayRules = {
  tags: {
    type: "array",
    required: true,
    min: 1,
    max: 10,
    message: "标签数量必须在1-10个之间"
  },
  images: {
    type: "array",
    required: false,
    max: 5,
    message: "最多只能上传5张图片"
  }
};
```

### object类型验证

```javascript
const objectRules = {
  address: {
    type: "object",
    required: true,
    message: "地址信息不能为空"
  },
  profile: {
    type: "object",
    required: false,
    message: "个人信息格式不正确"
  }
};
```

## 格式验证

### 邮箱格式验证

```javascript
const emailRule = {
  email: {
    type: "string",
    required: true,
    format: "email",
    message: "请输入有效的邮箱地址"
  }
};
```

### 手机号格式验证

```javascript
const phoneRule = {
  phone: {
    type: "string",
    required: true,
    format: "phone",
    message: "请输入有效的手机号码"
  }
};
```

### URL格式验证

```javascript
const urlRule = {
  website: {
    type: "string",
    required: false,
    format: "url",
    message: "请输入有效的网址"
  }
};
```

### 正则表达式验证

```javascript
const regexRule = {
  username: {
    type: "string",
    required: true,
    pattern: /^[a-zA-Z0-9_]{3,20}$/,
    message: "用户名只能包含字母、数字和下划线，长度3-20位"
  }
};
```

## 自定义验证器

### 同步自定义验证

```javascript
const customRules = {
  password: {
    type: "string",
    required: true,
    validator: (value) => {
      if (value.length < 6) {
        throw new Error("密码长度不能少于6位");
      }
      if (!/[A-Z]/.test(value)) {
        throw new Error("密码必须包含至少一个大写字母");
      }
      if (!/[0-9]/.test(value)) {
        throw new Error("密码必须包含至少一个数字");
      }
    }
  }
};
```

### 异步自定义验证

```javascript
const asyncRules = {
  username: {
    type: "string",
    required: true,
    validator: async (value) => {
      // 检查用户名是否已存在
      const exists = await checkUsernameExists(value);
      if (exists) {
        throw new Error("用户名已存在");
      }
    }
  }
};
```

## 嵌套验证

### 对象嵌套验证

```javascript
const nestedRules = {
  userInfo: {
    type: "object",
    required: true,
    fields: {
      name: {
        type: "string",
        required: true,
        min: 2,
        max: 50
      },
      age: {
        type: "number",
        required: true,
        min: 18,
        max: 100
      }
    }
  }
};
```

### 数组嵌套验证

```javascript
const arrayNestedRules = {
  products: {
    type: "array",
    required: true,
    item: {
      type: "object",
      fields: {
        name: {
          type: "string",
          required: true
        },
        price: {
          type: "number",
          required: true,
          min: 0
        }
      }
    }
  }
};
```

## 错误处理

### 获取详细的错误信息

```javascript
try {
  await vk.formRules.validate(data, rules);
} catch (error) {
  console.log('错误字段:', error.field);
  console.log('错误值:', error.value);
  console.log('错误消息:', error.message);
  console.log('错误规则:', error.rule);
}
```

### 自定义错误消息

```javascript
const rulesWithCustomMessage = {
  username: {
    type: "string",
    required: true,
    message: "用户名是必填项"
  },
  email: {
    type: "string",
    required: true,
    format: "email",
    message: {
      required: "邮箱是必填项",
      format: "请输入有效的邮箱格式"
    }
  }
};
```

## 高级用法

### 条件验证

```javascript
const conditionalRules = {
  paymentMethod: {
    type: "string",
    required: true,
    enum: ["credit_card", "paypal", "bank_transfer"]
  },
  cardNumber: {
    type: "string",
    required: function(value, data) {
      return data.paymentMethod === "credit_card";
    },
    message: "信用卡号是必填项"
  },
  paypalEmail: {
    type: "string",
    required: function(value, data) {
      return data.paymentMethod === "paypal";
    },
    format: "email",
    message: "PayPal邮箱是必填项"
  }
};
```

### 跨字段验证

```javascript
const crossFieldRules = {
  password: {
    type: "string",
    required: true,
    min: 6
  },
  confirmPassword: {
    type: "string",
    required: true,
    validator: (value, data) => {
      if (value !== data.password) {
        throw new Error("两次输入的密码不一致");
      }
    }
  }
};
```

## 最佳实践

1. **始终在服务端进行验证**：不要依赖前端验证，前端验证可以被绕过
2. **提供清晰的错误消息**：帮助用户理解哪里出错了
3. **使用合适的验证规则**：根据业务需求选择合适的验证类型
4. **定期更新验证规则**：随着业务变化调整验证规则
5. **测试验证规则**：确保所有边界情况都被正确处理