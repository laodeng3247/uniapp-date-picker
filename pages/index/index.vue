<template>
	<view class="custom-calendar">
		<view class="title">
			<div @click="lastMonth">lefticon</div>

			{{year}}年{{month<10?'0'+month:month}}月

			<div @click="nextMonth">righticon</div>
		</view>
		<view class="week">
			<view class="week-item" v-for="(item,index) in weekArr" :key="index">
				{{item}}
			</view>
		</view>
		<template v-if="!isSeletctAll">
			<swiper class="swiper" :current="swiperCurrent">
				<swiper-item v-for="item in daysArr" :key="item.id">
					<view class="day-wrap">
						<view :class="['day-item',{'active':current === i.value}]" v-for="i in item.arr" :key="i.id"
							@click="selectDate(i.value)">
							<text class="day">{{i.value}}</text>
						</view>
					</view>
				</swiper-item>
			</swiper>
		</template>
		<template v-else>
			<view class="posiStyle">
				<view class="day-wrap" v-for="item in daysArr" :key="item.id">
					<view :class="['day-item',{'active':current === i.value}]" v-for="i in item.arr" :key="i.id"
						@click="selectDate(i.value)">
						<text class="day">{{i.value}}</text>
					</view>
				</view>
				<view class="u-calendar__content__bg-month">{{month}}</view>
			</view>

		</template>
		<view class="more">
			<div @click="switchShowMode">showicon</div>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			showAll: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {
				// false展示一行  true展示所有 
				// showAll: false,
				// 年
				year: "",
				// 月
				month: "",
				// 日
				day: "",
				swiperCurrent: 0,
				// 星期几
				weekday: 1,
				// 每天
				daysArr: [],
				// 当前选中
				current: 1,
				weekArr: ['周日', '周一', '周二', '周三', '周四', '周五', '周六'],
				isSeletctAll: false
			}
		},
		created() {
			this.init();
		},
		methods: {
			// 选择日期
			selectDate(val) {
				if (!val) return;
				this.current = val;
				//这里我组装成了{year:YYYY,  month:MM,  day:DD}的格式
				//你们可以自己返回想要的数据格式
				this.$emit('current', {
					year: this.year,
					month: this.month,
					day: this.current,
				})
			},
			// 切换显示模式
			switchShowMode() {
				if (this.isSeletctAll == false) {
					this.isSeletctAll = true
				} else {
					this.isSeletctAll = false
				}
				// this.showAll = !this.showAll;
				this.getSwiperCurrent();
			},
			// 获取当前Swiper Current
			getSwiperCurrent() {
				this.swiperCurrent = this.daysArr.findIndex((item) => {
					return item.arr.some(i => i.value === this.current);
				});
			},
			// 初始化
			init() {
				let now = new Date();
				this.year = now.getFullYear();
				this.month = now.getMonth() + 1;
				this.day = now.getDate();
				this.current = this.day;
				//这里我组装成了{year:YYYY,  month:MM,  day:DD}的格式
				//你们可以自己返回想要的数据格式
				this.$emit('current', {
					year: this.year,
					month: this.month,
					day: this.current,
				})
				this.isSeletctAll = this.showAll
				this.changeData();
				this.getSwiperCurrent();
			},
			// 获取当前星期几
			getWeekday(year, month) {
				let date = new Date(`${year}/${month}/01 00:00:00`);
				return date.getDay() === 0 ? 7 : date.getDay();
			},
			//一个月有多少天
			getMonthDay(year, month) {
				let days = new Date(year, month, 0).getDate();
				return days;
			},
			// 切换日期 该函数逻辑完善不建议改动 但可精简优化
			changeData() {
				this.day = this.getMonthDay(this.year, this.month);
				this.weekday = this.getWeekday(this.year, this.month);
				let daysArr = this.generateArray(1, this.day)
				for (let i = 0; i < this.weekday; i++) {
					daysArr.unshift("")
				}
				let arr = [];
				daysArr.map((item, index) => {
					if (index !== 0 && index % 7 === 0) {
						if (index === daysArr.length - 1) {
							this.daysArr.push({
								id: Math.random(),
								arr
							});
							arr = [];
							arr.push({
								id: Math.random(),
								value: item
							});
							this.daysArr.push({
								id: Math.random(),
								arr
							});
							if (arr.length !== 7) {
								const len = arr.length
								for (let i = 0; i < 7 - len; i++) {
									arr.push("")
								}
							}
						} else {
							this.daysArr.push({
								id: Math.random(),
								arr
							});
							arr = [];
							arr.push({
								id: Math.random(),
								value: item
							});
						}
					} else if (index === daysArr.length - 1) {
						arr.push({
							id: Math.random(),
							value: item
						});
						if (arr.length !== 7) {
							const len = arr.length
							for (let i = 0; i < 7 - len; i++) {
								arr.push("")
							}
						}
						this.daysArr.push({
							id: Math.random(),
							arr
						});
					} else {
						arr.push({
							id: Math.random(),
							value: item
						});
					}
				});
				this.daysArr = this.daysArr
				console.log(this.daysArr, 11111)
			},
			generateArray: function(start, end) {

				// debugger
				let key = new Array(end + 1).keys()
				let arr = Array.from(key)
				return arr.slice(start);
			},
			// 上一月
			lastMonth() {
				if (this.month == 1) {
					this.month = 12
					this.year = this.year - 1
				} else {
					this.month = this.month - 1
				}
				this.day = 1
				this.daysArr = []
				this.changeData()
				// this.showAll = true
			},
			//下一月
			nextMonth() {
				if (this.month == 12) {
					this.month = 1
					this.year = this.year + 1
				} else {
					this.month = this.month + 1
				}
				this.day = 1
				this.daysArr = []
				this.changeData()
				this.showAll = true
			}
		}
	}
</script>

<style scoped lang="scss">
	.custom-calendar {
		background-color: #FFFFFF;
		position: sticky;
		top: 0;
		/*  #ifdef  H5  */
		top: 44px;
		/*  #endif  */
		z-index: 99;
		padding: 0 20rpx;

		.title {
			padding: 20rpx 32rpx;
			text-align: center;
			font-weight: bold;

			.left {
				padding: 10rpx;
				margin-right: 30rpx;
				background-color: #E4F0FC;
				border-radius: 8rpx;
			}

			.right {
				padding: 10rpx;
				margin-left: 30rpx;
				background-color: #E4F0FC;
				border-radius: 8rpx;
			}
		}

		.week {
			display: flex;
			align-items: center;
			text-align: center;

			.week-item {
				flex: 1;
			}
		}

		.day-wrap {
			display: flex;

			.day-item {
				z-index: 2;
				display: flex;
				justify-content: center;
				flex: 1;
				padding: 20rpx 0;
				flex-direction: column;
				text-align: center;
				border-radius: 8rpx;

				.day {
					font-weight: bold;
				}

				.num {
					font-size: 24rpx;
					color: #8F9BB3;
					transform: scale(.8);
				}

				&.active {
					background-color: #0676ED;
					color: #FFFFFF;

					.num {
						color: #FFFFFF;
					}
				}
			}
		}

		.swiper {
			height: 60rpx !important;
		}

		.more {
			text-align: center;
		}
	}

	.posiStyle {
		position: relative;
	}

	/deep/.u-calendar__content__bg-month {
		position: absolute;
		font-size: 260rpx;
		line-height: 260rpx;
		left: 50%;
		top: 50%;
		-webkit-transform: translate(-50%, -50%);
		transform: translate(-50%, -50%);
		color: #e4e7ed;
		z-index: 1;
	}
</style>