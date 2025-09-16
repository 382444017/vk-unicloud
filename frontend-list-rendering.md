---
type: "manual"
description: "列表渲染和分页处理规范"
---

# 列表渲染

VK框架提供了完整的列表渲染解决方案，支持分页、搜索、下拉刷新等功能。

## 前端列表组件

```javascript
export default {
  data() {
    return {
      // 云函数请求路径
      url: "db_test/pub/getList",

      // 列表数据
      list: [],

      // 分页信息
      pagination: {
        current: 1,
        size: 10,
        total: 0
      },

      // 加载状态
      loading: false,
      loadingMore: false,

      // 搜索条件
      searchForm: {
        keyword: "",
        category: ""
      },

      // 列表状态
      listStatus: {
        hasMore: true,
        loadingText: "加载更多",
        noMoreText: "没有更多了"
      }
    }
  },

  onLoad() {
    this.getList(true)
  },

  onReachBottom() {
    if (this.listStatus.hasMore && !this.loadingMore) {
      this.getList(false)
    }
  },

  onPullDownRefresh() {
    this.getList(true, () => {
      uni.stopPullDownRefresh()
    })
  },

  methods: {
    // 获取列表数据
    async getList(refresh = false, callback) {
      if (refresh) {
        this.pagination.current = 1
        this.list = []
      }

      this.loadingMore = true

      try {
        const res = await vk.callFunction({
          url: this.url,
          data: {
            pageIndex: this.pagination.current,
            pageSize: this.pagination.size,
            ...this.searchForm
          }
        })

        if (refresh) {
          this.list = res.rows
        } else {
          this.list = this.list.concat(res.rows)
        }

        this.pagination.total = res.total
        this.listStatus.hasMore = res.hasMore
        this.pagination.current++

      } catch (err) {
        console.error('获取列表失败:', err)
      } finally {
        this.loadingMore = false
        callback && callback()
      }
    },

    // 搜索
    onSearch() {
      this.getList(true)
    },

    // 重置搜索
    onReset() {
      this.searchForm = {
        keyword: "",
        category: ""
      }
      this.getList(true)
    }
  }
}