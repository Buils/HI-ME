<!-- 提醒，时间+事件 点击可修改内容，时间，删除 -->
<template>
	<view class="">
		<uni-section v-if="record.length > 0" title="正在倒计时..." type="circle">
			<uni-card mode="card" v-for="(item,index) in record" v-show="item.timePickers - new Date().getTime() > 0" :key="item.id" @click="showListItemPopup(index)" margin="5px" padding="5px">
				<uni-list-item :title="item.val" :rightText="item.timePickers | dayCounter"/>
				<view v-show="showSetting == index">
					<!-- <text>{{item.timePickers}}</text> -->
					<view class="mysettinggroup">
						<button :plain="true" size="mini" type="warn" @click="deleteItem(item)">删除</button>
						<button :plain="true" size="mini" type="primary" @click="modifyItem(item)">修改</button>
						<button :plain="true" size="mini" type="default" @click.stop.prevent="closeItemSetting()">关闭</button>
					</view>	
				</view>	
			</uni-card>
			<uni-section title="已经结束啦~" type="line">
				<uni-card mode="card" v-for="(item,index) in record" v-show="item.timePickers - new Date().getTime() <= 0" :key="item.id" @click="showListItemPopup(index)" margin="5px" padding="5px">
					<uni-list-item :title="item.val" :rightText="item.timePickers | dayCounter"/>
					<view v-show="showSetting == index">
						<view class="mysettinggroup">
							<button :plain="true" size="mini" type="warn" @click="deleteItem(item)">删除</button>
							<button :plain="true" size="mini" type="primary" @click="modifyItem(item)">修改</button>
							<button :plain="true" size="mini" type="default" @click.stop.prevent="closeItemSetting()">关闭</button>
						</view>	
					</view>	
				</uni-card>
			</uni-section>
		</uni-section>
		<uni-card v-else>暂无数据</uni-card>
		<!-- 输入内容盒子 -->
		<view class="">
			<uni-popup ref="infoSet" type="dialog">
				<uni-popup-dialog mode="input" title="事件设置" placeholder="请输入你想记录的内容"  @close="popUpCancel()" @confirm="settingConfirm" :before-close="true"></uni-popup-dialog>
				<uni-datetime-picker v-model="singleDateTime" returnType="timestamp"/>
			</uni-popup>
		</view>
		<!-- 修改内容盒子 -->
		<view class="">
			<uni-popup ref="modifySet" type="dialog">
				<uni-popup-dialog @close="popUpCancel()" @confirm="modifyConfirm()" type="warning">
					<uni-search-bar v-model="modifyInput" radius="5" placeholder="请输入你想修改的内容" clearButton="auto" cancelButton="none" :focus="true" />
				</uni-popup-dialog>
				<uni-datetime-picker v-model="modifyDateTime"/>
			</uni-popup>
		</view>
		<view class="">
			<uni-fab ref="recordSetting" :content="selectOptions" horizontal="left" vertical="bottom" @trigger="showSettingPopup($event)"></uni-fab>
		</view>
	</view>
</template>


<script>
	import {nanoid} from 'nanoid';
    export default {
        data() {
            return {
				singleDateTime:'',
				modifyDateTime:'',
				modifyInput:'',
				modifyId:'',
				showSetting:-1,
				record:[],
				selectOptions:[
					{
						iconPath: '/static/icon/record.png',
						selectedIconPath: '/static/icon/record_active.png',
						text: '倒计时',
						title:'uploadInfo',
						active: false
					},
					{
						iconPath: '/static/icon/record.png',
						selectedIconPath: '/static/icon/record_active.png',
						text: '清空列表',
						title:'clearList',
						active: false
					},
					{
						iconPath: '/static/icon/record.png',
						selectedIconPath: '/static/icon/record_active.png',
						text: '排序',
						title:'sortList',
						active: false
					}
				]
            }
        },
		created() {
			this.getLocalRecord();
			this.getTime();
		},
		methods:{
			getLocalRecord(){
				uni.getStorage({
					key:'record',
					success:(res)=>{
						this.record = JSON.parse(res.data);
					},
					fail:(fail)=>{
						this.record = [];
						console.log('fail',fail)
					}
				})
			},
			closeItemSetting(){
				this.showSetting = -1;
			},
			sortRecordList(){
				if(this.record != ""){
					this.record.sort(function(a,b){
						if(a.timePickers>b.timePickers){
							return b.timePickers - a.timePickers;
						}else if(a.timePickers<b.timePickers){
							return a.timePickers - b.timePickers;
						}
					})
					uni.showToast({
						title: '已排序'
					});
				}else{
					uni.showToast({
						title: '请先添加倒计时'
					});
				}
			},
			sortRecordListUp(){
				// 升序，时间最近的放最前面
				this.record.sort(function(a,b){
					return a.timePickers - b.timePickers;
				})
			},
			modifyItem(info){
				this.$refs.modifySet.open();
				this.modifyInput = info.val;
				this.modifyDateTime = info.timePickers;
				this.modifyId = info.id
			},
			modifyConfirm(){
				let _this = this;
				_this.record.filter((t)=>{
					if(t.id == _this.modifyId){
						t.val = _this.modifyInput;
						t.timePickers = getDate(_this.modifyDateTime)
						uni.showToast({
							title: '修改成功'
						});
						_this.showSetting = -1;
						this.sortRecordListUp();
					}
				})
			},
			deleteItem(info){
				let _this = this;
				uni.showModal({
					title: '删除',
					content: '确认删除吗?(不可恢复)',
					success: function (res) {
						if (res.confirm) {
							_this.record = _this.record.filter((t)=>{
								return t.id != info.id
							})
							uni.removeStorage({
								key:'record',
								success() {
									uni.showToast({
										title: '删除成功'
									});
								},
								fail(err) {
									uni.showToast({
										title:'删除失败'
									})
								}
							})
						}
						_this.showSetting = -1;
					}
				});
			},
			getTime(){
				// 默认获取明天
				let date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth()+1;
				let day = date.getDate();
				let hour = date.getHours()+1;
				this.singleDateTime = `${year}-${month > 10 ? month:'0'+month}-${day > 10 ? day:'0'+day} ${hour}:00:00`
			},
			settingConfirm(value){
				if(value != "" && this.singleDateTime != ""){
					let recordItem = {id:nanoid(),val:value,timePickers:getDate(this.singleDateTime).getTime()};
					this.record.unshift(recordItem);
					this.$refs.infoSet.close();
					this.$refs.recordSetting.close();
					this.sortRecordListUp();
					uni.showToast({
						title: '添加成功'
					});
				}else{
					uni.showToast({
						title: '请正确输入内容,不能为空'
					});
				}
			},
			settingCancel(){
				this.$refs.infoSet.close();
			},
			showSettingPopup(e){
				switch (e.item.title){
					case 'uploadInfo':
						this.$refs.infoSet.open();
						break;
					case 'clearList':
						this.clearInfoConfirm();
						break;
					case 'sortList':
						this.sortRecordList();
						break;
					default:
						break;
				}
				this.showSetting = -1;
			},
			showListItemPopup(index){
				this.showSetting = index;
			},
			clearInfoConfirm(){
				let _this = this;
				uni.showModal({
					title: '清空列表',
					content: '确认清空列表吗?(不可恢复)',
					success: function (res) {
						if (res.confirm) {
							uni.removeStorage({
								key:'record',
								success() {
									uni.showToast({
										title:'列表已清空'
									})
								},
								fail(err) {
									uni.showToast({
										title:'请勿重复清空列表'
									})
								}
							});
							_this.record = [];
							_this.$refs.recordSetting.close();
						}
					}
				});
			},
			popUpCancel(){
				this.$refs.recordSetting.close();
				this.$refs.infoSet.close();
				this.showSetting = -1;
			},
			beginTimer(){
				setInterval(()=>{
					for (var i=0;i<this.record.length;i++) {
						const timeStamp = getDate(this.record[i].timePickers).getTime();
						let remainingTime = timeStamp - new Date().getTime();
						if(remainingTime > 0){
							this.record[i].timePickers = timeStamp  - 1;
						}
					}
				},1000)
			}
		},
		mounted() {
			this.beginTimer();
		},
		beforeDestroy() {
			this.beginTimer();
		},
		watch:{
			record:{
				deep:true,
				handler(val){
					uni.setStorage({
						key:'record',
						data:JSON.stringify(val)
					})
				}
			}
		},
		filters:{
			dayCounter(settingDay){
				var nowtime = getDate().getTime();
				var endtime = getDate(settingDay).getTime();
				var lefttime = endtime - nowtime;
				var leftd = Math.floor(lefttime/(1000*60*60*24));  //计算天数
				var lefth = Math.floor(lefttime/(1000*60*60)%24)<10 ? '0'+ Math.floor(lefttime/(1000*60*60)%24): Math.floor(lefttime/(1000*60*60)%24);  //计算小时数
				var leftm = Math.floor(lefttime/(1000*60)%60)<10 ? '0'+Math.floor(lefttime/(1000*60)%60):Math.floor(lefttime/(1000*60)%60);  //计算分钟数
				var lefts = Math.floor(lefttime/1000%60)<10?'0'+Math.floor(lefttime/1000%60):Math.floor(lefttime/1000%60);  //计算秒数
				
				if(lefttime > 0){
				  if(leftd > 0){
				    return leftd + "天" + lefth + "时" + leftm + "分" + lefts + "秒";
				  }else{
				    return lefth + "时" + leftm + "分" + lefts + "秒";
				  }
				}else{
				  return "已结束";
				}
			}
		}
    }
</script>

<style>
	.uni-popup-dialog{border-radius: 11px 11px 0px 0px;}
	.uni-date {background-color: #fff;}
	.mysettinggroup{display: flex;justify-content: space-around;align-items: center;}
	.mysettinggroup uni-button{width: 32%;margin-top: 10rpx;}
</style>