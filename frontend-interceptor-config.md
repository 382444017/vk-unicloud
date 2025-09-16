---
type: "manual"
description: "拦截器配置和全局异常处理"
---

# 拦截器配置

## 配置前端非法token拦截器


```javascript
interceptor:{
  login:(obj)=>{
    let { vk, params, res } = obj;
    setTimeout(()=>{
      uni.navigateTo({
        url:"/pages/login/index/index",
        events:{
          // 监听登录成功后的事件
          loginSuccess: (data) => {
            // 重新执行一次云函数调用
            if(params) Vue.prototype.vk.callFunction(params);
          },
        }
      });
    },300); 
    console.log("跳自己的登录页面");
    // 上方代码可自己修改，写成你自己的逻辑处理。		
  }
}

```

## 配置前端全局异常拦截器

在app.config.js的 interceptor属性新增  
```javascript
interceptor:{
  fail:(obj)=>{
     let { vk, params, res } = obj;
     console.log("params:",params);
     console.log("res:",res);
     return true;// 返回false则取消框架内置fail的逻辑,返回true则会继续执行框架内置fail的逻辑
  }
}

```