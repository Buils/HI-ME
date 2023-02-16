<template>
	<view class="">
		<view class="">
			<view class="cardDesk opponentDesk">
				<view class="resetBtn">
					<button type="warn" size="mini" @click="resetGuessing" v-show="gameStart">RESET</button>
				</view>
				<view class="desk">
					<view class="guessingResult" :class="opponentGuessingResult" v-show="gameStart && opponentGuessingStart == false && myGuessingStart == false">{{opponentGuessingResult}}</view>
					<view class="guessingCard">{{guessingFingerList[opponentGuessingCode].type}}</view>
					<view class="button-group">
						<button class="goGuessingBtn" size="mini" v-show="!opponentGuessingStart" type="primary" @click="startGuessing">开始</button>
						<button class="goGuessingBtn" size="mini" v-show="opponentGuessingStart" type="warn" @click="opponentStopGuessing();bothStopGuessing();">暂停</button>
					</view>
					<scroll-view scroll-x="true" scroll-with-animation="true" scroll-anchoring="true" :scroll-left="opponentResultScrollLeft" class="result-group">
						<view class="result-group-item" v-for="(item,index) in opponentGuessingResultArr" :class="item">{{item}}</view>
					</scroll-view>
					<view class="resultCount">
						<ul>
							<li class="win">WIN:<b>{{opponentResult.win}}</b></li>
							<li class="draw">DRAW:<b>{{opponentResult.draw}}</b></li>
							<li class="lose">LOSE:<b>{{opponentResult.lose}}</b></li>
						</ul>
					</view>
				</view>
			</view>
			
			<view class="cardDesk myDesk">
				<view class="resetBtn">
					<button type="warn" size="mini" @click="resetGuessing" v-show="gameStart">RESET</button>
				</view>
				<view class="desk">
					<view class="guessingResult":class="myGuessingResult" v-show="gameStart && opponentGuessingStart == false && myGuessingStart == false ">{{myGuessingResult}}</view>
					<view class="guessingCard">{{guessingFingerList[myGuessingCode].type}}</view>
					<view class="button-group">
						<button class="goGuessingBtn" size="mini" v-show="!myGuessingStart" type="primary" @click="startGuessing">开始</button>
						<button class="goGuessingBtn" size="mini" v-show="myGuessingStart" type="warn" @click="stopGuessing();bothStopGuessing();">暂停</button>
					</view>
					<scroll-view scroll-x="true" scroll-with-animation="true" scroll-anchoring="true" :scroll-left="myResultScrollLeft" class="result-group">
						<view class="result-group-item" v-for="(item,index) in myGuessingResultArr" :class="item">{{item}}</view>
					</scroll-view>
					<view class="resultCount">
						<ul>
							<li class="win">WIN:<b>{{myResult.win}}</b></li>
							<li class="draw">DRAW:<b>{{myResult.draw}}</b></li>
							<li class="lose">LOSE:<b>{{myResult.lose}}</b></li>
						</ul>
					</view>
				</view>
			</view>
			
			<uni-popup ref="resetDialog" type="dialog">
				<uni-popup-dialog ref="inputClose" title="确定重置该局游戏吗?" @confirm="resetDialogConfirm" @close="closeResetDialogConfirm"></uni-popup-dialog>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'FingerGuessing',
		data() {
			return {
				gameStart:false,
				myGuessingCode:1,
				myGuessingStart:false,
				myResultScrollLeft:100,
				myGuessingTimer:null,
				myGuessingResultArr:[],
				opponentGuessingCode:1,
				opponentGuessingStart:false,
				opponentResultScrollLeft:100,
				opponentGuessingTimer:null,
				opponentGuessingResultArr:[],
				guessingFingerList:[
					{
						type:'石头',
						typeCode:0,
						imgUrl:'../../static/gamesIcon/dice.jpeg'
					},
					{
						type:'剪刀',
						typeCode:1,
						imgUrl:'../../static/gamesIcon/dice.jpeg'
					},
					{
						type:'布',
						typeCode:2,
						imgUrl:'../../static/gamesIcon/dice.jpeg'
					}
					
				],
			}
		},
		methods:{
			startGuessing(){
				this.gameStart = true;
				this.myGuessingStart = true;
				this.opponentGuessingStart = true;
				clearInterval(this.myGuessingTimer);
				this.myGuessingTimer = setInterval(()=>{
					this.myGuessingCode = Math.floor(Math.random()*3);
				},50)
				clearInterval(this.opponentGuessingTimer);
				this.opponentGuessingTimer = setInterval(()=>{
					this.opponentGuessingCode = Math.floor(Math.random()*3);
				},50)
			},
			stopGuessing(){
				this.myGuessingStart = false;
				clearInterval(this.myGuessingTimer);
			},
			opponentStopGuessing(){
				this.opponentGuessingStart = false;
				clearInterval(this.opponentGuessingTimer);
			},
			bothStopGuessing(){
				if(this.myGuessingStart == false && this.opponentGuessingStart == false){
					this.myGuessingResultArr.push(this.myGuessingResult);
					this.opponentGuessingResultArr.push(this.opponentGuessingResult);
				}
				this.$nextTick(function() {
					this.myResultScrollLeft = this.myGuessingResultArr.length * 60;
					this.opponentResultScrollLeft = this.opponentGuessingResultArr.length * 60;
				});
			},
			resetGuessing(){
				this.$refs.resetDialog.open()
			},
			closeResetDialogConfirm(){
				this.$refs.resetDialog.close()
			},
			resetDialogConfirm(){
				uni.redirectTo({
					url:'./FingerGuessing',
					success() {
						uni.showToast({
							title:'重置成功'
						})
					}
				})
			}
		},
		computed:{
			myGuessingResult(){
				if(this.myGuessingCode - this.opponentGuessingCode == -1 || this.myGuessingCode - this.opponentGuessingCode == 2){
					return 'win';
				}else if(this.myGuessingCode == this.opponentGuessingCode){
					return 'draw';
				}else{
					return 'lose';
				}
			},
			opponentGuessingResult(){
				if(this.opponentGuessingCode - this.myGuessingCode == -1 || this.opponentGuessingCode - this.myGuessingCode == 2){
					return 'win';
				}else if(this.opponentGuessingCode == this.myGuessingCode){
					return 'draw';
				}else{
					return 'lose';
				}
			},
			myResult(){
				let obj = {win:0,draw:0,lose:0};
				for(var i= 0, l = this.myGuessingResultArr.length; i< l; i++){ 
				  var item = this.myGuessingResultArr[i]; 
				  obj[item] = (obj[item] +1 ) || 1; 
				} 
				return obj; 
			},
			opponentResult(){
				let obj = {win:0,draw:0,lose:0};
				for(var i= 0, l = this.opponentGuessingResultArr.length; i< l; i++){ 
				  var item = this.opponentGuessingResultArr[i]; 
				  obj[item] = (obj[item] +1 ) || 1; 
				} 
				return obj; 
			}
		}
	}
</script>

<style scoped>
	.goGuessingBtn{padding: 20px 30px;}
	.button-group{width: 50%;margin:15px auto;text-align: center}
	.guessingCard{width: 120px;height: 80px;line-height: 80px;border: 2px solid #000;text-align: center;margin: 0 auto;}
	.guessingResult{position: absolute;right: 10px;top: 10px;width: 45px;height: 45px;line-height:45px;text-align:center;border-radius:50%;font-size: 14px;font-weight: bold;}
	.win{color: gold;border:2px solid gold;}
	.draw{color: #403838;border:2px solid #403838;}
	.lose{color: red;border:2px solid red;}
	.opponentGuessingResult{}
	.cardDesk{position: absolute;top:50%;left:0;width: 100%;height: 50%;overflow:auto;}
	.cardDesk::-webkit-scrollbar{display: block;width: 5px;}
	.cardDesk::-webkit-scrollbar-track{background: rgba(0, 0, 0, 0.3);}
	.cardDesk::-webkit-scrollbar-thumb{background: #000;}
	.desk{margin-top: 50px;}
	.myDesk{background: dodgerblue;}
	.opponentDesk{transform: rotateZ(180deg);transform-origin:top center;background: #dd2424;}
	.result-group{white-space: nowrap;width: 70%;height:48px;line-height:48px;overflow-anchor:auto;margin: 5px auto;}
	.result-group .uni-scroll-view{overflow-anchor:auto;}
	.result-group-item{display: inline-block;width: 40px;height: 40px;line-height:40px;text-align:center;border-radius:50%;font-size: 14px;font-weight: bold;margin: 0px 3px;}
	.resultCount{}
	.resultCount ul{display: flex;justify-content: space-around;align-items: center;margin: 10px auto;padding: 0;}
	.resultCount ul li{list-style-type: none;padding: 5px;background: #fff;font-size: 13px;}
	.resetBtn{position: absolute;left: 50%;z-index: 2;transform: translateX(-50%);}
</style>
