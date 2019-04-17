<template>
	<view class="container">
		<view class="top">
			<view class="top-left">
				<view class="top-left-box">
					<view class="box" v-show="!recommend">
						<navigator @tap="clickshow()">推荐</navigator>
					</view>
					<view class="box navigator" v-show="recommend">
						<navigator>推荐</navigator>
					</view>
				</view>
				<view class="top-left-box">
					<view class="box" v-show="!special">
						<navigator @tap="clickshow2()">专题</navigator>
					</view>
					<view class="box navigator" v-show="special">
						<navigator>专题</navigator>
					</view>
				</view>
				<view class="top-left-box">
					<view class="box" v-show="!serialize">
						<navigator @tap="clickshow3()">连载</navigator>
					</view>
					<view class="box navigator" v-show="serialize">
						<navigator>连载</navigator>
					</view>
				</view>
			</view>
			<view class="top-right">
				<view class="search">
					<navigator url="../search/search">
						<image src="../../static/search-grey.png"></image>
					</navigator>
				</view>
			</view>
		</view>

		<view class="article-box">
			<view class="article" v-for="(article, index) in articles" :key="index" v-show="recommend">
				<!-- 标题 -->
				<text class="article-title" @tap="gotoDetail(article.id)">{{ article.title }}</text>
				<!-- 大于等于三张图片的显示方式 -->
				<view class="" v-if="article.imgs.length >= 3">
					<view class="thumbnail-box">
						<view class="thumbnail-item" v-for="(item, index1) in article.imgs" :key="index1">
							<image :src="item.imgUrl"></image>
						</view>
					</view>
				</view>
				<!-- 小于三种图片的显示方式 -->
				<view class="" v-else-if="article.imgs.length >= 1">
					<view class="text-img-box">
						<view class="left">
							<rich-text :nodes="article.content" bindtap="tap"></rich-text>
						</view>
						<view class="right">
							<image :src="article.imgs[article.imgs.length - 1].imgUrl"></image>
						</view>
					</view>
				</view>
				<!-- <view class="" v-else-if="article.imgs.length <=3">
					<view class="text-img-box">
						<view class="left">
							<text>{{handleContent(article.content)}}</text>
						</view>
						<view class="right">
							<image :src="article.imgs[article.imgs.length - 1].imgUrl"></image>
						</view>
					</view>
				</view> -->
				<!-- 没有图片的显示方式 -->
				<view class="text-box" v-else>
					<text>{{ article.title }}</text>
				</view>
				<!-- 文章作者等信息 -->
				<view class="article-info">
					<image :src="article.avatar" class="avatar small"></image>
					<text class="info-text">{{article.nickname}}</text>
					<!-- <text class="info-text">{{article.createTime}}</text> -->
					<view class="bb">
						<graceHeader title="弹出层" desc="请点击按钮测试 ^_^ "></graceHeader>
						<view class="grace-bg-white grace-common-mt grace-padding grace-common-border">
							<view>
								<button  @tap="showBanner" class="aa"><image src="../../static/share.png" class="tan"></image></button>
								<!-- <button type="primary" @tap="showBanner2" style="margin:15px 0;">打开弹出层演示2</button> -->
							</view>
						</view>
						<!-- 弹出层演示 1 -->
						<graceMaskView :show="show" bgcolor="#FFFFFF" v-on:close="closeBanner">
							<view>
								<image src="../../static/nav3-a.png" style="width:100%; margin-top:25px; border-top-right-radius:5px; border-top-left-radius:5px;"
								 mode="widthFix"></image>
							</view>
							<!-- <view style="padding:25px; padding-bottom:30px;">
								<button type='warn' style="background:#F6644D; padding:0 20px;">一个按钮</button>
							</view> -->
						</graceMaskView>
						<!-- 
						<graceMaskView :show="show2" bgcolor="none" v-on:close="closeBanner2">
							<view @tap="tap2">
								<image src="http://static.hcoder.net/graceui/hb.png" style="width:100%; border-radius:5px;" mode="widthFix"></image>
							</view>
						</graceMaskView> -->
					</view>
				</view>
				
			</view>
		</view>

		<view class="btn">
			<navigator url="../write/write" hover-class="navigator-hover">
				<view class="btn-up">
					<image src="../../static/pan.png"></image>
				</view>
			</navigator>
		</view>
		
	</view>
</template>

<script>
	import graceMaskView from "../../graceUI/components/graceMaskView.vue";
	export default {
		data() {
			return {
				articles: [],

				recommend: true,
				special: false,
				serialize: false,
				show : false,
			};
		},
		components :{
			graceMaskView
		},
		onLoad: function() {
			this.getArticles();
		},
		onShow: function() {},
		onPullDownRefresh: function() {
			this.getArticles();
		},
		methods: {
			// 第1个演示 开启与关闭
			showBanner : function(){
				this.show = true;
			},
			closeBanner : function(){
				this.show = false;
			},
			clickshow: function() {
				this.recommend = true;
				this.special = false;
				this.serialize = false;
			},
			clickshow2: function() {
				this.recommend = false;
				this.special = true;
				this.serialize = false;
			},
			clickshow3: function() {
				this.recommend = false;
				this.special = false;
				this.serialize = true;
			},
			getArticles: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/article/list',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: res => {
						_this.articles = res.data.data;
					},
					complete: function() {
						uni.stopPullDownRefresh();
					}
				});
			},
			gotoDetail: function(aId) {
				uni.navigateTo({
					url: '../article_detail/article_detail?aId=' + aId
				});
			},
			handleTime: function(date) {
				var d = new Date(date);
				var year = d.getFullYear();
				var month = d.getMonth() + 1;
				var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
				var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
				var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
				var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
				return year + '-' + month + '-' + day + ' ' + hour + ':' + minutes + ':' + seconds;
			},
			// 
			// 			handleContent: function(msg) {
			// 				return msg.substring(0, 50);
			// 			}
			handleContent: function(msg) {
				let description = msg;
				description = description.replace(/(\n)/g, "");
				description = description.replace(/(\t)/g, "");
				description = description.replace(/(\r)/g, "");
				description = description.replace(/<\/?[^>]*>/g, "");
				description = description.replace(/\s*/g, "");
				return description;
			},

		}
	};
</script>

<style scoped>
	.container {
		font-size: 13pt;
		background: #eeeeee;
	}

	.top {
		width: 100%;
		height: 35px;
		background: #ffffff;
		display: flex;
		justify-content: space-between;
		border-bottom: 1px solid #aaa;

	}

	.top-left {
		margin-left: 3px;
		display: flex;
		width: 50%;

	}

	.top-left-box {
		height: 100%;
		display: flex;
		flex: 1 1 30%;
	}

	.top-right {
		margin-right: 10px;

	}

	.navigator {
		color: #fd572b;
		border-bottom: 2px solid #fd572b;
	}

	.search image {
		width: 32px;
		height: 32px;
	}

	.btn-up {
		width: 60px;
		height: 60px;
		border-radius: 50%;
		color: #9370db;
		position: fixed;
		bottom: 30px;
		right: 30px;
	}

	.btn-up image {
		width: 100%;
		height: 100%;
	}

	.article {
		display: flex;
		flex-direction: column;
		width: 100%;

	}

	.article-title {
		font-weight: bold;
		/* margin-right: 85%; */
	}

	.text-img-box {
		display: flex;
		flex-direction: row;
	}

	.thumbnail-box {
		display: flex;
		flex-direction: row;
	}

	.thumbnail-item image {
		width: 220upx;
		height: 220upx;
		margin-left: 25upx;
	}

	.left {
		flex: 1 1 60%;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 4;
		overflow: hidden;
	}

	.right {
		flex: 1 1 40%;
		width: 100%;
		height: 100%;
	}

	.right image {
		width: 90%;
		height: 200upx;
	}

	.article-info {
		display: flex;
		color: #E9E9E9;
		width: 100%;
		margin-left: 10upx;
		
	}

	.avatar {
		width: 75upx;
		height: 75upx;
		border-radius: 50%;

	}

	.info-text {
		color: #8F8F94;
		margin-left: 10px;
	}
	.tan{
		width:20px;
		height:20px;
	}
	.aa{
		width: 30px;
		height: 30px;
		
	}
	.bb{
		
	}
</style>
