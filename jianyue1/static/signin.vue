<template>
	<view class="uni-flex uni-column container">
		<view v-if="codeshow" style="width: 100%;">
			<view class="psd-register">账号密码登录</view>
			<input class="uni-input" type="number" placeholder="请输入手机号或邮箱" v-model="userDTO.mobile" required="required" />
			<input class="uni-input" password type="text" placeholder="请输入密码" v-model="userDTO.password" required="required" />
			<button @tap="signIn(userDTO)" v-if="userDTO.password.length>=4" class="login">登录</button>
			<button v-if="userDTO.password.length<4" class="login-false">登录</button>
			<view class="login-method">
				<view class="psd-login" @tap="changePages1()">手机验证码登录</view>
				<view class="problem">登录遇到问题？</view>
			</view>
			<view class="other"><text class="inline"></text><text class="other-means">社交账号直接登录</text><text class="inline1"></text></view>
			<view class="means1">
				<view class="means" v-for="(mean,index) in means" :key="index">
					<image :src="mean.img" class="img"></image>
					<view class="name">{{mean.name}}</view>
				</view>
			</view>
			<view class="footer-info">登录代表已经同意<navigator class="nav">用户协议</navigator>和<navigator class="nav">隐私政策</navigator>
			</view>
		</view>
		<view v-if="psdshow">
			<view class="psd-register">手机验证码登录</view>
			<input class="uni-input" type="number" placeholder="请输入手机号" v-model="mobile1" required="required" />
			<view class="codeverify">
				<input class="uni-input" style="flex: 1 1 55%;" password type="text" placeholder="请输入验证码" v-model="verifycode"
				 required="required" />
				<button class="receive" v-show="mobile1.length>0">获取验证码</button>
				<button class="receive" disabled="true" v-show="mobile1.length==0">获取验证码</button>
			</view>
			<button v-if="verifycode.length>=6" class="login" style="position: relative;top: 50upx;">登录</button>
			<button v-if="verifycode.length<6" class="login-false" style="position: relative;top: 50upx;">登录</button>
			<view class="login-method" style="top: 90upx;">
				<view class="psd-login" @tap="changePages()">账号密码登录</view>
				<view class="problem">登录遇到问题？</view>
			</view>
			<view class="other"><text class="inline"></text><text class="other-means">社交账号直接登录</text><text class="inline1"></text></view>
			<view class="means1">
				<view class="means" v-for="(mean,index) in means" :key="index">
					<image :src="mean.img" class="img"></image>
					<view class="name">{{mean.name}}</view>
				</view>
			</view>
			<view class="footer-info1">未注册的手机号登录时将自动注册，且<navigator style="display: flex;justify-content: center;">代表已经同意<navigator
					 class="nav">用户协议</navigator>和<navigator class="nav">隐私政策</navigator>
				</navigator>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userDTO: {
					mobile: '',
					password: '',
				},
				verifycode: '',
				mobile1:'',
				means: [{
						img: '../../static/wx.png',
						name: '微信'
					},
					{
						img: '../../static/qq.png',
						name: 'QQ'
					},
					{
						img: '../../static/weibo.png',
						name: '微博'
					},
					{
						img: '../../static/more.png',
						name: '其他'
					}
				],
				codeshow: true,
				psdshow: false
			};
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: '登录'
			});
		},
		methods: {
			changePages: function() {
				this.codeshow = true;
				this.psdshow = false;
			},
			changePages1: function() {
				this.psdshow = true;
				this.codeshow = false;
			},
			signIn: function(userDTO) {
				var _this = this;
				// console.log(userDTO.mobile + ',' + userDTO.password);
				uni.request({
					url: 'http://localhost:8080/api/user/sign_in',
					method: 'POST',
					data: {
						mobile: userDTO.mobile,
						password: userDTO.password
					},
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						// console.log(res.data.data);
						if (res.data.code == 0) {
							//将用户数据记录在本地存储
							uni.setStorageSync('login_key', {
								userId: res.data.data.id,
								nickname: res.data.data.nickname,
								avatar: res.data.data.avatar,
								token: res.data.data.token,
								login: true
							});
							uni.showToast({
								title: '登录成功'
							});
							uni.navigateBack();
						}
						//登录失败，弹出各种原因
						else {
							uni.showModal({
								title: '提示',
								content: res.data.msg
							});
						}
					}
				});
			}
		}
	};
</script>

<style scoped>
	.container {
		width: 85%;
		margin: 0 auto;
	}

	input {
		margin-top: 40upx;
		height: 65px;
		font-size: 35upx;
		margin-bottom: 5px;
		caret-color: #FD572B;
	}

	.psd-register {
		margin-top: 40upx;
		font-size: 50upx;
		display: flex;
		justify-content: center;
	}

	.login {
		border-radius: 60upx;
		background-color: #FD572B;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #FFFFFF;
		margin-top: 50upx;
	}

	.login-false {
		border-radius: 60upx;
		background-color: #EA6F5A;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #FFFFFF;
		margin-top: 50upx;
	}

	.login-method {
		width: 75%;
		position: relative;
		top: 40upx;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 80upx;
		margin: 0 auto;
	}

	.psd-login {
		flex: 1 1 50%;
		font-size: 30upx;
		color: #3194D0;
		display: flex;
		justify-content: center;
		border-right: 2px solid #E0E0E0;
	}

	.problem {
		flex: 1 1 50%;
		display: flex;
		justify-content: center;
		font-size: 30upx;
		color: #3194D0;
		border: none;
	}

	.other {
		position: relative;
		top: 250upx;
		width: 75%;
		margin: 0 auto;
		height: 80upx;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.inline {
		display: inline-block;
		width: 35px;
		margin-right: 10upx;
		border-top: 1px solid #cccccc;
		vertical-align: 5px;
	}

	.inline1 {
		display: inline-block;
		width: 35px;
		margin-left: 10upx;
		border-top: 1px solid #cccccc;
		vertical-align: 5px;
	}

	.other-means {
		font-size: 35upx;
		color: #757575;
	}

	.means {
		display: flex;
		flex: 1 1 25%;
		height: 100upx;
		align-items: center;
		justify-content: center;
	}

	.img {
		width: 50upx;
		height: 50upx;
	}

	.means1 {
		display: flex;
		width: 100%;
		position: relative;
		top: 250upx;
		height: 120upx;
		align-items: center;
		justify-content: center;
	}

	.name {
		font-size: 30upx;
		margin-left: 15upx;
	}

	.footer-info {
		display: flex;
		width: 80%;
		margin: 0 auto;
		position: relative;
		top: 320upx;
		color: #8A8A8A;
		letter-spacing: 2upx;
		text-wrap: balance;
	}

	.footer-info1 {
		display: flex;
		flex-direction: column;
		width: 80%;
		margin: 0 auto;
		position: relative;
		top: 320upx;
		color: #8A8A8A;
		letter-spacing: 2upx;
		text-wrap: balance;
	}

	.nav {
		color: #19AD1A;
		text-decoration: underline;
	}

	.codeverify {
		display: flex;
		width: 100%;
		height: 65upx;
		margin-top: 40upx;
		justify-content: space-between;
		align-items: center;
	}

	.receive {
		flex: 1 1 32%;
		background-color: #FFFFFF;
		display: flex;
		height: 85upx;
		justify-content: center;
		align-items: center;
		font-size: 30upx;
		color: #FD572B;
		border: 1px solid #EEEEEE;
	}
</style>
