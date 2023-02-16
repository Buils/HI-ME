<template>
	<view class="account">
		<view class="user_header">
			<image class="myHeader" src="../../static/logo.png" mode="aspectFit"></image>
			<view class="user_header_name">HI-ME</view>
			<view class="user_header_tag">Prod By Buils</view>
		</view>
		<view class="">
			<uni-section title="关于" type="line">
				<view class="uni-padding-wrap uni-common-mt">
					<uni-segmented-control :current="current" :values="items" :style-type="styleType"
						:active-color="activeColor" @clickItem="onClickItem" />
				</view>
				<view class="content">
					<view v-if="current === 0">
						<uni-card title="社交媒体" thumbnail="../../static/icon/socialMedia.png">
							<text>按钮设置开关，可将对应的社交媒体账号二维码保存到本地相册再上传，所有图片均保存在App内，可离线使用.每个分享二维码下方的按钮还能帮助你快速打开对应的App功能.</text>
						</uni-card>
						<uni-card title="游戏" thumbnail="../../static/icon/games.png">
							<text>与朋友分享的酒桌游戏，仅供娱乐，请勿进行赌博等任何违法行为，喝酒莫贪杯，微醺胜买醉.</text>
						</uni-card>
						<uni-card title="记录" thumbnail="../../static/icon/record.png">
							<text>设置任务，选择事件(纪念日，生日，未完成任务等)和提醒时间，你可以点击事件进行修改和删除。</text>
						</uni-card>
						<uni-card title="提示" thumbnail="../../static/icon/user.png">
							<text>所有存储均采用本地存储，如果你进行此App的清理数据，即相当于重置此App(清除缓存不受影响)</text>
						</uni-card>
					</view>
					<view v-if="current === 1">
						<uni-card title="分享" thumbnail="../../static/socialMediaIcon/link.png">
							<text>桌面长按此应用图标,点击分享,即可选择不同分享方式.</text>
						</uni-card>
						<uni-card title="QQ-面对面快传" thumbnail="../../static/socialMediaIcon/qq.png">
							<text>打开QQ,点击右上方+,点击面对面快传,选择此应用分享给好友.</text>
							<button class="block" type="primary" size="mini" @click="openQQ">OPEN QQ</button>
						</uni-card>
					</view>
					<view v-if="current === 2">
						<uni-card title="关于HI-ME" thumbnail="../../static/logo.png">
							<text class="block"><span>&bull;</span>应用仅作为娱乐分享用途.</text>
							<text class="block"><span>&bull;</span>如果出现问题,建议清除此应用缓存或清除数据。</text>
							<text class="block"><span>&bull;</span>更多问题请联系QQ:874568542</text>
						</uni-card>
					</view>
				</view>
			</uni-section>
		</view>
	</view>
</template>

<script>
	export default{
		name:'my',
		data(){
			return{
				current: 0,
				activeColor: '#007aff',
				styleType: 'button',
				items: ['教程','分享','更多'],
				optionList: [
					{
						url: '/static/images/c1.png',
						text: '打开QQ',
						funcName:'openQQ'
					},
				]
			}
		},
		methods:{
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex
				}
			},
			openQQ(){
				if (plus.os.name == 'Android') {
				    plus.runtime.launchApplication(  
				        {  
				            pname: 'com.tencent.mobileqq'  
				        },  
				        function(e) {  
							plus.nativeUI.toast("未发现QQ App");
				        }  
				    );  
				} else if (plus.os.name == 'iOS') {  
				    plus.runtime.launchApplication({ action: 'mqq://' }, function(e) {  
						plus.nativeUI.toast("未发现QQ App");
				    });  
				}
			}
		},

	}
</script>

<style>
	.user_header{text-align: center;}
	.user_header .user_header_name{font-size: 16px;padding: 5px;background: #007aff;color: #fff;margin:0px auto 10px auto;display: table;border-radius:5px;}
	.user_header .user_header_tag{font-size: 13px;}
	.myHeader{width: 100px;height: 100px;margin:20px auto;border-radius: 50%;box-shadow: 0px 0px 10px #000;}
	.block{display: block;width: 100%;}
</style>