<template>
	<view class="">
		<view>
			<uni-section title="倒计时" type="line" padding>
				<uni-countdown
					ref="countDown"
					class="a-center button-flex" 
					:font-size="30" 
					:showDay="false"
					:minute="setMinute" 
					:second="0"
					:start="isCounting"
					color="#FFFFFF" background-color="#007AFF" />
			</uni-section>
		</view>
		<!-- 设置时间盒子 -->
		<view class="">
			<uni-section title="设置一个倒计时吧(1-100mins)" type="line" padding>
				<uni-number-box 
					class="button-flex" 
					v-model="changeValue" 
					:disabled="isCounting"
					:step="0.5" :value="1" :min="0.5"
					></uni-number-box>
			</uni-section>
			
			<view class="button-flex" v-show="!isCounting">
				<button class="mg-tb20" type="primary" size="mini" @click="startCoundDown">开始倒计时!</button>
			</view>
			
			<view class="button-flex" v-show="isCounting">
				<button class="mg-tb20" type="warn" size="mini" @click="stop">停止</button>
				<button class="mg-tb20" type="warn" size="mini" @click="pickTime">计次</button>
				<button class="mg-tb20" type="warn" size="mini" @click="pause">暂停</button>
			</view>
		</view>
		
	</view>
</template>

<script>
	export default{
		name:'countDown',
		data(){
			return{
				setMinute:0,
				changeValue:1,
				isCounting:false
			}
		},
		methods:{
			startCoundDown(){
				this.setMinute = this.changeValue
				this.$refs['countDown'].update();
				this.isCounting = !this.isCounting;
			},
			pickTime(){
				
			},
			stop(){
				this.setMinute = 0;
				this.isCounting = !this.isCounting;
			},
			pause(){
				this.isCounting = !this.isCounting;
			},
			timeup() {
				uni.showToast({
					title: '时间到'
				})
			}
		},
		watch:{
			changeValue:{
				deep:true,
				handler(nVal){
					this.setMinute = nVal;
				}
			}
		}
	}
</script>

<style>
	.a-center{text-align: center;}
	.mg-tb20{margin: 20px auto;}
	.button-flex {display: flex;justify-content: center;align-items: center;}
</style>