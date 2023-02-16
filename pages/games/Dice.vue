<template>
	<view>
		<uni-section title="摇个骰子,注意音量哦" type="square">
			<view class="diceDesk" v-show="showRandomDice">
				<text v-for="item in diceNumArray" class="numberCard">{{item}}</text>
			</view>
			<view class="diceDesk" v-show="!showRandomDice">
				<text>骰子隐藏中,请开始你的表演...</text>
			</view>
			<uni-popup ref="inputDialog" type="dialog">
				<uni-popup-dialog ref="inputClose" title="请选择骰子数量" @close="dialogInputClose" @confirm="dialogInputConfirm">
					<uni-number-box v-model="vModelValue" :min="1" :max="10"></uni-number-box>
					<view class="mg-l20"><checkbox :checked="isSetAudio" @click="isSetAudio = !isSetAudio"/><text>开启音效</text></view>
				</uni-popup-dialog>
			</uni-popup>
			<view v-show="isRandom == false">
				<button class="w50" type="primary" @click="startRandomDice">开始摇一摇</button>
				<button class="w50" type="default" @click="inputDialogToggle">设置</button>
			</view>
			<view v-show="isRandom == true">
				<button class="w50" v-show="!isRestart" type="warn" @click="stopRandomDice">停!</button>
			</view>
			<view v-show="isRandom == true && isRestart">
				<button class="mg-tb20 toggleRandomDiceBtn" v-show="!showRandomDice" type="primary" @click="showRandomDice = true">显示</button>
				<button class="mg-tb20 toggleRandomDiceBtn" v-show="showRandomDice" type="warn" @click="showRandomDice = false">隐藏</button>
				<button class="w50" v-show="isRestart && showRandomDice" type="primary" @click="restartRandomDice">重摇</button>
				<button class="w50" v-show="isRestart && showRandomDice" type="default" @click="resetRandomDice">重置</button>
			</view>
		</uni-section>
	</view>
</template>

<script>
	var	innerAudioContext = uni.createInnerAudioContext();
		innerAudioContext.loop = true;
		innerAudioContext.src = '../../static/audio/dice_audio.mp3';
	export default {
		name: 'Dice',
		data() {
			return {
				isSetAudio:true,
				isRandom:false,
				isRestart:false,
				showRandomDice:true,
				diceNumber:5,
				vModelValue:5,
				diceNumArray:[1,1,1,1,1],
				SHAKE_THRESHOLD: 4000,//速度要大于4000，证明用户是在快速摇手机
				lastUpdate: 0,
				x: 0,
				y: 0,
				z: 0,
				lastX: 0,
				lastY: 0,
				lastZ: 0
			}
		},
		created() {
			if (window.DeviceMotionEvent) {
			  window.addEventListener("devicemotion", this.deviceMotionHandler, false);
			}else{
			    alert("您的浏览器不支持DeviceOrientation");
			}
		},
		methods:{
			deviceMotionHandler(eventData) {
			  let acceleration = eventData.accelerationIncludingGravity;
			  let curTime = new Date().getTime();// 当前时间戳
			  if (curTime - this.lastUpdate > 100) {//手机抖动的时间要大于100ms,防止用户拿手机时，突然抖动而触发摇一摇功能
			    let diffTime = curTime - this.lastUpdate;
			    this.lastUpdate = curTime;
			    this.x = acceleration.x;// 表示x轴（西到东）上的加速度
			    this.y = acceleration.y;// 表示y轴（南到北）上的加速度
			    this.z = acceleration.z;// 表示z轴（下到上）上的加速度
			    let speed =
			      (Math.abs(
			        this.x + this.y + this.z - this.lastX - this.lastY - this.lastZ
			      ) /
			        diffTime) *
			      10000;
			    if (speed > this.SHAKE_THRESHOLD) {//速度要大于4000，证明用户是在快速摇手机
			       window.removeEventListener(//移除运动事件监听
			        "devicemotion",
			        this.deviceMotionHandler,
			        false
			      );
			      this.startRandomDice();// 摇一摇要做的事情
			    }else{
					this.stopRandomDice();
				}
			    this.lastX = this.x;
			    this.lastY = this.y;
			    this.lastZ = this.z;
			  }
			},
			startRandomDice(){
				this.timer = setInterval(()=>{
					this.getDice()
				},50)
				this.isRandom = true;
				if(this.isSetAudio){innerAudioContext.play();}
			},
			restartRandomDice(){
				clearInterval(this.timer);
				this.timer = setInterval(()=>{
					this.getDice()
				},50)
				this.isRestart = false;
				if(this.isSetAudio){innerAudioContext.play();}
			},
			resetRandomDice(){
				clearInterval(this.timer);
				this.isRandom = false;
				this.isRestart = false;
				this.diceNumArray = [];
				for (var i = 0; i < this.diceNumber; i++) {
					this.diceNumArray.push(1)
				}
				if(this.isSetAudio){innerAudioContext.stop();}
			},
			stopRandomDice(){
				clearInterval(this.timer);
				this.isRestart = true;
				if(this.isSetAudio){innerAudioContext.stop();}
			},
			getDice(){
				this.diceNumArray = [];
				for (var i = 0; i < this.diceNumber; i++) {
					this.diceNumArray.push(parseInt(Math.random()*5)+1)
				}
				this.isRestart = false;
			},
			inputDialogToggle() {
				this.$refs.inputDialog.open()
			},
			dialogInputConfirm() {
				this.diceNumber = this.vModelValue;
				// 关闭窗口后，恢复默认内容
				this.$refs.inputDialog.close()
			},
			dialogInputClose(){
				this.vModelValue = this.diceNumber;
			}
		},
		beforeDestroy() {
			innerAudioContext.destroy();
		}
	}
</script>

<style>
	.w50{width: 50%;margin: 5px auto;}
	.mg-l20{margin-left:20px;}
	.mg-tb20{margin:20px auto;}
	.toggleRandomDiceBtn{width: 100px;height: 100px;line-height: 100px;box-shadow: 1px 1px 5px rgba(0,0,0,0.7);}
	.diceDesk{display: flex;justify-content: center;flex-wrap:wrap;align-items: center;margin:30px auto;}
	.numberCard{width: 60px;height: 60px;line-height: 60px;text-align: center;font-size: 20px;font-weight: bold;border: 1px solid #000;display: inline-block;margin: 0px 5px 10px 5px;}
</style>
