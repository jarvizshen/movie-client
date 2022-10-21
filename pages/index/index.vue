<template>
	<view class="content">
		<view class="top">
			<view class="index-button">
				<view class="icon iconfont icon-1804-liufupinggongnengtubiao_huaban1fuben23"></view>
			</view>
			<view class="top-bar">
				<view class="nav-item" v-for="type in typeList" @click="clearPage(),loadMoviesByType(type.name)">
					{{type.name}}
				</view>
			</view>
			<!-- <view class="index-button">
				<view class="icon iconfont icon-icon_search"></view>
			</view> -->
		</view>
		<view class="images-wall">
			<view class="flow wall-left">
				<list>
					<cell class="image-item" v-for="movie,index in movieList">
						<view v-if="index%2==0" @click="movieDetail(movie.content)">
							<image :src="movie.content.img" mode="widthFix" class="image"></image>
							<view class="title">{{movie.content.name}}</view>
							<view class="author-bar">
								<view class="author-info">
									<view class="author">{{movie.content.director?movie.content.director.name:""}}
									</view>
								</view>
								<view class="praise-count">
									<view class="number">
										{{movie.content.rating}}
									</view>
								</view>
							</view>
						</view>
					</cell>
				</list>

			</view>
			<view class="flow wall-right">
				<list>
					<cell class="image-item" v-for="movie,index in movieList">
						<view v-if="index%2!=0" @click="movieDetail(movie.content)">
							<image :src="movie.content.img" mode="widthFix" class="image"></image>
							<view class="title">{{movie.content.name}}</view>
							<view class="author-bar">
								<view class="author-info">
									<view class="author">{{movie.content.director?movie.content.director.name:""}}
									</view>
								</view>
								<view class="praise-count">
									<view class="number">
										{{movie.content.rating}}
									</view>
								</view>
							</view>
						</view>
					</cell>
				</list>

			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				movieList: [],
				pageIndex: 0,
				typeList: [],
				typeName: ''
			}
		},
		onLoad() {
			this.loadTypes()
			this.loadMovies();
		},
		onPullDownRefresh() {
			this.loadMovies();
		},
		onReachBottom() {
			this.pageIndex++
			if (this.typeName == '') {
				this.loadMovies()
			} else {
				this.loadMoviesByType(this.typeName)
			}
		},
		methods: {
			movieDetail(movie) {
				uni.navigateTo({
					url: `/pages/detail/detail?movie=${encodeURIComponent(JSON.stringify(movie))}`
				})
			},
			loadTypes() {
				uni.request({
					url: 'http://localhost:8080/api/movies/types',
					success: (result) => {
						if (result.statusCode == 200) {
							this.typeList = result.data
						}
					}
				})
			},
			clearPage() {
				this.pageIndex = 0
			},
			loadMoviesByType(movieType) {
				this.typeName = movieType
				uni.request({
					url: `http://localhost:8080/api/types?movieType=${this.typeName}&&page=${this.pageIndex}`,
					method: 'GET',
					success: (result) => {
						if (result.statusCode == 200) {
							if (result.data && result.data.content) {
								if (this.pageIndex == 0) {
									uni.pageScrollTo({
										scrollTop: 0,
										duration: 300
									})
									this.movieList = result.data.content
								} else {
									this.movieList = this.movieList.concat(result.data.content)
								}
								console.log(this.movieList)
							}
							
						}
					}
				})
			},
			//获取影片列表 
			loadMovies() {
				uni.request({
					url: "http://localhost:8080/api/movies?page=" + this.pageIndex,
					method: 'GET',
					success: (result) => {
						console.log("收到的结果：", result)
						if (result.statusCode == 200) {
							if (result.data && result.data.content) {
								if (this.pageIndex == 0) {
									this.movieList = result.data.content
								} else {
									this.movieList = this.movieList.concat(result.data.content)
								}
							}
							console.log(this.movieList)
						}
					},
					complete: () => {
						uni.stopPullDownRefresh()
						uni.hideLoading()
					}
				});


			}
		}
	}
</script>

<style>
	.content {
		padding-top: 80px;
		padding-bottom: 50px;
		background-color: #efefef;
	}

	.top {
		width: 750rpx;
		position: fixed;
		display: flex;
		justify-content: center;
		align-items: center;
		top: 0;
		left: 0;
		z-index: 999;
		background-color: #fff;
	}

	.top-bar {
		flex: 1;
		overflow-x: scroll;
		display: flex;
		flex-wrap: nowrap;
		padding: 20rpx 10rpx;
		align-items: center;
		justify-content: flex-start;
		position: relative;
	}

	.index-button {
		flex-shrink: 0;
		width: 80rpx;
	}

	.index-button .icon {
		font-size: 46px;
		color: #fce71c;
	}

	.index-button .icon-icon_search {
		color: #7A7E83;
		font-size: 30px;
	}

	.top-bar .nav-item {
		margin-right: 50rpx;
		flex-shrink: 0;
		font-size: 100%;
		color: #a2a2a2;
		padding: 15rpx 5rpx;
	}

	.top-bar .nav-item.actived {
		color: #000000;
		font-size: 105%;
		text-shadow: 0px 0px 1px rgba(0, 0, 0, 0.3);
		border-bottom: 2px solid #efefef;
	}

	.images-wall {
		display: flex;
		width: 750rpx;
		justify-content: space-between;
		align-items: flex-start;
		background: #ebebeb;
		min-height: 100vh;
		flex-wrap: wrap;
	}

	.images-wall .image-item {
		width: 363rpx;
		flex-shrink: 0;
		border-radius: 5px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		background: #fff;
		margin-top: 20rpx;
		margin-left: 7rpx;
		margin-right: 7rpx;
	}

	.images-wall .image-item .image {
		width: 100%;
		min-height: 60px;
		border-radius: 5px 5px 0 0;
	}

	.images-wall .image-item .title {
		padding: 5rpx 10rpx;
	}

	.images-wall .image-item .author-bar {
		padding: 5rpx 10rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.images-wall .image-item .author-bar .author {
		color: #686868;
		font-size: 70%;
		margin-left: 20rpx;
	}

	.images-wall .image-item .author-bar .author-info {
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}

	.images-wall .image-item .author-bar .author-info .avartar {
		width: 50rpx;
		height: 50rpx;
	}

	.images-wall .image-item .author-bar .author-info .avartar image {
		width: 100%;
		height: 100%;
		border-radius: 50%;
	}

	.images-wall .image-item .author-bar .praise-count {
		display: flex;
		align-items: center;
		color: #686868;
	}

	.images-wall .image-item .author-bar .praise-count .number {
		margin-left: 10rpx;
		font-size: 90%;
	}
</style>
