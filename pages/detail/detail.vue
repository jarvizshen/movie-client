<template>
	<image :src="img" style="height: 500px;"></image>
	<view class="movie_name">
		<h1>片名：{{name}}</h1>
	</view>
	<h3>导演：</h3>
	<view class="director">
		<image :src="director.img" style="height: 400px;"></image>
		<view class="director_name">
		<p>{{director.name}}</p>
		</view>
	</view>
	<h3>演员：</h3>
	<swiper class="actors" :autoplay="false" :interval="2000" :duration="500" :circular="true"
		indicator-active-color="000" :easing-function="true" :indicator-dots="true" style="height: 500px;">
		<swiper-item v-for="actor in actors">
			<view>
				<image :src="actor.img" style="height: 400px;"></image>
				<view class="actor_name">
				<p>{{actor.name}}</p>
				</view>
			</view>
		</swiper-item>
	</swiper>
	<view class="rating" v-for="rating in ratings">
		<h3>评分：</h3>
		<p>{{rating.title}} {{rating.ratio}}% <progress :percent="rating.ratio"></progress></p>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: '',
				img: '',
				actors: [],
				director: {},
				ratings: []
			}
		},
		onLoad(option) {
			const movie = JSON.parse(decodeURIComponent(option.movie))
			// console.log(movie);
			this.actors = movie.actors
			this.director = movie.director
			// console.log(this.actors);
			this.name = movie.name
			this.img = movie.big_image
			uni.request({
				url: `http://localhost:8080/api/rating?movieName=${this.name}`,
				success: (result) => {
					if (result.statusCode == 200) {
						// console.log(result);
						this.ratings = result.data
						console.log(this.ratings);
					}
				}
			})
		}

	}
</script>

<style scoped>
.movie_name{
	text-align: center;
}
.director_name{
	text-align: center;
}
.actor_name{
	text-align: center;
}
</style>
