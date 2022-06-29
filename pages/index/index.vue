<template>
	<view class="content">
		<view class="text-area" v-for="(item,index) in casts" :key="index">
			<uni-card>
				<text>{{item.dayweather}}</text>
			</uni-card>
		</view>
	</view>
</template>

<script>
	export default {
		async onLoad() {
				let _this = this
				console.log('触发获取地理位置之前')
				await _this.getLocation().then(async ([longitude, latitude]) => {
					// // 调用经纬度获取地理信息函数
					await _this.adressInfo(longitude, latitude)

				})
				// 获取天气信息
				uni.request({
					url: `https://restapi.amap.com/v3/weather/weatherInfo`,
					data: {
						key: 'a1d4f15846a044361f29b7ecb4256e0c',
						city: _this.adcode,
						// 获取全部天气预报 4条
						extensions: 'all'
					},
					success(res) {
						// console.log('res', res)
						let casts = res.data.forecasts[0].casts
						_this.casts = casts
						console.log('casts', casts)
					}
				})
		},
		data() {
			return {
				// 国家
				country: '',
				// 省份
				province: '',
				// 地区
				district: '',
				// 地区编号
				adcode: '',
				// 四天天气情况
				casts:[]
			}
		},
		methods: {
			// 获取当前地理位置
			getLocation: function() {
				console.log('触发获取地理位置之中')
				let _this = this
				return new Promise((reslove, reject) => {
					uni.getLocation({
						type: 'wgs84',
						success: function(res) {
							// 经度
							let longitude = res.longitude
							// 纬度
							let latitude = res.latitude
							reslove([longitude, latitude])
						}
					});

				})
			},
			// 根据经纬度获取地理信息
			adressInfo: function(longitude, latitude) {
				let _this = this
				console.log('根据经纬度获取地理信息之中')
				return new Promise((reslove, reject) => {
					uni.request({
						url: 'https://restapi.amap.com/v3/geocode/regeo',
						data: {
							key: 'a1d4f15846a044361f29b7ecb4256e0c',
							location: `${longitude},${latitude}`
						},
						success(res) {
							let addressComponent = res.data.regeocode
								.addressComponent
							_this.country = addressComponent.country
							_this.province = addressComponent.province
							_this.district = addressComponent.district
							_this.adcode = addressComponent.adcode
							reslove(addressComponent.adcode)
						}
					})

				})
			}


		}
	}
</script>

<style lang="less">
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		.text-area {
			display: flex;
			justify-content: center;
			.uni-card{
				width: 100%;
			}
		}
	}

	
</style>
