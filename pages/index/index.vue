<template>
	<view>
		<uni-section :title="myTag" type="line">
			<uni-collapse accordion>
				<uni-collapse-item 
					v-for="(item,index) in socialMedia" 
					:title="item.type"
					:thumb="item.thumb"
					:show-animation="true"
					v-if="item.enable == true"
				>
					<component :is="item.edit" :editValue="item"></component>
				</uni-collapse-item>
			</uni-collapse>
		</uni-section>
		
		<uni-section title="设置" type="line">
			<view class="example-body">
				<button type="primary" @click="showDrawer('showLeft')"><text class="word-btn-white">社交媒体设置</text></button>
				<uni-drawer ref="showLeft" mode="left" :width="330" :mask-click="false">
					<scroll-view style="height: 100%;" scroll-y="true">
						<view class="button-sp-area">
							<button class="mini-btn" type="warn" size="mini" @click="resetStorage();">重置</button>
							<button class="mini-btn" type="default" size="mini" @click="uploadStorage();">上传</button>
							<button class="mini-btn" type="primary" size="mini" @click="closeDrawer('showLeft');">关闭</button>
						</view>
						<uni-notice-bar show-icon scrollable text="请自行上传您对应的社交软件二维码名片.这只是测试版哟,可离线使用." />
						<view class="uploadSocialConnect">
							<!-- card list -->
							<uni-card 
								v-for="(item,index) in socialMedia"
								:key="index"
							>
								<template v-slot:title>
									<uni-list>
										<uni-list-item 
											:show-switch="true" 
											:switchChecked="item.enable" 
											@switchChange="isAbleMedia(item.edit,item.enable)" 
											:thumb="item.thumb" 
											:title="item.type"
										/>
									</uni-list>
								</template>
								<view v-if="item.edit =='makeuqrCode'">
									<uni-easyinput placeholder="请输入您的电话号码" :value="item.codeValue" ref="setCodeValue" @change="getCodeValue"></uni-easyinput>
								</view>
								<uni-file-picker v-else
									limit="1" 
									title="请上传二维码名片"
									@select="handleSelect($event,item.edit)"
									@fail="handleFail"
								></uni-file-picker>
							</uni-card>
							<!-- card list -->
						</view>
					</scroll-view>
				</uni-drawer>
			</view>
		</uni-section>
	</view>
</template>

<script>
	// const files = require.context("@/components", false, /.vue$/);
	// const modules = {};
	// files.keys().forEach(key => {
	// 	const name = key.split(2,-4);
	// 	console.log(name)
	// 	modules[name] = files(key).default || files(key);
	// });

	import makeuqrCode from '@/components/makeuqrCode.vue';
	import weChat from '@/components/weChat.vue';
	import qq from '@/components/qq.vue';
	import sinaWeibo from '@/components/sinaWeibo.vue';
	import xiaohongshu from '@/components/xiaohongshu.vue';
	import bilibili from '@/components/bilibili.vue';
	import tiktok from '@/components/tiktok.vue';
	import youtube from '@/components/youtube.vue';
	import facebook from '@/components/facebook.vue';
	import instagram from '@/components/instagram.vue';
	import twitter from '@/components/twitter.vue';
	
	export default {
		name:'index',
		components:{
			makeuqrCode,weChat,qq,sinaWeibo,xiaohongshu,bilibili,tiktok,youtube,facebook,instagram,twitter,
		},
		data() {
			return {
				myTag:"你好,交个朋友吧~",
				showLeft: true,
				drag: false,
				groupA: {
				  name: "itxst",
				  put: true, //可以拖入
				  pull: true,
				},
				socialMedia:[
					{
						type:'电话号码',
						edit:'makeuqrCode',
						enable:false,
						thumb:'../../static/socialMediaIcon/dianhuahaoma.png',
						codeValue:''
					},
					{
						type:'微信',
						edit:'weChat',
						enable:false,
						thumb:'../../static/socialMediaIcon/wechat.png',
						qrCodeAddress:''
					},
					{
						type:'QQ',
						edit:'qq',
						enable:false,
						thumb:'../../static/socialMediaIcon/qq.png',
						qrCodeAddress:''
					},
					{
						type:'新浪微博',
						edit:'sinaWeibo',
						enable:false,
						thumb:'../../static/socialMediaIcon/sinaWeibo.png',
						qrCodeAddress:''
					},
					{
						type:'小红书',
						edit:'xiaohongshu',
						enable:false,
						thumb:'../../static/socialMediaIcon/xiaohongshu.png',
						qrCodeAddress:''
					},
					{
						type:'Bilibili',
						edit:'bilibili',
						enable:false,
						thumb:'../../static/socialMediaIcon/bilibili.png',
						qrCodeAddress:''
					},
					{
						type:'抖音',
						edit:'tiktok',
						enable:false,
						thumb:'../../static/socialMediaIcon/tiktok.png',
						qrCodeAddress:''
					},
					{
						type:'Youtube',
						edit:'youtube',
						enable:false,
						thumb:'../../static/socialMediaIcon/youtube.png',
						qrCodeAddress:''
					},
					{
						type:'FaceBook',
						edit:'facebook',
						enable:false,
						thumb:'../../static/socialMediaIcon/facebook.png',
						qrCodeAddress:''
					},
					{
						type:'Instagram',
						edit:'instagram',
						enable:false,
						thumb:'../../static/socialMediaIcon/ins.png',
						qrCodeAddress:''
					},
					{
						type:'Twitter',
						edit:'twitter',
						enable:false,
						thumb:'../../static/socialMediaIcon/twitter.png',
						qrCodeAddress:''
					},
				]
			}
		},
		onPullDownRefresh() {
			this.getQRCode();
			uni.stopPullDownRefresh();
			uni.showToast({
				title:'已刷新'
			})
		},
		onNavigationBarButtonTap(e) {
			if (this.showLeft) {
				this.$refs.showLeft.close()
				this.uploadStorage();
			} else {
				this.$refs.showLeft.open()
			}
		},
		created() {
			this.getQRCode();
		},
		methods: {
			getQRCode(){
				uni.getStorage({
					key:'socialMediaArr',
					success:(res)=>{
						this.socialMedia = res.data;
					},
					fail:(fail)=>{
						console.log('fail',fail)
					}
				})
			},
			// 获取上传状态
			handleSelect(e,edit) { // 上传图片
				uni.saveFile({
					tempFilePath:e.tempFilePaths[0],
					success: (res) => {
						var file1 = 'uniapp_save'
						var savedFilePath = res.savedFilePath;
						let _this = this;
						// 创建doc下存储文件的目录
						plus.io.resolveLocalFileSystemURL('_doc',docEntry=>{  // 打开doc目录
							docEntry.getDirectory('SocialMediaAddQRCode', {create:true,exclusive:false},file2=>{
								plus.io.requestFileSystem(plus.io.PRIVATE_DOC,fs=>{
									fs.root.getFile(savedFilePath, { create: false }, function (fileEntry) {
										fileEntry.moveTo(file2, null, function (fileEntry) {
											_this.socialMedia.map((item)=>{
												if(item.edit == edit){
													item.qrCodeAddress = fileEntry.fullPath;
												}
											})
										});
									})
								})
							})
						})
					},
				})
			},
			isAbleMedia(edit,enable){
				this.socialMedia.map((item)=>{
					if(item.edit == edit){
						item.enable = !enable;
					}
				})
			},
			getCodeValue(){
				this.socialMedia.map((item)=>{
					if(item.edit == 'makeuqrCode'){
						item.codeValue = this.$refs['setCodeValue'][0].val;
					}
				})
			},
			uploadStorage(){
				// var jsonData = JSON.stringify(this.socialMedia);
				uni.setStorage({
					key:'socialMediaArr',
					data:this.socialMedia
				})
				// uni.showToast({
				// 	title:'上传成功'
				// })
				uni.startPullDownRefresh()
			},
			resetStorage(){
				uni.removeStorage({
					key:'socialMediaArr',
					success() {
						uni.showToast({
							title:'重置成功'
						})
					},
					fail(err) {
						uni.showToast({
							title:'内容已重置'
						})
					}
				});
				plus.io.resolveLocalFileSystemURL('_doc/SocialMediaAddQRCode',docEntry=>{
					docEntry.removeRecursively(entry=>{
						plus.nativeUI.toast('重置成功');
					},e=>{
						alert(e.message)
					})
				});
				this.socialMedia.map((item)=>{
					item.enable = false;
					item.codeValue = '';
					item.qrCodeAddress = '';
				})
			},
			handleFail(e){
				console.log(e)
			},
			// --------------------------------drawer--------------------------
			// 打开窗口
			showDrawer(e) {
				this.$refs[e].open()
			},
			// 关闭窗口
			closeDrawer(e) {
				this.$refs[e].close()
				this.uploadStorage();
			},
			//开始拖拽事件
			onStart() {
			  this.drag = true;
			  return true;
			},
			//拖拽结束事件
			onEnd() {
			  this.drag = false;
			},
		}
	}
</script>

<style lang="scss">
	.button-sp-area{margin: 20px auto;display: flex;justify-content: space-around;align-items: center;}
	.ghostClass {
	  background-color: #eee !important;
	}
	
	.chosenClass {
	  background-color: #eee !important;
	  opacity: 1 !important;
	}
	
	.dragClass {
	  background-color: #eee !important;
	  opacity: 1 !important;
	  box-shadow: none !important;
	  outline: none !important;
	  background-image: none !important;
	}
</style>
