# Element UI 组件示例

本文档展示了 `pages_template/element/` 目录下的Element UI组件示例代码。

## 表单组件示例

### basic-form.vue - 基础表单

```vue
<template>
  <view class="page-body">
    <div style="padding: 40rpx;font-size: 70rpx;font-family: kaiti;">
      普通表单功能演示
    </div>

    <div style="padding-left: 300rpx;">
      <el-radio-group v-model="data.labelPosition" size="small">
        <el-radio-button label="left">左对齐</el-radio-button>
        <el-radio-button label="right">右对齐</el-radio-button>
        <el-radio-button label="top">顶部对齐</el-radio-button>
      </el-radio-group>

      <div style="margin: 20px;"></div>

      <el-form 
        :label-position="data.labelPosition" 
        label-width="100px" 
        :model="data.ruleForm" 
        :rules="data.rules" 
        ref="ruleForm"
      >
        <el-form-item label="普通表单框" prop="name" required>
          <el-col :span="10">
            <el-input v-model="data.ruleForm.name"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item label="Select选择" prop="region" required>
          <el-col :span="24">
            <el-select v-model="data.ruleForm.region" placeholder="请选择活动区域">
              <el-option label="选项一" value="shanghai"></el-option>
              <el-option label="选项二" value="beijing"></el-option>
            </el-select>
          </el-col>
        </el-form-item>

        <el-form-item label="时间选择器" required>
          <el-col :span="4">
            <el-form-item prop="date1">
              <el-date-picker 
                type="date" 
                placeholder="选择日期" 
                v-model="data.ruleForm.date1" 
                style="width: 100%;"
              ></el-date-picker>
            </el-form-item>
          </el-col>
          <el-col class="line" style="text-align: center;" :span="1">
            <text style="color: #ccc;">—</text>
          </el-col>
          <el-col :span="4">
            <el-form-item prop="date2">
              <el-time-picker 
                placeholder="选择时间" 
                v-model="data.ruleForm.date2" 
                style="width: 100%;"
              ></el-time-picker>
            </el-form-item>
          </el-col>
        </el-form-item>

        <el-form-item label="多级联动" required>
          <div class="block">
            <el-cascader 
              placeholder="多级联动组件展示" 
              :options="data.options" 
              :props="{ multiple: true }" 
              filterable
            ></el-cascader>
          </div>
        </el-form-item>

        <el-form-item label="开关组件" prop="delivery" required>
          <el-switch v-model="data.ruleForm.delivery"></el-switch>
        </el-form-item>

        <el-form-item label="多选框" prop="type" required>
          <el-checkbox-group v-model="data.ruleForm.type">
            <el-checkbox label="选项一" name="type"></el-checkbox>
            <el-checkbox label="选项二" name="type"></el-checkbox>
            <el-checkbox label="选项三" name="type"></el-checkbox>
            <el-checkbox label="选项四" name="type"></el-checkbox>
          </el-checkbox-group>
        </el-form-item>

        <el-form-item label="单选框" prop="resource" required>
          <el-radio-group v-model="data.ruleForm.resource">
            <el-radio label="选项一"></el-radio>
            <el-radio label="选项二"></el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item label="活动形式" prop="desc" required>
          <el-col :span="10">
            <el-input type="textarea" v-model="data.ruleForm.desc"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </view>
</template>

<script>
export default {
  data() {
    return {
      data: {
        options: [
          {
            value: 'zhinan',
            label: '第一层数据',
            children: [{
              value: 'shejiyuanze',
              label: '第二层数据',
              children: [{
                value: 'yizhi',
                label: '数据一'
              }, {
                value: 'fankui',
                label: '数据二'
              }, {
                value: 'xiaolv',
                label: '数据三'
              }, {
                value: 'kekong',
                label: '数据四'
              }]
            }],
          },
        ],
        labelPosition: 'right',
        ruleForm: {
          name: '',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        },
        rules: {
          name: [
            { required: true, message: '该项为必填项', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
          region: [
            { required: true, message: '请至少选择一个', trigger: 'change' }
          ],
          date1: [
            { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
          ],
          date2: [
            { type: 'date', required: true, message: '请选择时间', trigger: 'change' }
          ],
          type: [
            { type: 'array', required: true, message: '请至少选择一个', trigger: 'change' }
          ],
          resource: [
            { required: true, message: '请至少选择一个', trigger: 'change' }
          ],
          desc: [
            { required: true, message: '该项为必填项', trigger: 'blur' }
          ]
        }
      }
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('submit!')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>
```

### advanced-form.vue - 高级表单

```vue
<template>
  <view class="page-body">
    <div style="padding: 40rpx;font-size: 70rpx;font-family: kaiti;">
      高级表单功能演示
    </div>

    <el-form :model="advancedForm" :rules="advancedRules" ref="advancedForm" label-width="120px">
      
      <!-- 动态表单字段 -->
      <el-form-item label="动态字段" prop="dynamicField">
        <el-input v-model="advancedForm.dynamicField"></el-input>
        <el-button @click="addDynamicField" type="text">添加字段</el-button>
      </el-form-item>

      <!-- 条件显示字段 -->
      <el-form-item v-if="advancedForm.showConditional" label="条件字段" prop="conditionalField">
        <el-input v-model="advancedForm.conditionalField"></el-input>
      </el-form-item>

      <!-- 表单验证 -->
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="advancedForm.email" type="email"></el-input>
      </el-form-item>

      <!-- 自定义验证 -->
      <el-form-item label="自定义验证" prop="customValidation">
        <el-input v-model="advancedForm.customValidation"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitAdvancedForm">提交</el-button>
        <el-button @click="resetAdvancedForm">重置</el-button>
      </el-form-item>
    </el-form>
  </view>
</template>

<script>
export default {
  data() {
    return {
      advancedForm: {
        dynamicField: '',
        conditionalField: '',
        email: '',
        customValidation: '',
        showConditional: false
      },
      advancedRules: {
        email: [
          { required: true, message: '请输入邮箱地址', trigger: 'blur' },
          { type: 'email', message: '请输入正确的邮箱格式', trigger: 'blur' }
        ],
        customValidation: [
          { validator: this.validateCustomField, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    validateCustomField(rule, value, callback) {
      if (!value) {
        callback(new Error('请输入内容'))
      } else if (value.length < 3) {
        callback(new Error('长度不能小于3个字符'))
      } else {
        callback()
      }
    },
    addDynamicField() {
      // 添加动态字段逻辑
    },
    submitAdvancedForm() {
      this.$refs.advancedForm.validate((valid) => {
        if (valid) {
          console.log('表单验证通过')
        }
      })
    },
    resetAdvancedForm() {
      this.$refs.advancedForm.resetFields()
    }
  }
}
</script>
```

## 弹窗组件示例

### dialog.vue - 基础弹窗

```vue
<template>
  <view class="page-body">
    <el-button @click="dialogVisible = true">打开弹窗</el-button>

    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
    >
      <span>这是一段信息</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </view>
</template>

<script>
export default {
  data() {
    return {
      dialogVisible: false
    }
  },
  methods: {
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => {})
    }
  }
}
</script>
```

### drawer.vue - 抽屉组件

```vue
<template>
  <view class="page-body">
    <el-button @click="drawer = true" type="primary" style="margin-left: 16px;">
      打开抽屉
    </el-button>

    <el-drawer
      title="我是标题"
      :visible.sync="drawer"
      :direction="direction"
      :before-close="handleDrawerClose"
    >
      <span>我来啦!</span>
    </el-drawer>
  </view>
</template>

<script>
export default {
  data() {
    return {
      drawer: false,
      direction: 'rtl'
    }
  },
  methods: {
    handleDrawerClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => {})
    }
  }
}
</script>
```

## 消息提示组件

### message.vue - 消息提示

```vue
<template>
  <view class="page-body">
    <el-button :plain="true" @click="open1">成功</el-button>
    <el-button :plain="true" @click="open2">警告</el-button>
    <el-button :plain="true" @click="open3">消息</el-button>
    <el-button :plain="true" @click="open4">错误</el-button>
  </view>
</template>

<script>
export default {
  methods: {
    open1() {
      this.$message({
        message: '恭喜你，这是一条成功消息',
        type: 'success'
      })
    },
    open2() {
      this.$message({
        message: '警告哦，这是一条警告消息',
        type: 'warning'
      })
    },
    open3() {
      this.$message('这是一条消息提示')
    },
    open4() {
      this.$message.error('错了哦，这是一条错误消息')
    }
  }
}
</script>
```

### notification.vue - 通知提示

```vue
<template>
  <view class="page-body">
    <el-button @click="open1">成功</el-button>
    <el-button @click="open2">警告</el-button>
    <el-button @click="open3">信息</el-button>
    <el-button @click="open4">错误</el-button>
  </view>
</template>

<script>
export default {
  methods: {
    open1() {
      this.$notify({
        title: '成功',
        message: '这是一条成功的提示消息',
        type: 'success'
      })
    },
    open2() {
      this.$notify({
        title: '警告',
        message: '这是一条警告的提示消息',
        type: 'warning'
      })
    },
    open3() {
      this.$notify.info({
        title: '信息',
        message: '这是一条信息的提示消息'
      })
    },
    open4() {
      this.$notify.error({
        title: '错误',
        message: '这是一条错误的提示消息'
      })
    }
  }
}
</script>
```

## 表格组件示例

### table.vue - 基础表格

```vue
<template>
  <view class="page-body">
    <el-table
      :data="tableData"
      style="width: 100%"
      stripe
      border
    >
      <el-table-column
        prop="date"
        label="日期"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="address"
        label="地址"
      ></el-table-column>
      <el-table-column
        label="操作"
        width="200"
      >
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)"
          >编辑</el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)"
          >删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </view>
</template>

<script>
export default {
  data() {
    return {
      tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        date: '2016-05-01',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }]
    }
  },
  methods: {
    handleEdit(index, row) {
      console.log(index, row)
    },
    handleDelete(index, row) {
      console.log(index, row)
    }
  }
}
</script>
```

## 导航组件示例

### menu.vue - 导航菜单

```vue
<template>
  <view class="page-body">
    <el-menu
      :default-active="activeIndex"
      class="el-menu-demo"
      mode="horizontal"
      @select="handleSelect"
    >
      <el-menu-item index="1">处理中心</el-menu-item>
      <el-submenu index="2">
        <template slot="title">我的工作台</template>
        <el-menu-item index="2-1">选项1</el-menu-item>
        <el-menu-item index="2-2">选项2</el-menu-item>
        <el-menu-item index="2-3">选项3</el-menu-item>
      </el-submenu>
      <el-menu-item index="3">订单管理</el-menu-item>
    </el-menu>
  </view>
</template>

<script>
export default {
  data() {
    return {
      activeIndex: '1'
    }
  },
  methods: {
    handleSelect(key, keyPath) {
      console.log(key, keyPath)
    }
  }
}
</script>
```

## 最佳实践

### 1. 组件导入和注册

```vue
<template>
  <div>
    <el-button>按钮</el-button>
    <el-input placeholder="请输入内容"></el-input>
  </div>
</template>

<script>
import { ElButton, ElInput } from 'element-ui'

export default {
  components: {
    ElButton,
    ElInput
  }
}
</script>
```

### 2. 主题定制

```javascript
// 在main.js中定制主题
import Vue from 'vue'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI, {
  size: 'small',
  zIndex: 3000
})
```

### 3. 国际化配置

```javascript
import Vue from 'vue'
import ElementUI from 'element-ui'
import locale from 'element-ui/lib/locale/lang/zh-CN'

Vue.use(ElementUI, { locale })
```

### 4. 按需引入

```javascript
// 按需引入组件
import { Button, Select } from 'element-ui'

Vue.component(Button.name, Button)
Vue.component(Select.name, Select)
```

## 常见问题

### 1. 样式冲突处理

```css
/* 使用scoped样式 */
<style scoped>
.el-button {
  margin-right: 10px;
}
</style>

/* 使用深度选择器 */
<style scoped>
::v-deep .el-dialog__body {
  padding: 20px;
}
</style>
```

### 2. 表单验证技巧

```javascript
// 自定义验证规则
const validatePass = (rule, value, callback) => {
  if (value === '') {
    callback(new Error('请输入密码'))
  } else if (value.length < 6) {
    callback(new Error('密码长度不能小于6位'))
  } else {
    callback()
  }
}

// 异步验证
const validateAsync = (rule, value, callback) => {
  setTimeout(() => {
    if (value === 'admin') {
      callback(new Error('用户名不能为admin'))
    } else {
      callback()
    }
  }, 1000)
}
```

### 3. 表格性能优化

```vue
<el-table
  :data="tableData"
  v-loading="loading"
  :row-key="row => row.id"
  :tree-props="{children: 'children', hasChildren: 'hasChildren'}"
>
  <!-- 使用固定列减少重排 -->
  <el-table-column
    fixed="left"
    prop="name"
    label="姓名"
    width="120"
  ></el-table-column>
</el-table>
```

## 组件组合示例

### 表单+表格组合

```vue
<template>
  <view class="page-body">
    <!-- 搜索表单 -->
    <el-form :model="searchForm" inline>
      <el-form-item label="关键词">
        <el-input v-model="searchForm.keyword" placeholder="请输入关键词"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSearch">搜索</el-button>
      </el-form-item>
    </el-form>

    <!-- 数据表格 -->
    <el-table :data="tableData" v-loading="loading">
      <el-table-column prop="name" label="名称"></el-table-column>
      <el-table-column prop="status" label="状态">
        <template slot-scope="scope">
          <el-tag :type="scope.row.status === 1 ? 'success' : 'danger'">
            {{ scope.row.status === 1 ? '启用' : '禁用' }}
          </el-tag>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
    ></el-pagination>
  </view>
</template>
```

通过以上示例，您可以快速掌握Element UI组件的使用方法，并应用到实际项目中。