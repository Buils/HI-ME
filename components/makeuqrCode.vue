<template>
	<view v-if="editValue.codeValue">
		<u-qrcode style="margin: 20px auto;" auto canvas-id="qr" :value="editValue.codeValue" size="200"></u-qrcode>
	</view>
	<view v-else>
		<text class="a-center pd-tb20 block">未获取到内容,请重新上传!</text>
	</view>
</template>


<script>
	import uQRCode from '@/uni_modules/Sansnn-uQRCode/js_sdk/uqrcode/uqrcode.js';
	export default{
		name:'makeuqrCode',
		components:{uQRCode},
		props:['editValue'],
		data(){
			return{
				
			}
		}
	}
</script>

<style>
	.pd-tb20{padding: 20px 0px;}
	.a-center{text-align: center;}
	.block{display: block;}
</style>