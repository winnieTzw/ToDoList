<template>
	<view class="content">
		<!-- 选项卡布局 -->
		<view v-if="list.length!==0" class="todo-header">
			<!-- 选项卡左侧布局 -->
			<view class="todo-header__left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<!-- 选项卡右侧布局 -->
			<view class="todo-header__right">
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex===0}" @click="tab(0)">全部</view>
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex===1}"@click="tab(1)">待办</view>
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex===2}"@click="tab(2)">已完成</view>
			</view>
		</view>
		<!-- 缺省页面 -->
		<view v-if="list.length===0" class="default">
			<view class="image-default">
				<image src="../../static/default.png" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="default-info__text">您还未创建任何代办事项！</view>
				<view class="default-info__text">点击下方+号创建</view>
			</view>
		</view>
		<!-- 待办项列表 -->
		<view v-else class="todo-content">
			<view class="todo-list" :class="{'todo--finish':item.checked}" v-for="(item,index) in listData" :key="index" @click="finish(item.id)">
				<view class="todo-list__checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list__content">
					<text>
						{{item.content}}
					</text>
				</view>
			</view>
		</view>
		<!-- 创建按钮 -->
		<view class="create-todo" :class="{'create-todo-active':textShow}" @click="create">
			<text class="iconfont icon-add"></text>
		</view>
		<!-- 创建文本框 -->
		<view v-if="active" class="create-content" :class="{'create--show':textShow}">
			<view class="create-content-box">
				<view class="create-input">
					<input type="text" v-model="value" placeholder="请输入要创建的ToDo..."/>
				</view>
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
				list:[],
				active:false,
				value:'',
				activeIndex:0,
				text:'全部',
				textShow:false
			}
		},
		onLoad() {

		},
		computed:{
			listData(){
				let list = JSON.parse(JSON.stringify(this.list))
				let newList = []
				
				
				if(this.activeIndex === 0){
					this.text = '全部'
					return list
				}
				if(this.activeIndex === 1){
					this.text = '待办'
					list.forEach((item)=>{
						if(!item.checked){
							newList.push(item)
						}
					})
					return newList
				}
				if(this.activeIndex === 2){
					this.text = '已完成'
					list.forEach((item)=>{
						if(item.checked){
							newList.push(item)
						}
					})
					return newList
				}
				return[]
			}
		},
		methods: {
			create(){
				if(this.active){
					this.close()
				}else{
					this.open()
				}
			},
			open(){
				this.active = true
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.textShow = true
					},50)
				})
			},
			close(){
				this.textShow = false
				this.$nextTick(()=>{
					this.active = false
				},350)
			},
			add(){
				if(this.value==''){
					uni.showToast({
						title:"请输入内容！",
						icon:"none"
					})
					return
					}
				this.list.unshift({
					id:'id'+new Date().getTime(),
					content:this.value,
					checked:false
					})
				this.value = ''
				this.close()
			},
			finish(id){
				let index = this.list.findIndex((item)=>item.id===id)
				this.list[index].checked = !this.list[index].checked
			},
			tab(index){
				this.activeIndex = index
			}
		}
	}
</script>

<style>
	@import '../../comm/icon.css';
	.todo-header{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		display: flex;
		align-items: center;
		padding: 0 15px;
		height: 45px;
		color: #333333;
		box-sizing: border-box;
		font-size: 12px;
		box-shadow: -1px 1px 5px 0 rgba(0,0,0,0.1);
		background: #FFFFFF;
		z-index:10;
	}
	.todo-header__left{
		width: 100%;
	}
	.todo-header__right{
		flex-shrink: 0;
		display: flex;
	}
	.todo-header__right-item{
		padding: 0 5px;
	}
	.active-tab{
		color: #279abf;
	}
	.active-text{
		font-size: 14px;
		color: #279abf;
		padding-right: 10px;
	}
	.todo-content{
		position: relative;
		padding-top: 45px;
		padding-bottom: 150px;
	}
	.todo-list{
		display: flex;
		align-items: center;
		position: relative;
		padding: 15px;
		margin: 15px;
		color: #0c3854;
		font-size: 14px;
		border-radius: 10px;
		background: #cfebfd;
		box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1),-1px 2px 1px 0 rgba(255,255,255) inset;
		overflow: hidden;
	}
	.todo-list::after{
		position: absolute;
		content: '';
		top: 0;
		bottom: 0;
		left: 0;
		width: 5px;
		background: #91d1e8;
	}
	.todo-list__checkbox{
		padding-right: 15px;
	}
	.checkbox{
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background: #FFFFFF;
		box-shadow: 0 0 5px 1px rgba(0,0,0,0.1);
	}
	.todo--finish .checkbox{
		position: relative;
		background: #eee;
	}
	.todo--finish .checkbox::after{
		content: '';
		position: absolute;
		width: 10px;
		height: 10px;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		margin: auto;
		background: #cfebfd;
		border-radius: 50%;
		box-shadow: 0 0 2px 0 rgba(0,0,0,0.2) inset;
	}
	.todo--finish .todo-list__content{
		color: #999999;
		
	}
	.todo--finish.todo-list:before{
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
	.todo--finish.todo-list::after{
		background: #cccccc;
	}
	.create-todo{
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		bottom: 20px;
		left: 0;
		right: 0;
		width: 50px;
		height: 50px;
		margin: 0 auto;
		border-radius: 50%;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1),-1px 1px 1px 0 rgba(255,255,255) inset;
	}
	.icon-add{
		font-size: 30px;
		color: #add8e6;
	}
	.create-content{
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		transition: all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%);
	}
	.create--show{
		opacity: 1;
		transform: scale(1) translateY(0);
	}
	.create-content-box{
		display: flex;
		align-items: center;
		padding: 0 0 0 15px;
		border-radius: 50px;
		background: #DEEFF5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1),-1px 1px 1px 0 rgba(255,255,255) inset;
		z-index: 2;
	}
	.create-content-box::after{
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -10px;
		width: 20px;
		height: 20px;
		margin: 0 auto;
		background: #DEEFF5;
		transform: rotate(45deg);
	}
	.create-input{
		width: 100%;
		padding-right: 15px;
		color: #ADD8E6;
	}
	.create-button{
		display: flex;
		justify-content: center;
		align-items: center;
		width: 80px;
		height: 50px;
		flex-shrink: 0;
		border-radius: 50px;
		color: #88d4ec;
		font-size: 14px;
		box-shadow: -2px 0px 2px 1px rgba(0,0,0,0.1);
	}
	.create-content::after{
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -10px;
		width: 20px;
		height: 20px;
		margin: 0 auto;
		background: #DEEFF5;
		transform: rotate(45deg);
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1);
		z-index: -1;
	}
	.default{
		padding-top: 100px;
	}
	.image-default{
		display: flex;
		justify-content: center;
	}
	.image-default image{
		width: 100%;
	}
	.default-info{
		text-align: center;
		font-size: 14px;
		color: #CCCCCC;
	}
	.icon-add{
		transition: transform 0.3s;
	}
	.create-todo-active{
		transform: rotate(135deg);
	}
</style>
