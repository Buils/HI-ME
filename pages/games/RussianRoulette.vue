<template>
	<view>
		<view class="round_box">
			<view class="turntable-wrap" v-show="this.hitAreArr.length >= 2">
			    <view class="light" id="turntable_light">
					<span v-for="item in lightNum" 
					:style="{transform:'rotate('+(360 / lightNum)*item+'deg)'}"
					></span>
				</view>
			    <view class="turntable_box">
			        <ul class="bg">
						<view :animation="animationData" class="turntable">
							<view v-for='(item,index) in hitAreArr' :key='index'>
								<view class='sector' :style="'transform:rotate('+(index*(360/hitAreArr.length)-0.5*(360/hitAreArr.length))+'deg)'">
									 <view class='inner' :style="'transform:rotate('+(360/hitAreArr.length)+'deg);background-color:#ffffff'">
									 </view>
								 </view>
								<view :style="'transform:rotate('+index*(360/hitAreArr.length)+'deg) ;'" class="cell">{{item}}</view>
							</view>
						</view>
					</ul>
			    </view>
			    <view class="pointer disabled" id="turntable_pointer" @click="onStart">点击开始</view>
			</view>
		</view>
		<view>
			<uni-popup ref="resultDialog" type="dialog">
				<uni-popup-dialog type="info" cancelText="再来一次" title="转盘结果" :content="hitResult" @confirm="dialogConfirm" @close="onRestart"></uni-popup-dialog>
			</uni-popup>
		</view>

		<text class="notEnoughtTips" v-show="this.hitAreArr.length < 2">请输入两个或以上的结果即可开始游戏</text>
		<button class="button settingBtn" type="primary" size="mini" @click="showDrawer('showLeft')">设置</button>
		
		<uni-drawer ref="showLeft" mode="left" :width="380" :mask-click="false">
			<view class="closeDrawerBtn">
				<button class="button" size="mini" type="warn" @click="clearHitAre">清空</button>
				<button class="button ml5" size="mini" type="warn" @click="resetHitAre">重置</button>
				<button class="button ml5" size="mini" type="warn" @click="closeDrawer('showLeft')">关闭</button>
			</view>
			<view class="closeDrawerBtn">
				<view style="display: flex;justify-content: space-between;align-items: center;">
					<uni-easyinput v-model="myInput" @confirm="addHitAre" :maxlength="8" :styles="{color: '#000',borderColor: '#2979FF'}" placeholder="请输入内容(最多输入8个字)"></uni-easyinput>
					<button class="uni-button ml5" size="mini" type="primary" @click="addHitAre">确定</button>
				</view>
				<uni-table ref="table" border stripe emptyText="暂无更多数据" @selection-change="selectionChange">
					<uni-tr>
						<uni-th width="70" align="center">ID</uni-th>
						<uni-th width="180" align="center">内容</uni-th>
						<uni-th width="100" align="center">设置</uni-th>
					</uni-tr>
					<uni-tr v-for="(item, index) in hitAreArr" :key="index">
						<uni-td align="center">{{ hitAreArr.length - index }}</uni-td>
						<uni-td align="center">{{ item }}</uni-td>
						<uni-td align="center">
							<view class="uni-group">
								<button class="uni-button" size="mini" type="warn" @click="deleteHitAreItem(index)">删除</button>
							</view>
						</uni-td>
					</uni-tr>
				</uni-table>
			</view>
		</uni-drawer>
	</view>
</template>

<script>
	export default {
		name: 'RussianRoulette',
		data() {
			return {
				deg: 0, // 初始化角度
				duration:3000,//动画时长
				awardNumber: 3, // 中奖区域 从1开始
				isStart: false,//防止多次触发动画
				animationData: {},//动画对象
				// 
				myInput:'',
				hitResult:'',
				hitAreArr:['喝一杯','喝半杯','不用喝','左边的人喝','右边的人喝','选一个人喝'],
				lightNum:18
			}
		},
		onShow: function(){ //创建对象，并将对象存放在data中以挂载在需要进行动画的元素之上
			this.getHitAre();
			var animation = uni.createAnimation({
				duration: this.duration,
				timingFunction: 'ease-in-out', //旋转模式
			})
			this.animation = animation //挂载在vue实例上以便渲染
		},
		methods: {
			//封装动画旋转整数圈的方法，只需接受（角度，时间）即可开始旋转动画
			rotate: function (deg,duration) {
				if (this.isStart) return //防止用户多次点击动画
				this.isStart=true //此时旋转开始，动画无法再次触发
				setTimeout(function() { //设置定时器在动画时间后方可再次触发转盘动画
					this.isStart=false
				}.bind(this), duration)//此时用的普通函数，存在this指向问题，需要改变this指向
				this.animation.rotate(deg).step().rotate((this.awardNumber-1)*-(360/this.hitAreArr.length)).step({
					duration: 0,
					timingFunction: 'linear',
				})
				this.animationData = this.animation.export()
			},
			//开始旋转
			onStart(){
				if(this.isStart == true){uni.showToast({title:'转盘进行中'});return false;}
				this.awardNumber = parseInt(Math.random()*this.hitAreArr.length+1);
			   //提前将要转的角度算好，再传入要转的时长即可转到自己想要的角度
				this.rotate((this.duration/500)*360+(360-(this.awardNumber-1)*(360/this.hitAreArr.length)),this.duration);
				setTimeout(function(){
					this.$refs.resultDialog.open()
					this.hitResult = this.hitAreArr[this.awardNumber-1];
				}.bind(this),this.duration);
			},
			onRestart(){
				this.$refs.resultDialog.close()
				this.onStart()
			},
			getHitAre(){
				let that = this;
				uni.getStorage({
					key:'hitAre',
					success(res) {
						that.hitAreArr = res.data;
					},
					fail(err){
						console.log(err);
					}
				})
			},
			resetHitAre(){
				this.hitAreArr = ['喝一杯','喝半杯','不用喝','左边的人喝','右边的人喝','选一个人喝']
				uni.removeStorage({
					key:'hitAre',
					success(res) {
						uni.showToast({title:'重置成功'})
					}
				})
			},
			clearHitAre(){
				this.hitAreArr = [];
				uni.removeStorage({
					key:'hitAre',
					success(res) {
						uni.showToast({title:'列表已清空'})
					}
				})
			},
			dialogConfirm(){
				this.$refs.resultDialog.close()
			},
			showDrawer(e) {
				this.$refs[e].open()
			},
			// 关闭窗口
			closeDrawer(e) {
				this.$refs[e].close()
				this.setHitAre();
			},
			setHitAre(){
				uni.setStorage({
					key:'hitAre',
					data:this.hitAreArr
				})
			},
			addHitAre(){
				if(this.myInput.trim().length > 0){
					this.hitAreArr.unshift(this.myInput);
					this.myInput = '';
					uni.showToast({title:'添加成功'})
				}else{
					uni.showToast({title:'请输入内容'})
				}
			},
			deleteHitAreItem(index){
				this.hitAreArr.splice(index,1)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.notEnoughtTips{position: fixed;left: 50%;top: 50%;transform: translate(-50%,-50%);width: 95%;text-align: center;}
	.closeDrawerBtn{text-align: right;margin: 10px;}
	.ml5{margin-left: 5px;}
	.round_box{position: fixed;left: 50%;top: 50%;transform: translate(-50%,-50%);}
	.settingBtn{position: absolute;right: 10px;top: 10px;background: #b9a046;}
	.turntable-wrap {position: relative;overflow: hidden;margin: 30px auto;width: 360px;height: 360px;border: 7px solid #b2a98d;border-radius: 50%;box-shadow: 0 0 20px #b2a98d;}
	@keyframes white-to-yellow {0% {background: #fff;}100% { background: #d7a945;}}
	.turntable-wrap .light {position: absolute;top: 0;left: 0;width: 100%;height: 100%;background: #e0ddd1;animation: rotate 5s linear infinite;}
	.turntable-wrap .light span {position: absolute;top: 0;left: 0;right: 0;margin: 0 auto;width: 10px;height: 100%;transform-origin: center center;}
	.turntable-wrap .light span:before {content: '';position: absolute;top: 5px;left: 0;right: 0;margin: 0 auto;width: 10px;height: 10px;border-radius: 50%;}
	.turntable-wrap .light span:nth-of-type(even):before {background: #fff;animation: white-to-yellow 1s linear infinite;}
	.turntable-wrap .light span:nth-of-type(odd):before {background: #d7a945;animation: white-to-yellow 1s linear reverse infinite;}
	.turntable_box .bg {position: absolute;top: 50%;left: 50%;width: calc(100% - 45px);height: calc(100% - 45px);padding:0;background: #fff;border: 1px solid #dfd8be;border-radius: 50%;transform:translate(-50%,-50%);overflow: hidden;}
	.turntable-wrap .pointer {box-sizing: border-box;position: absolute;top: 50%;left: 0;right: 0;margin: -23px auto;width: 46px;height: 46px;border-radius: 50%;background: #fff;border: 5px solid #fff;box-shadow: 0 0 0 5px #b9a046;text-align: center;line-height: 16px;color: #b9a046;font-size: 14px;font-weight: 700;cursor: pointer;}
	.turntable-wrap .pointer:before {content: '';position: absolute;top: -58px;left: 0;right: 0;margin: 0 auto;width: 0;border-style: solid;border-color: transparent transparent #b9a046 transparent;border-width: 25px 10px 25px 10px;}
	.turntable {position: relative;margin: 0px auto;background:#b9a046;height:100%;width:100%;border-radius: 50%;text-align: center;overflow: hidden;}
	.cell{color:#b9a046;font-size: 12px;text-align: left;position: absolute;height: fit-content;top: 0;left: 50%;margin-left: -10px;padding-top: 10px;transform-origin:10px 157px;-webkit-writing-mode: vertical-lr;width: fit-content;height: 120px;white-space: nowrap;overflow: hidden;text-overflow: ellipsis;}
	.sector {width: 315px; height: 315px;position: absolute;clip: rect(0 315px 315px 157.5px);overflow: hidden;}
	.inner {width: 100%; height: 100%;position: absolute;clip: rect(0 157.5px 315px 0);transform: rotate(60deg);border-radius: 50%;}
</style>
