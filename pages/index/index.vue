<template>
	<view class="content">
		<!-- 状态栏 -->
		<view v-if="list.length !==0" class="todo-header">
			<!-- 状态栏左侧 -->
			<view class="todo-header_left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<!-- 状态栏右侧 -->
			<view class="todo-header_right">
				<view class="todo-header_right-item" :class="{'active-tab':activeIndex === 0}" @click="tab(0)">
					全部
				</view>
				<view class="todo-header_right-item" :class="{'active-tab':activeIndex === 1}" @click="tab(1)">
					待办
				</view>
				<view class="todo-header_right-item" :class="{'active-tab':activeIndex === 2}" @click="tab(2)">
					已完成
				</view>
			</view>
		</view>
		<!-- 没有数据显示页面 -->
		<view v-if="list.length === 0" class="default">
			<view class="image-default">
				<image src="../../static/default.jpg" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="default-info_text">
					还没有创建任何待办事项
				</view>
				<view class="default-info_text">
					点击下方+创建一个吧
				</view>
			</view>
		</view>
		<!-- 内容区域 -->
		<view v-else class="todo-content">
			<view class="todo-list" :class="{'todo--finish':item.checked}" v-for="(item,index) in listData" :key="index" @click="finish(item.id)">
				<view class="todo-list_checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list_content">
					{{item.content}}
				</view>
			</view>
		</view>
		<!-- 创建按钮 -->
		<view class="create-todo" @click="create">
			<text class="iconfont icon-plus-line" :class="{'create-todo-active':textShow}"></text>
		</view>
		<!-- 弹出框 -->
		<view v-if="active" class="create-content">
			<view class="create-content-box">
				<!-- input输入 -->
				<view class="create-input">
					<input type="text" v-model="value" placeholder="请输入您要创建的todo" />
				</view>
				<!-- 发布按钮 -->
				<view class="create-button" @click="add">
					创建
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				value: '',
				list: [],
				// 控制是否显示
				active: false,
				activeIndex: 0,
				text: '全部',
				textShow: false
			}
		},
		computed: {
			listData() {
				// 将数组转为字符串以后再转为对象
				// 获取全部数据
				let list = JSON.parse(JSON.stringify(this.list))
				// 得到筛选过后的数据
				let newList = []
				// 点击 全部
				if (this.activeIndex === 0) {
					this.text = '全部'
					return list
				}
				// 点击 待办
				if (this.activeIndex === 1) {
					// checked = false
					list.forEach((item) => {
						if (!item.checked) {
							newList.push(item)
						}
					})
					this.text = '待办'
					return newList
				}
				// 点击 已完成
				if (this.activeIndex === 2) {
					if (this.activeIndex === 2) {
						// checked = true
						list.forEach((item) => {
							if (item.checked) {
								newList.push(item)
							}
						})
						this.text = '已完成'
						return newList
					}
				}
			}
		},
		onLoad() {},
		methods: {
			// 打开输入框
			create() {
				if(this.active){
					this.close()
				}else{
					this.open()
				}
			},
			// 打开动画
			open() {
				// 显示输入框
				this.active = true
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.textShow = true
					},50)
				})
			},
			// 关闭动画
			close() {
				this.textShow = false
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.active = false
					},350)
				})
			},
			// 添加待办事项
			add() {
				console.log(this.value)
				// 如果输入框为空 弹出提示
				if (this.value === '') {
					uni.showToast({
						title: "请输入内容",
						icon: "none"
					})
					return
				}
				// 将输入数据放到list数组中
				this.list.unshift({
					// 给list每个内容加一个时间戳
					id: 'id' + new Date().getTime(),
					content: this.value,
					checked: false
				})
				// 置空输入框
				this.value = ''
				// 将输入框隐藏
				this.active = false
				this.close()
			},
			// 点击列表触发
			finish(id) {
				// 找到当前点击的列表所在行
				console.log("我被点击了", id)
				let index = this.list.findIndex((item) => item.id === id)
				console.log("当前点击的行", this.list[index])
				this.list[index].checked = !this.list[index].checked
			},
			tab(index) {
				this.activeIndex = index
			}
		}
	}
</script>

<style>
	@import '../../common/icon.css';

	.todo-header {
		position: fixed;
		top: 0;
		left: 0;
		display: flex;
		align-items: center;
		padding: 0 15px;
		font-size: 12px;
		color: #333333;
		width: 100%;
		height: 45px;
		box-sizing: border-box;
		box-shadow: -1px 1px 5px 0 rgb(0, 0, 0, 0.1);
		background-color: #FFFFFF;
		z-index: 10;
	}

	.todo-header_left {
		width: 100%;
	}

	.active-text {
		font-size: 14px;
		color: #279abf;
		padding: 10px;
	}

	.todo-header_right {
		flex-shrink: 0;
		display: flex;
	}

	.todo-header_right-item {
		padding: 0 5px;
	}

	.active-tab {
		color: #279abf;
	}

	.todo-content {
		position: relative;
		padding-top: 55px;
		padding-bottom: 100px;
	}

	.todo-list {
		position: relative;
		display: flex;
		align-items: center;
		padding: 15px;
		margin: 15px;
		color: #0c3854;
		box-shadow: -1px 1px 5px 1px rgb(0, 0, 0, 0.1), -1px 2px 1px 0 rgba(255, 255, 255) inset;
		border-radius: 10px;
		background: #cfebfd;
		overflow: hidden;
	}

	.todo-list:after {
		position: absolute;
		content: '';
		top: 0;
		bottom: 0;
		left: 0;
		width: 5px;
		background: #91d1e8;
	}

	.todo-list_checkbox {
		padding-right: 15px;
	}

	.checkbox {
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background: #FFFFFF;
		box-shadow: 0 0 5px 1px rgba(0, 0, 0, 0.1);
	}

	.todo--finish .checkbox {
		position: relative;
		background: #eee;
	}

	.todo--finish .checkbox:before {
		content: '';
		position: absolute;
		width: 10px;
		height: 10px;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		border-radius: 50%;
		background: #cfebfd;
		box-shadow: 0 0 2px 0px rgba(0, 0, 0, 0.2) inset;
	}

	.todo--finish .todo-list_content {
		color: #999999;
	}

	.todo--finish.todo-list:before {
		content: '';
		position: absolute;
		top: 0;
		bottom: 0;
		left: 40px;
		right: 10px;
		height: 2px;
		margin: auto 0;
		background: #bdcdd8;
	}

	.todo--finish.todo-list:after {
		background: #cccccc;
	}

	.create-todo {
		display: flex;
		position: fixed;
		bottom: 20px;
		justify-content: center;
		align-items: center;
		right: 0;
		left: 0;
		margin: 0 auto;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255)
	}

	.icon-plus-line {
		font-size: 30px;
		color: #add8e6;
	}

	.create-content {
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
	}

	.create-content-box {
		display: flex;
		align-items: center;
		padding: 0 15px;
		padding-right: 0;
		border-radius: 50px;
		background: #DEEFF5;
		box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255) inset;
		z-index: 2;
	}

	.create-input {
		width: 100%;
		padding-right: 15px;
		color: #ADD8E6;
	}

	.create-button {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-shrink: 0;
		width: 80px;
		height: 50px;
		border-radius: 50px;
		font-size: 16px;
		color: #88d4dc;
		box-shadow: -2px 0px 2px 1px rgba(0, 0, 0, 0.1);
	}

	.create-content:after {
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -9px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
		box-shadow: 1px 1px 5px 2px rgba(0, 0, 0, 0.1);
		z-index: -1;
	}

	.create-content-box:after {
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -9px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
	}

	.default {
		padding-top: 100px;
	}

	.image-default {
		display: flex;
		justify-content: center;
	}

	.image-default image {
		width: 100%;
	}

	.default-info {
		text-align: center;
		font-size: 14px;
		color: #cccccc;
	}
	.icon-plus-line {
		transition: transform 0.3s;
	}
	.create-todo-active {
		transform: rotate(135deg);
	}
</style>
