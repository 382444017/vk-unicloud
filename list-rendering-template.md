# 列表渲染前后端一体模板

## 概述

列表渲染前后端一体模板是一种高效的开发模式，将前端列表渲染与后端数据接口紧密结合，提供统一的开发体验和更好的性能优化。

## 核心特性

### 1. 统一数据格式
```javascript
// 前端期望的数据结构
{
  list: [],        // 数据列表
  total: 0,        // 总数据量
  page: 1,         // 当前页码
  size: 10,        // 每页大小
  hasMore: true    // 是否有更多数据
}
```

### 2. 前后端约定

#### 前端请求参数
```javascript
{
  page: 1,         // 页码
  size: 10,        // 每页大小
  keyword: '',     // 搜索关键词
  sortField: 'createTime', // 排序字段
  sortOrder: 'desc' // 排序方式
}
```

#### 后端响应格式
```javascript
{
  code: 0,         // 状态码
  msg: 'success',  // 消息
  data: {
    list: [],      // 数据列表
    total: 100,    // 总数据量
    page: 1,       // 当前页码
    size: 10,      // 每页大小
    hasMore: true  // 是否有更多数据
  }
}
```

## 前端实现

### 基础列表组件
```javascript
// 列表组件示例
export default {
  data() {
    return {
      list: [],
      loading: false,
      finished: false,
      page: 1,
      size: 10,
      total: 0
    }
  },
  methods: {
    async loadData() {
      if (this.loading) return
      
      this.loading = true
      try {
        const res = await uniCloud.callFunction({
          name: 'your-function-name',
          data: {
            page: this.page,
            size: this.size,
            // 其他查询参数
          }
        })
        
        if (res.result.code === 0) {
          const data = res.result.data
          this.list = this.page === 1 ? data.list : [...this.list, ...data.list]
          this.total = data.total
          this.finished = !data.hasMore
          this.page++
        }
      } catch (error) {
        console.error('加载数据失败:', error)
      } finally {
        this.loading = false
      }
    }
  }
}
```

## 后端实现

### 云函数模板
```javascript
// 云函数列表查询模板
const db = uniCloud.database()

exports.main = async (event, context) => {
  const { page = 1, size = 10, keyword = '', sortField = 'createTime', sortOrder = 'desc' } = event
  
  // 构建查询条件
  const where = {}
  if (keyword) {
    where.title = new RegExp(keyword, 'i')
  }
  
  // 执行查询
  const collection = db.collection('your-collection')
  const countResult = await collection.where(where).count()
  const total = countResult.total
  
  const listResult = await collection
    .where(where)
    .orderBy(sortField, sortOrder)
    .skip((page - 1) * size)
    .limit(size)
    .get()
  
  return {
    code: 0,
    msg: 'success',
    data: {
      list: listResult.data,
      total,
      page: parseInt(page),
      size: parseInt(size),
      hasMore: page * size < total
    }
  }
}
```

## 最佳实践

1. **分页优化**: 使用游标分页代替偏移分页处理大数据量
2. **缓存策略**: 对静态数据实施缓存减少数据库查询
3. **错误处理**: 统一的错误处理机制
4. **性能监控**: 监控接口响应时间和查询性能

## 常见问题

### Q: 如何处理复杂的查询条件？
A: 使用动态构建查询条件的方式，根据前端传递的参数动态生成where条件

### Q: 如何优化大数据量的分页？
A: 使用基于createTime的游标分页，避免使用skip()方法

### Q: 如何保证数据安全性？
A: 在后端进行参数验证和权限检查，避免SQL注入和数据泄露