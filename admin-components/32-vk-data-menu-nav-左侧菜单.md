# 5、vk-data-menu-nav 左侧菜单

## 介绍

主要用在了 /windows/leftWindow.vue 文件内，功能是渲染左侧树形菜单列表

## 完整示例

```html
<template>
	<scroll-view class="sidebar" :class="classCom" scroll-y="true" v-loading="!vk.getVuex('$app.inited')" :style="styleCom">
		<vk-data-menu-nav
			v-if="vk.getVuex('$app.inited')"
			:data="vk.getVuex('$app.navMenu')"
			:unique-opened="true"
			:collapse="vk.getVuex('$app.leftCollapse')"
			:collapse-transition="false"
			:theme="theme"
			default-menu-icon="el-icon-folder-opened"
			default-sub-menu-icon="el-icon-tickets"
			@select="select"
		></vk-data-menu-nav>
	</scroll-view>
</template>

<script>
	import config from "@/app.config.js";
	export default {
		data() {
			return {
				theme: config.theme
			}
		},
		methods: {
			select(e){

			},
		},
		// 监听属性
		watch: {
			$route: {
				immediate: true,
				handler(newRoute, oldRoute) {
					let that = this;
					let { vk } = that;
					let { path , query } = newRoute;
					let url = path + vk.pubfn.queryParams(query);
					let route = { path, query, url };
					vk.setVuex('$app.route', route);
				}
			}
		},
		// 计算属性
		computed: {
			styleCom(){
				let theme = this.theme;
				if (theme && theme.use) {
					return theme[theme.use].leftMenu;
				} else {
					return {};
				}
			},
			classCom(){
				let obj = {
					pc: vk.getVuex('$app.isPC'),
					mobile: !vk.getVuex('$app.isPC'),
					collapse: vk.getVuex('$app.leftCollapse'),
				};
				return obj;
			}
		}
	}
</script>

<style lang="scss">
	.sidebar {
		position: fixed;
		top: var(--window-top);
		width: 240px;
		height: calc(100vh - (var(--window-top)) + 50px);
		box-sizing: border-box;
		box-shadow: var(--boxShadow, 2px 0 0px rgba(0,21,4,0.2));
		border-top: var(--borderTop);
		background-color: $left-window-bg-color;
		padding-bottom: 10px;
		top: 50px;
		z-index: 998;
	}
	.sidebar.collapse{
		width: 64px;
	}
	.title {
		margin-left: 5px;
	}
	.center{
		text-align: center;
		margin-top: 100px;
	}
</style>
```

## API

### 属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| data | 菜单数据数组 | Array | [] | - |
| mode | 菜单模式 | String | "vertical" | "horizontal" |
| collapse | 是否折叠菜单 | Boolean | false | true, false |
| theme | 主题配置 | String、 Object | - | - |
| background-color | 菜单背景颜色 | String | "#ffffff" | - |
| sub-background-color | 子菜单背景颜色 | String | "#ffffff" | - |
| text-color | 菜单文字颜色 | String | "#515a6e" | - |
| active-text-color | 激活菜单文字颜色 | String | "#409EFF" | - |
| active-background-color | 激活菜单背景颜色 | String | "#ecf5ff" | - |
| unique-opened | 是否只保持一个子菜单展开 | Boolean | false | true, false |
| default-openeds | 默认展开的子菜单数组 | Array | [] | - |
| menu-trigger | 子菜单触发方式 | String | "hover" | "click","hover" |
| router | 是否使用 vue-router 的模式 | Boolean | false | true, false |
| collapse-transition | 是否开启折叠动画 | Boolean | true | true, false |
| default-menu-icon | 默认菜单图标 | String | "el-icon-folder-opened" | - |
| default-sub-menu-icon | 默认子菜单图标 | String | "el-icon-tickets" | - |

### 事件

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| open | 监听 - 菜单展开时 | index |
| close | 监听 - 菜单关闭时 | index |
| select | 监听 - 菜单项被点击时 | item |