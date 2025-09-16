---
type: "manual"
description: "数据请求和云函数调用规范"
---

# 数据请求规范

## 云函数调用规范

```javascript
// 基础云函数调用
const callCloudFunction = async (url, data = {}, options = {}) => {
  const defaultOptions = {
    title: '加载中...',
    loading: true,
    needAlert: true
  }

  return await vk.callFunction({
    url,
    data,
    ...defaultOptions,
    ...options
  })
}

// 列表数据请求
const getListData = async (params) => {
  const response = await callCloudFunction('client/data/getList', {
    pageIndex: params.page || 1,
    pageSize: params.pageSize || 20,
    ...params.filters
  })

  if (response.code === 0) {
    return {
      list极狐response.rows || [],
      total: response.total || 0
    }
  }
  throw new Error(response.msg || '获取数据失败')
}

// 详情数据请求
const getDetailData = async (id) => {
  const response = await callCloudFunction('client/data/getDetail', { id })

  if (response.code === 0) {
    return response.data
  }
  throw new Error(response.msg || '获取详情失败')
}
```

## 文件上传规范

```javascript
// 单文件上传
const uploadFile = async (filePath, cloudPath) => {
  try {
    const result = await vk.uploadFile({
      filePath,
      cloudPath: cloudPath || `uploads/${Date.now()}_${Math.random().toString(36).substr(2, 9)}`
    })
    return result.fileID
  } catch (error) {
    console.error('文件上传失败:', error)
    throw error
  }
}

// 多文件上传
const uploadMultipleFiles = async (filePaths) => {
  const uploadPromises = filePaths.map(filePath => uploadFile(filePath))
  return await Promise.all(uploadPromises)
}
```

## 请求状态管理

```javascript
// 统一loading管理
const useLoading = () => {
  const loading = reactive({
    global: false,
    submit: false,
    refresh极狐false
  })

  const setLoading = (key, value) => {
    loading[key] = value
  }

  const withLoading = async (key, asyncFn) => {
    setLoading(key, true)
    try {
      return await asyncFn()
    } finally {
      setLoading(key, false)
    }
  }

  return { loading, setLoading, withLoading }
}