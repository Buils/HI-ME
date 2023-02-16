<template>
	<view v-if="editValue.qrCodeAddress">
		<view class="content a-center">
			<image :src="editValue.qrCodeAddress" class="image" mode="widthFix"/>
		</view>
		<uni-section title="更多" type="circle" padding v-if="optionList">
			<uni-grid :column="3" :square="false" :highlight="true">
				<uni-grid-item v-for="item2 in optionList">
					<view class="grid-item-box" @click="useListFunc(item2.funcName)">
						<image :src="item2.url" class="image" mode="aspectFill" style="width: 25px;height: 25px;"/>
						<text class="text">{{ item2.text }}</text>
					</view>
				</uni-grid-item>
			</uni-grid>
		</uni-section>
	</view>
	<view v-else>
		<text class="a-center pd-tb20 block">未获取到内容,请重新上传!</text>
	</view>
</template>

<script>
	export default{
		name:'sinaWeibo',
		props:['editValue'],
		data(){
			return{
				optionList: [
					{
						url: '/static/images/c1.png',
						text: '微博',
						funcName:'openSinaWeibo'
					},
				]
			}
		},
		methods:{
			useListFunc(funcName){
				switch (funcName){
					case 'openSinaWeibo':
						if (plus.os.name == 'Android') {
						    plus.runtime.launchApplication(  
						        {  
						            pname: 'com.sina.weibo'  
						        },  
						        function(e) {  
						           plus.nativeUI.toast("未发现新浪微博 App")
						        }  
						    );  
						} else if (plus.os.name == 'iOS') {  
						    plus.runtime.launchApplication({ action: 'sinaweibo://' }, function(e) {  
								plus.nativeUI.toast("未发现新浪微博 App")
						    });  
						}
						break;
					
					default:
						break;
				}
			}
		}
	}
</script>

<style>
	.pd-tb20{padding: 20px 0px;}
	.block{display: block;}
	.a-center{text-align: center;}
	.example-body {
		padding: 10px;
		padding-top: 0;
	}
	.grid-item-box {
		flex: 1;
		// position: relative;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 15px 0;
	}
</style>