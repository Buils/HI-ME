<template>
	<view>
		<uni-section title="给我点~" type="circle">
			<view class="croco">
				<view class="croco_head">
					<view v-show="isTrap == false">
						<image src="../../static/gamesImages/croco_hi.jpg" mode="aspectFill" class="croco_image"></image>
					</view>
					<view :class="{'traped':isTrap == true}" v-show="isTrap == true">
						Game Over
					</view>
				</view>
				<view class="toothTagList">
					<view 
						class="toothTag"
						v-for="(item,index) in toothArray"
						:key="item.id"
						@click="dile(item.id)"
						:class="{
							'removeTooth':item.isActive == true,
							'trap':isTrap == true && item.id == trapIndex
						}"
					>{{item.toothName}}</view>
				</view>
			</view>
			
			<view class="croco_result_mask" v-show="isTrap == true"></view>
			<view class="croco_result" v-show="isTrap == true">
				<image src="../../static/gamesImages/croco_hi.jpg" mode="aspectFill" class="croco_image"></image>
				<text class="result_text">你被鳄鱼咬了一口，游戏结束，坏牙下标为:{{trapIndex}}</text>
				<button type="warn" @click="restartGame()">重新开始</button>
			</view>
			
		</uni-section>
	</view>
</template>

<script>
	export default {
		name: 'CrocoDile',
		data() {
			return {
				toothArray:[
					{id:1,toothName:'牙齿',isActive:false},
					{id:2,toothName:'牙齿',isActive:false},
					{id:3,toothName:'牙齿',isActive:false},
					{id:4,toothName:'牙齿',isActive:false},
					{id:5,toothName:'牙齿',isActive:false},
					{id:6,toothName:'牙齿',isActive:false},
					{id:7,toothName:'牙齿',isActive:false},
					{id:8,toothName:'牙齿',isActive:false},
					{id:9,toothName:'牙齿',isActive:false},
					{id:10,toothName:'牙齿',isActive:false},
					{id:11,toothName:'牙齿',isActive:false},
					{id:12,toothName:'牙齿',isActive:false},
				],
				trapIndex:4,
				isTrap:false
			}
		},
		created() {
			this.setTrap();
		},
		methods:{
			setTrap(){
				this.trapIndex = Math.floor(Math.random()*this.toothArray.length+1);
			},
			dile(id){
				if(id == this.trapIndex){
					this.isTrap = true;
				}else{
					this.toothArray[id-1].isActive = true;
				}
			},
			restartGame(){
				uni.redirectTo({
					url:'./CrocoDile',
					success() {
						uni.showToast({
							title:'重置成功'
						})
					}
				})
			}
		},
		
	}
</script>

<style scoped>
	.trap{background: #ff0000;}
	.traped{background: #ff0000;}
	.croco{width: 80%;height: auto;margin: 20px auto;display: flex;flex-direction: column;justify-content: space-between;align-items: center;}
	.croco .croco_head{width: 130px;height: 130px;line-height:130px;text-align:center;border: 1px solid #000;}
	.croco_image{width: 130px;height: 130px;}
	.toothTagList{display: flex;justify-content:space-around;flex-wrap:wrap;flex-direction:row;margin-top:20px;}
	.toothTag{flex: 0 0 25%;width: 60px;height: 60px;line-height: 60px;margin:0px 5px 10px 5px;border: 1px solid #000;border-radius:0px 0px 30px 30px;text-align:center;box-shadow:0px 5px 5px rgba(0,0,0,0.5);display: inline-block;}
	.removeTooth{border-color: transparent;background: lightgreen;}
	.croco_result_mask{position: absolute;z-index: 9;left:0%;bottom:0px;top:0;right:0;background:rgba(0,0,0,0.5);}
	.croco_result{position: absolute;z-index: 10;left:50%;bottom:10px;transform:translateX(-50%);width: 80%;background:#fff;padding:10px;border: 1px solid #ddd;text-align: center;box-shadow: 0px 0px 3px #000;}
	.croco_result .result_text{margin:20px 0px;display: block;}
</style>
