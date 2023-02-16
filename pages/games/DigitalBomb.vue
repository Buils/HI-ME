<template>
	<view class="desk">
		<uni-section title="请输入一个整数,注意引爆音量哦~" type="circle">
			<view class="bomb_desk">
				<view class="bombImage">
					<view v-show="myNumber != bombNumber">
						<image src="../../static/gamesImages/bomb_false.jpg" mode="aspectFill" class="bomb_image"></image>
					</view>
					<view v-show="myNumber == bombNumber">
						<image src="../../static/gamesImages/bomb_true.jpg" mode="aspectFill" class="bomb_image"></image>
					</view>
				</view>
				<view class="bomb_result" v-show="myNumber == bombNumber">
					<text class="result_text">游戏结束，引爆数为:{{bombNumber}}</text>
					<button type="warn" @click="restartGame()">重新开始</button>
				</view>
				<view class="bomb_numberList" v-show="myNumber != bombNumber">
					<ul>
						<li class="minNumber numberBox">{{minNumber}}</li>
						<li class="line">-</li>
						<li class="maxNumber numberBox">{{maxNumber}}</li>
					</ul>
				</view>
				<view class="bomb_numberInput" v-show="myNumber != bombNumber">
					<text>请在下方输入整数:<b>{{minNumber}}</b>到<b>{{maxNumber}}</b>之间</text>
					<view>
						<view class="mg-tb20">
							<slider 
								:value="numberValue" 
								@changing="sliderChange" 
								:min="minNumber+1" 
								:max="maxNumber-1" 
								 activeColor="#FFCC33" backgroundColor="#000000" block-color="#007aff" block-size="25" 
								show-value/>
						</view>
						<button class="sub_button" size="mini" type="primary" @click="setMyNumber">确定</button>
					</view>
				</view>
			</view>
		</uni-section>
	</view>
</template>

<script>
	var	innerAudioContext = uni.createInnerAudioContext();
		innerAudioContext.src = '../../static/audio/explore_audio.mp3';
	export default {
		name: 'DigitalBomb',
		data() {
			return {
				myNumber:0,
				bombNumber:50,
				minNumber:0,
				maxNumber:100,
				numberValue:50
			}
		},
		created() {
			this.setBombNumber();
		},
		methods:{
			sliderChange(e) {
				this.numberValue = e.detail.value;
			},
			setBombNumber(){
				this.bombNumber = parseInt(Math.random()*99+1);
			},
			setMyNumber() {
				this.myNumber = this.numberValue;
				if(this.myNumber > this.bombNumber){
					this.maxNumber = this.myNumber;
				}else{
					this.minNumber = this.myNumber;
				}
				this.numberValue = Math.floor((this.maxNumber + this.minNumber) / 2);
			},
			restartGame(){
				uni.redirectTo({
					url:'./DigitalBomb',
					success() {
						uni.showToast({
							title:'重置成功'
						})
					}
				})
			}
		},
		watch:{
			myNumber(nVal){
				if(this.myNumber == this.bombNumber){innerAudioContext.play();}
			}
		}
	}
</script>

<style scoped>
	.mg-tb20{margin: 20px auto;}
	.bomb_desk{position: absolute;left: 50%;top: 50%;transform: translate(-50%,-50%);width: 100%;text-align: center;}
	.bomb_image{width: 180px;height: 180px;}
	.bomb_numberList{}
	.bomb_numberList ul{display: flex;justify-content: center;align-items: center;margin:60px auto 30px auto;padding: 0;}
	.bomb_numberList ul li{list-style-type: none;width: 80px;height: 40px;line-height: 40px;}
	.bomb_numberList ul li.numberBox{background: #eee;}
	.bomb_numberInput text b{background: #ff0000;color: #fff;padding:5px;margin:0px 10px;}
	.sub_button{width: 100px;padding:10px 0px;}
	.bomb_result{width: 60%;margin:30px auto;background:#fff;padding:10px;border: 1px solid #ddd;text-align: center;box-shadow: 0px 0px 3px #000;}
	.bomb_result .result_text{margin-bottom: 20px;display: block;}
</style>
