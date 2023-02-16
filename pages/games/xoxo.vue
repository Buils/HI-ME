<template>
	<view>
		<uni-section title="连线开始" type="square">
			<view class="whoTerm" v-show="winner == '' ">
				<view v-show="startInput">现在是:<b class="otrueColor">O</b>的回合</view>
				<view v-show="!startInput">现在是:<b class="xfalseColor">X</b>的回合</view>
			</view>
			<view class="xoxoDesk">
				<view 
					v-for="(item,index) in xoxoArr" 
					class="xxooInputTag" 
					@click="playerClick(index)"
					:class="{
						'winnerColor':winnerIndex[0] == index || winnerIndex[1] == index || winnerIndex[2] == index,
						'clickFalse':clickFalse == true,
						'xfalseColor':item == 'X',
						'otrueColor':item == 'O',
					}"
				>{{item}}</view>
			</view>
			<view class="result">
				<view class="" v-show="winner == 'X' ">获胜者是:<b class="xfalseColor">{{winner}}</b></view>
				<view class="" v-show="winner == 'O' ">获胜者是:<b class="otrueColor">{{winner}}</b></view>
				<view class="" v-show="winner == 'noWinner' ">本轮游戏无获胜者产生,请点击再来一次重新开始.</view>
				<view class="" v-show="winnerIndex != '' ">获胜下标为:<b class="winnerColor">{{winnerIndex}}</b></view>
			</view>
			<view class="button_group">
				<button class="button reloadBtn" type="primary" size="mini" @click="reloadDesk" v-show="winner != '' ">再来一次</button>
			</view>
			<view class="button_flex">
				<button class="button" type="primary" size="mini">X的获胜次数:{{winnerX}}</button>
				<button class="button" type="warn" size="mini">O的获胜次数:{{winnerO}}</button>
			</view>
			<view class="button_flex">
				<button class="button reloadBtn" type="primary" size="mini" @click="startInput = !startInput">切换回合</button>
				<button class="button resetBtn" type="warn" size="mini" @click="reset">重置</button>
			</view>
		</uni-section>
	</view>
</template>

<script>
	export default {
		name: 'XOXO',
		data() {
			return {
				xoxoArr:['','','','','','','','',''],
				startInput:true,
				winnerIndex:[],
				winner:'',
				clickFalse:false,
				winnerX:0,
				winnerO:0
			}
		},
		methods:{
			playerClick(index){
				if(this.xoxoArr[index] == ''){
					if(this.startInput){
						this.$set(this.xoxoArr,index,'O')
					}else{
						this.$set(this.xoxoArr,index,'X')
					}
				}
				this.startInput = !this.startInput;
			},
			reloadDesk(){
				if(this.winner == 'X'){
					this.startInput = true;
				}else{
					this.startInput = false;
				}
				this.xoxoArr = ['','','','','','','','',''];
				this.winnerIndex = [];
				this.winner = '';
				this.clickFalse = false;
			},
			reset(){
				uni.redirectTo({
					url:'./xoxo',
					success() {
						uni.showToast({
							title:'重置成功'
						})
					}
				})
			}
		},
		watch:{
			xoxoArr:{
				deep:true,
				handler:function(nVal){
					if(nVal[0] == nVal[1] && nVal[1] == nVal[2] && nVal[0] !='' && nVal[1] !='' && nVal[2] !=''){
						this.winnerIndex = [0,1,2];
						this.winner = nVal[0];
						this.clickFalse = true;
					}
					if(nVal[3] == nVal[4] && nVal[4] == nVal[5] && nVal[3] !='' && nVal[4] !='' && nVal[5] !=''){
						this.winnerIndex = [3,4,5];
						this.winner = nVal[3];
						this.clickFalse = true;
					}
					if(nVal[6] == nVal[7] && nVal[7] == nVal[8] && nVal[6] !='' && nVal[7] !='' && nVal[8] !=''){
						this.winnerIndex = [6,7,8];
						this.winner = nVal[6];
						this.clickFalse = true;
					}
					if(nVal[0] == nVal[3] && nVal[3] == nVal[6] && nVal[0] !='' && nVal[3] !='' && nVal[6] !=''){
						this.winnerIndex = [0,3,6];
						this.winner = nVal[0];
						this.clickFalse = true;
					}
					if(nVal[1] == nVal[4] && nVal[4] == nVal[7] && nVal[1] !='' && nVal[4] !='' && nVal[7] !=''){
						this.winnerIndex = [1,4,7];
						this.winner = nVal[1];
						this.clickFalse = true;
					}
					if(nVal[2] == nVal[5] && nVal[5] == nVal[8] && nVal[2] !='' && nVal[5] !='' && nVal[8] !=''){
						this.winnerIndex = [2,5,8];
						this.winner = nVal[2];
						this.clickFalse = true;
					}
					if(nVal[0] == nVal[4] && nVal[4] == nVal[8] && nVal[0] !='' && nVal[4] !='' && nVal[8] !=''){
						this.winnerIndex = [0,4,8];
						this.winner = nVal[0];
						this.clickFalse = true;
					}
					if(nVal[2] == nVal[4] && nVal[4] == nVal[6] && nVal[2] !='' && nVal[4] !='' && nVal[6] !=''){
						this.winnerIndex = [2,4,6];
						this.winner = nVal[2];
						this.clickFalse = true;
					}
					if(nVal.indexOf('') == -1 && this.winnerIndex == ''){
						this.clickFalse = true;
						this.winner = 'noWinner';
					}
					
				}
			},
			winner:{
				deep:true,
				handler:function(nVal){
					if(nVal == 'X'){
						this.winnerX++;
					}else if(nVal == 'O'){
						this.winnerO++;
					}else{
						return false;
					}
				}
			}
		}
	}
</script>

<style scoped>
	.whoTerm{margin:10px auto;text-align: center;}
	.whoTerm b{margin:0px 5px;font-size: 20px;}
	.button_group{margin:20px auto;display: table;}
	.button_group button{margin-top: 10px;display: block;height: 50px;line-height:50px;}
	.button_flex{display: flex;justify-content: space-around;align-items: center;}
	.button_flex button{margin-top: 10px;display: block;min-width:122px;height: 40px;line-height:40px;}
	.xoxoDesk{display: flex;justify-content:center;flex-wrap:wrap;flex-direction:row;margin-top:20px;}
	.xxooInputTag{flex: 0 0 30%;height:120px;line-height: 120px;border: 1px solid #000;text-align:center;display: inline-block;font-size: 50px;font-weight: bold;}
	.xfalseColor{color: #007aff;}
	.otrueColor{color: #FF0000;}
	.clickFalse{pointer-events: none;background: rgba(0, 0, 0, 0.5);color: #000;}
	.winnerColor{color: #ffd700;}
	.result{margin:20px auto;text-align: center;}
	.result b{margin-left: 10px;font-size: 20px;}
</style>
