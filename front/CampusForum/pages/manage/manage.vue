<template>
	<view class="big">
		<view class="trade">
			<view class="texts" :class="curr==0?'active':''" data-index="0" @tap="setCurr">
				注册审核
			</view>
			<view class="texts" :class="curr==1?'active':''" data-index="1" @tap="setCurr">
				用户
			</view>
		</view>
		<swiper :current="curr" @change="setCurr" style="height: 700px;">
			<swiper-item>
				<scroll-view>
					<view v-for="(item,index) in applicationList.data">
						<view class="person_container">
							<view class="user">
								<text class="user_name">{{ item.name }}</text>
								<br />
								<text class="user_description">{{ item.description }}</text>
							</view>
							<view :class="item.sign_state?'follow':'unfollow'" @click="cilckPass(index)">
								{{ item.sign_state?'已通过':'通过' }}
							</view>
							<view :class="item.sign_state?'follow':'unfollow'" @click="cilckCancel(index)">
								{{ item.sign_state?'拒绝':'已拒绝' }}
							</view>
						</view>
					</view>
				</scroll-view>
			</swiper-item>
			<swiper-item>
				<scroll-view>
					<view v-for="(item,index) in userList.data">
						<view class="person_container" @click="enterUser(item.id)">
							<image class="user_avater" :src="item.avater"></image>
							<view class="user">
								<text class="user_name">{{ item.name }}</text>
								<br />
								<text class="user_description">{{ item.description }}</text>
							</view>
							<view :class="item.isBanned?'follow':'unfollow'" @click="clickBan()">
								{{ item.isBanned?'封禁':'已封禁' }}
							</view>
						</view>
					</view>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	import * as userApi from "../../api/user.js"
	export default {
		data() {
			return {
				curr:0,
				windowWidth:'',
				applicationList:{
					"totle":"50",
					"page":"1",
					"pageSize":"10",
					"data":[
						{
							"id":"1",
							"username":"facedawn",
							"isPassed":0,
							"description":"???"
						},
						{
							"id":"2",
							"username":"s",
							"isPassed":1
						},
						{
							"id":"3",
							"username":"b",
							"isPassed":0
						},
						
					]
				},
				userList:{
					"totle":"50",
					"page":"1",
					"pageSize":"10",
					"data":[
						{
							"id":"21313",
							"username":"xxxx",
							"avater":"",
							"description":"asdasdsad",
							"isbanned":0,
						}
					]
				},
				 tabIndex:0,
				    tabBars:[
				        { name:"审核注册",id:"application"},
				        { name:"查看用户",id:"user"},
				    ]
			}
		},
		onLoad() {
			//获取applicationList数据，还要传page和pagesize
			
			console.log(123);
			var that=this;
			uni.getSystemInfo({
			    success: function (res) {
					that.windowWidth=res.windowWidth+'px';	
			    }
			});
			
			userApi.selectAllRegisterUser().then(data=>{
				console.log(data)
				if (typeof data === "undefined") {
					uni.showToast({
						title: '服务器错误',
						icon: "error",
						mask: true,
						duration: 2000
					})
				} else if (data.code != 200) {
					uni.showToast({
						title: data.msg,
						icon: "error",
						mask: true,
						duration: 2000
					})
				} else {
					this.applicationList.data=data.data.items
					for(let i=0;i<data.data.items.length;i++)
					{
						this.applicationList.data[i].sign_state=0
					}
				}
			})
			
			userApi.selectAll().then(data=>{
				console.log(data)
				if (typeof data === "undefined") {
					uni.showToast({
						title: '服务器错误',
						icon: "error",
						mask: true,
						duration: 2000
					})
				} else if (data.code != 200) {
					uni.showToast({
						title: data.msg,
						icon: "error",
						mask: true,
						duration: 2000
					})
				} else {
					this.userList.data=data.data.items
				}
			})
		},
		methods: {
			enterUser(userId){
				console.log(userId)
				uni.navigateTo({
				    url: `/pages/otherUsers/otherUsers?id=${userId}`
				})
			},
			setCurr(e) {
				let thisCurr = e.detail.current || e.currentTarget.dataset.index || 0;
				this.curr = thisCurr;
			},
			clickBan(id){
				console.log(id+"ban")
			},
			cilckPass(index){
				if(this.applicationList.data[index].sign_state==0){
					userApi.passRegister(this.applicationList.data[index].id).then(data=>{
						if (typeof data === "undefined") {
							uni.showToast({
								title: '服务器错误',
								icon: "error",
								mask: true,
								duration: 2000
							})
						} else if (data.code != 200) {
							uni.showToast({
								title: data.msg,
								icon: "error",
								mask: true,
								duration: 2000
							})
						} else {
							console.log("ok")
							this.applicationList.data[index].sign_state=1;
						}
					
					})
				}
			},
			cilckCancel(index){
				if(this.applicationList.data[index].sign_state==0){
					userApi.rejectRegister(this.applicationList.data[index].id).then(data=>{
						if (typeof data === "undefined") {
							uni.showToast({
								title: '服务器错误',
								icon: "error",
								mask: true,
								duration: 2000
							})
						} else if (data.code != 200) {
							uni.showToast({
								title: data.msg,
								icon: "error",
								mask: true,
								duration: 2000
							})
						} else {
							console.log("ok")
							this.applicationList.data[index].sign_state=2;
						}
					
					})
				}
			},
			pageChange(e){
				//重新加载applicationList
				this.applicationList.page=e.current;
				console.log(this.applicationList.page);
			},
			 tabtap(index){
			        this.tabIndex=index;
			    }
		}
	}
</script>

<style>
	.idRow{
		width: 10%;
	}
	.usernameRow{
		width: 49%;
	}
	.passButton{
		background-color: #18BC37;
	}
	.cancelButton{
		background-color: #E43D33;
	}
	.tabs {
	    flex: 1;
	    flex-direction: column;
	    overflow: hidden;
	    background-color: #ffffff;
	    /* #ifdef MP-ALIPAY || MP-BAIDU */
	    height: 100vh;
	    /* #endif */
	}
	
	.scroll-h {
	    width: 750upx;
	    height: 80upx;
	    flex-direction: row;
	    /* #ifndef APP-PLUS */
	    white-space: nowrap;
	    /* #endif */
	    /* flex-wrap: nowrap; */
	    /* border-color: #cccccc;
	    border-bottom-style: solid;
	    border-bottom-width: 1px; */
	}
	
	.line-h {
	    height: 1upx;
	    background-color: #cccccc;
	}
	
	.uni-tab-item {
	    /* #ifndef APP-PLUS */
	    display: inline-block;
	    /* #endif */
	    flex-wrap: nowrap;
	    padding-left: 34upx;
	    padding-right: 34upx;
	}
	
	.uni-tab-item-title {
	    color: #555;
	    font-size: 30upx;
	    height: 80upx;
	    line-height: 80upx;
	    flex-wrap: nowrap;
	    /* #ifndef APP-PLUS */
	    white-space: nowrap;
	    /* #endif */
	}
	
	.uni-tab-item-title-active {
	    color: #007AFF;
	}
	
	.trade {
		margin-top: 30rpx;
		width: 100%;
		overflow: auto;
	}
	
	.trade view {
		text-align: center;
		width: 50%;
		float: left;
		font-size: 40rpx;
		font-weight: bold;
		padding-bottom: 10rpx;
	}
	
	.trade .texts.active {
		border-bottom: 8rpx solid #00A1D6;
	}
	
	.person_container {
		margin-bottom: 20rpx;
		padding: 10rpx;
		display: flex;
	}
	
	.user_avater {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
	}
	
	.user {
		margin-left: 15rpx;
	}
	
	.user_name {
		font-size: 45rpx;
		font-weight: bold;
	}
	
	.user_description {
		font-size: 35rpx;
		color: #555555;
	}
	
	.unfollow {
		position: absolute;
		right: 30rpx;
		font-size: 40rpx;
		color: white;
		margin-top: 30rpx;
		width: 200rpx;
		height: 60rpx;
		text-align: center;
		background: #83cbac;
	}
	
	.follow {
		position: absolute;
		right: 30rpx;
		font-size: 40rpx;
		color: white;
		margin-top: 30rpx;
		width: 200rpx;
		height: 60rpx;
		text-align: center;
		background: #b5aa90;
		/* box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19); */
	}
</style>
