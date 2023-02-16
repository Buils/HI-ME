<template>
	<view class="">
		<uni-notice-bar scrollable="true" single show-icon text="游戏仅用于娱乐,严禁用于赌博或其他违法行为,后果自负."></uni-notice-bar>
		<uni-section title="游戏列表" type="line" padding>
			<uni-grid :column="3" :highlight="true">
				<uni-grid-item v-for="(item, index) in gamesList" :index="index" :key="index">
					<navigator 
						class="navigate-item" 
						:url="item.gameUrl" 
						open-type="navigate" 
						hover-class="navigator-hover"
						>
						<view class="grid-item-box" style="background-color: #fff;">
							<image :src="item.gameIcon" class="image" mode="aspectFill"></image>
							<text class="text text-uppercase f-bold">{{item.gameName}}</text>
						</view>
					</navigator>
				</uni-grid-item>
			</uni-grid>
		</uni-section>
	</view>
</template>

<script>
	export default{
		name:'countDown',
		data(){
			return{
				gamesList:[
					{
						gameName:'摇色子',
						gameIcon:'../../static/gamesImages/gamesIcon/dice.jpeg',
						gameUrl:'../games/Dice',
					},
					{
						gameName:'猜拳',
						gameIcon:'../../static/gamesImages/gamesIcon/finger-guessing.jpg',
						gameUrl:'../games/FingerGuessing',
					},
					{
						gameName:'俄罗斯轮盘',
						gameIcon:'../../static/gamesImages/gamesIcon/russian-roulette.jpg',
						gameUrl:'../games/RussianRoulette',
					},
					{
						gameName:'数字炸弹',
						gameIcon:'../../static/gamesImages/gamesIcon/digitalbomb.jpg',
						gameUrl:'../games/DigitalBomb',
					},
					{
						gameName:'鳄鱼拔牙',
						gameIcon:'../../static/gamesImages/gamesIcon/crocodile.jpg',
						gameUrl:'../games/CrocoDile',
					},
					{
						gameName:'XOXO',
						gameIcon:'../../static/gamesImages/gamesIcon/xoxo.jpg',
						gameUrl:'../games/xoxo',
					}
				]
			}
		},
		methods:{
			
		},
	}
</script>

<style>
	.text-uppercase{text-transform: uppercase;}
	.a-center{text-align: center;}
	.f-bold{font-weight: bold;}
	.grid-item-box {
		display: flex;justify-content: center;align-items: center;flex-direction: column;height: 100%;
	}
	.navigate-item{height: 100%;}
	.image {
		width: 80px;
		height: 80px;
		margin-bottom:5px;
		border-radius: 18px;
		box-shadow: 0px 0px 5px rgba(0,0,0,0.8);
	}
</style>