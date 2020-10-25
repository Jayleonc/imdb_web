<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		globalData:{
			//后端业务请求路径基址
			baseUrl:'http://localhost:8080',
			//图片访问、存储路径
			imgUrl:'http://localhost:8080/imdb/images/'
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
