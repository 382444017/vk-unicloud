# 18、image 图片上传

## 万能表单使用方式

```js
{ key: "image1", title: "image类型", type: "image", limit: 9, cloudPathRemoveChinese: true },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| limit | 最大上传数量 | Number | - | - |
| provider | 云存储供应商，可选： unicloud 上传至空间内置存储 extStorage 上传至扩展存储 aliyun 上传至阿里云oss | String | - | - |
| needSave | 是否需要保存图片url到admin后台 | Boolean | false | true |
| categoryId | 当needSave=true时，图片保存的分类id（即vk-files-categories表的_id） | String | - | - |
| cloudPathRemoveChinese | 删除文件名中的中文（默认true） | Boolean | true | false |
| cloudDirectory | 上传至指定的云端目录（默认会以年月日为目录） | String | - | - |
| httpRequest | 覆盖默认的上传行为，可以自定义上传的实现（下方有详细说明） | function | - | - |
| listType | 文件列表的类型 | String | picture-card | text/picture/picture-card |
| drag | 是否开启拖拽上传 | Boolean | false | true |
| fileSize | 限制文件大小 | Number | - | - |
| sizeUnit | 文件的单位 | String | MB | KB、MB、GB |
| autoUpload | 是否在选取文件后立即进行上传，默认为true 如果为false，则提交表单前需主动调用vk.uploadFile来上传 手动上传示例 | Boolean | true | false |
| tempFileType | 当autoUpload为false时，本地文件转为哪种类型 tempPath 文件临时路径 base64 文件base64编码后的值 手动上传示例 | Boolean | true | false |
| buttonText | 当listType=picture或text时，上传按钮的文本 | String | 点击上传 | - |
| tipsImageText | 右侧提示图的文本，一般配合drag=true时使用 如 示例图 | String | - | - |
| tipsImage | 右侧提示图的图片地址，一般配合drag=true时使用 | String | - | - |
| tipsImageStyle | 右侧提示图的图片样式，一般配合drag=true时使用 | String | width: 200px | - |
| beforeRemove | 删除文件之前的钩子，参数为上传的文件和文件列表，若返回 false 或者返回 Promise 且被 reject，则停止删除。详情 | function(file, fileList) | - | - |
| onRemove | 文件列表移除文件时的事件 详情 | function(file, fileList) | - | - |
| onPreview | 用户点击图片预览时的事件（可通过此事件对图片进行处理，如原图替换预览图） 详情 | function(file, open) | - | - |
| 其他 | 其他参数请查看element Upload 上传组件 https://element.eleme.cn/#/zh-CN/component/upload | - | - | - |

### httpRequest 用法

**注意**：如果是上传到unicloud云储存或阿里云OSS，无需写httpRequest，框架已集成。

```js
{
  key: "image1", title: "image类型", type: "image", limit: 9,
  // 此时的 onSuccess 需要自己实现
  onSuccess(data) {
    // 此处写上传成功后，把数据赋值到你的表单变量中，如下
    // 多图上传 that.form1.data.image1.push(data.url);
    // 单图上传 that.form1.data.image1 = data.url;
  },
  httpRequest(obj) {
    let { action, file, filename, data, headers, onProgress, onSuccess, onError } = obj;
    // 在此处写将 file 上传到你指定的地方
    // 上传成功后，需要执行 onSuccess(res);
    // 上传失败时，需要执行 onError(res);
    // 正在上传时，可以监听上传过程，同时执行下方代码，达到显示上传过程进度条的功能 
    /* 
    onProgress({
       percent:progress,
       isTrusted:progress >= 100 ? true:false,
       returnValue:progress >= 100 ? true:false,
       total:progressEvent.total
     }); 
     */
    // 例如
    uni.uploadFile({
      url: 'https://www.example.com/upload', //仅为示例，非真实的接口地址
      file: file,
      name: filename,
      header: {

      },
      formData: {

      },
      onProgressUpdate: (res) => {
        let { progress, totalBytesExpectedToWrite } = res;
        onProgress({
          percent: progress,
          isTrusted: progress >= 100 ? true : false,
          returnValue: progress >= 100 ? true : false,
          total: totalBytesExpectedToWrite
        });
      },
      success: (res) => {
        if (res.statusCode == 200) {
          onSuccess(res.data);
        } else {
          onError(res);
        }
      },
      fail: (res) => {
        onError(res);
      }
    });
  }
},
```

### 手动上传示例

vk-unicloud-admin-ui 的npm依赖需 >= 1.17.6

如果不希望选择图片马上就上传，则可以设置 autoUpload: false，设置后，表单双向绑定的值为图片的本地路径，在最终提交表单前，需要手动执行 vk.uploadFile 来上传。

```js
{
  key: "id_card_front", title:"身份证（正面）", type: "image", limit: 1, width: 800,
  autoUpload: false,
  drag: true,
  fileSize: 2,
  sizeUnit: "mb",
  tips: "只能上传jpg/png文件，且不超过2MB",
  tempFileType: "base64",
  tipsImageText: "示例图",
  tipsImage: "static/tips/id_card_front.png",
},
```

在最终提交表单前，需要手动执行 vk.uploadFile 来上传。

```js
// 表单提交
submitForm(){
  let that = this;
  // 先执行表单验证
  that.$refs.form1.validate(async (valid) => {
    if (valid){
      // 验证通过
      // 需要手动上传的字段名
      let keys = [
        "id_card_front", // 身份证正面
        "id_card_back", // 身份证反面
      ];
      vk.showLoading('上传中...');
      for (let i = 0; i < keys.length; i++) {
        let key = keys[i];
        let val = that.form1.data[key]; // 当前表单绑定的值
        // 判断是否需要上传
        if (typeof val === "object" && val.url && val.url.indexOf("base64,") > -1) {
          if (key.indexOf("id_card") === -1) {
            // 直接上传（不是身份证则直接上传，不加密）
            // base64转file
            let file = await vk.pubfn.base64ToFile({
              base64: val.url,
            })
            // 上传到云存储
            let uploadRes = await vk.uploadFile({
              file: file
            });
            // 修改表单绑定的值为真实文件url地址
            that.form1.data[key] = uploadRes.url;
          } else {
            // 加密上传（是身份证则加密上传）
            let uploadRes = await vk.callFunction({
              url: '加密上传的云函数的路径',
              data: {
                name: val.name,
                base64: val.url
              }
            });
            // 修改表单绑定的值为真实文件url地址
            that.form1.data[key] = uploadRes.url;
          }
        }
      }
      vk.hideLoading();
      that.$refs.form1.submitForm();
    }
  });
},
```

### 加密存储上传的文件

对于特殊类型的图片，例如身份证正面照片，为确保安全性，我们需要对其进行加密存储，不建议直接上传至云存储。因此，在这种情况下，我们需要手动上传，并将 autoUpload 设置为 false。

以下是加密存储的详细流程：

1. 【前端】用户选择文件后，表单自动更新为所选文件的 base64 编码（autoUpload 设置为 false 以及 tempFileType 设置为 base64）
2. 【前端】在表单通过验证并准备提交之前，将 base64 发送请求至云函数进行加密
3. 【云端】发送请求至云函数，云函数执行加密函数 vk.crypto.aes.encrypt，对前端传递的 base64 进行加密，得到加密后的 encryptedBase64
4. 【云端】云函数将加密后的 encryptedBase64 字符串转 buff（let fileBuffer = Buffer.from(encryptedBase64, "utf-8");）得到 fileBuffer
5. 【云端】将 fileBuffer 上传至云存储，上传成功后，得到加密文件的访问路径 encryptedUrl，并将其存储在数据库中
6. 【前端】当需要显示文件时，向云函数发送解密请求
7. 【云端】云函数通过 encryptedUrl 下载加密文件，并读取其内容。然后将二进制内容转换为 base64 格式，得到加密后的 encryptedBase64
8. 【云端】使用 vk.crypto.aes.decrypt 解密加密后的 encryptedBase64，获得真实文件的 base64，并将其返回给前端
9. 【前端】获取解密后的 base64，即可显示文件内容

#### 云函数加密上传示例

```js
'use strict';
module.exports = {
  /**
   * 上传加密的文件
   */
  main: async (event) => {
    let { data = {}, userInfo, util, filterResponse, originalParam } = event;
    let { customUtil, uniID, config, pubFun, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: "" };
    // 业务逻辑开始-----------------------------------------------------------

    let {
      name, // 文件名
      base64, // 文件base64编码后的值
    } = data;
    
    // 加密
    let encryptedBase64 = vk.crypto.aes.encrypt({
      mode: "aes-256-ecb",
      data: base64
    });

    // 加密的base64转buff
    let fileBuffer = Buffer.from(encryptedBase64, "utf-8");
    
    // 上传到云存储
    let uploadFileRes = await uniCloud.uploadFile({
      cloudPath: `${Date.now()}-${name}`,
      cloudPathAsRealPath: true,
      fileContent: fileBuffer
    });
    
    // 返回给前端加密文件的url（就算此url被暴露，加密文件被下载了，对方没有密钥，无法查看文件内的内容）

    res.fileID = uploadFileRes.fileID;
    res.url = uploadFileRes.fileID;

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

#### 云函数解密云存储示例

```js
'use strict';
module.exports = {
  /**
   * 解密文件
   */
  main: async (event) => {
    let { data = {}, userInfo, util, filterResponse, originalParam } = event;
    let { customUtil, uniID, config, pubFun, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: "" };
    // 业务逻辑开始-----------------------------------------------------------
    
    let id_card_front = "加密文件的url";
    
    // 此处应该判断下该用户是否有权限解密此文件
    if (userInfo.role.indexOf("admin") === -1) {
      return { code:-1, msg: "您没有权限访问" };
    }
    
    // 下载图片
    let imageBuffer = await vk.request({
      url: id_card_front,
      method: "GET",
      dataType: "default"
    });
    
    // 将imageBuffer转待解密的字符串
    let decrypt = imageBuffer.toString('utf8');
    
    // 解密得到真实的图片base64
    let id_card_front_base64 = vk.crypto.aes.decrypt({
      mode: "aes-256-ecb",
      data: decrypt
    });
    
    // 返回给前端解密后的文件的base64

    res.base64 = id_card_front_base64

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### onPreview

```js
{ 
  key: "image1", title: "image类型", type: "image", limit: 9, cloudPathRemoveChinese: true,
  onPreview: (file, open) => {
    // 主动调用open函数打开预览弹窗
    open(file.url);
  }
},
```

### beforeRemove

```js
{ 
  key: "image1", title: "image类型", type: "image", limit: 9, cloudPathRemoveChinese: true,
  beforeRemove: (file, fileList) => {
    let url = file.url;
    console.log('url: ', url)
    return new Promise((resolve, reject) => {
      // 异步操作
      vk.showLoading("请求中...");
      setTimeout(() => {
        let success = true; // 假设这是异步操作的结果
        if (success) {
          // 如果操作成功，调用 resolve 方法
          vk.hideLoading();
          resolve();
        } else {
          // 如果操作失败，调用 reject 方法
          vk.hideLoading();
          reject();
        }
      }, 1000); // 假设异步操作需要 1 秒
    });
  }
},
```

### onRemove

```js
{ 
  key: "image1", title: "image类型", type: "image", limit: 9, cloudPathRemoveChinese: true,
  onRemove: (file, fileList) => {
    console.log(file.url);
  }
},
```

## 万能表格使用方式

```js
 { key: "image", title: "图片", type: "image", width: 120 },
```

## template 使用方式

```html
<vk-data-upload v-model="image1" :limit="9"></vk-data-upload>