<!-- 记账 -->
<template>
	<view class="app">
		
		<view class="top">
				<button v-for="(item,index) in items"  @click="onClick(index)"  :class=" isActive[index] ==1 ? 'active':'inactive' " :key='index'>{{item}}</button>
		</view>
		
		<view class="bottom whole_view">
			<!-- 支出按钮布局 -->
			<view class="pay_view" v-show="isActive[0]" >
				<view class="pay_item item" v-for="(location,index) in payUrl" v-bind:class=" payItemSelected[index] ? 'selected':'unselected' " @click="paySelected(index,$event);showCalculate(index)" :key='index'>
					<view class="img_view"><image :src='location'></image></view>
					<text> {{payName[index]}}</text>
				</view>
			</view>
			
			<!-- 收入按钮布局 -->
			<view class="earn_view" v-show="isActive[1]">
				<view class="earn_item item" v-for="(location,index) in earnUrl"  v-bind:class=" earnItemSelected[index] ? 'selected':'unselected' " @click="earnSelected(index,$event);showCalculate(index)" :key='index'>
					<view class="img_view"><image :src='location'></image></view>
					<text>{{earnName[index]}}</text>
				</view>
			</view>
			
			<!-- 计算窗口 -->
			<view v-show="isCal" class="wholeKeyBoard">
				<!-- 第一行设置备注 -->
				<view class="remarks">
					<text class="remark">备注:</text>
					<textarea class="remarkArea" value="" v-model="remarks"  />
					<!-- 计算的金额 -->
					<text class="calnum">{{moneyNum}}</text>
				</view>
				<!-- 第二行 -->
				<view class="secondLine">
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="7" @click="calnum('7')">7</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="8" @click="calnum('8')">8</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="9" @click="calnum('9')">9</view>
					<view>
					    <xp-picker ref="picker"  @confirm="confirm"   />
					    <view @tap="show" hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton">{{time}}</view>
					</view>
				</view>
				<!-- 第三行 -->
				<view class="thirdLine">
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="4" @click="calnum('4')">4</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="5" @click="calnum('5')">5</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="6" @click="calnum('6')">6</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="+" @click="calnum('+')">+</view>
				</view>
				<!-- 第四行 -->
				<view class="fourthLine">
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="1" @click="calnum('1')">1</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="2" @click="calnum('2')">2</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="3" @click="calnum('3')">3</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="-" @click="calnum('-')">-</view>
				</view>
				<!-- 第五行 -->
				<view class="fifthLine">
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" @click="calnum('.')">.</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" keyValue="0" @click="calnum('0')">0</view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton" @click="back"><image class="keyboardPic" src="../../static/keyboardPic/退格.png" mode=""></image></view>
					<view  hover-class="keyButtonSelected" hover-start-time='20'hover-stay-time='70'  class="keyBoardButton complete"  @click="completeBtn" >完成</view>
				</view>
			</view>
		</view>
	</view>	
</template>

<script>
	export default {
		data() {
			return {
				items:['支出','收入'],
				isActive:[0,0],
				payUrl:["../../static/payPic/餐饮.png","../../static/payPic/服饰.png","../../static/payPic/工具.png","../../static/payPic/公交.png","../../static/payPic/蔬菜.png","../../static/payPic/数码.png","../../static/payPic/运动.png"],
				earnUrl:["../../static/earnPic/股票.png","../../static/earnPic/红包.png","../../static/earnPic/礼品.png","../../static/earnPic/理财.png"],
				payName:["餐饮","服饰","工具","公交","蔬菜","数码","运动"],
				earnName:["股票","红包","礼品","理财"],
				payItemSelected:[0,0,0,0,0,0,0],
				earnItemSelected:[0,0,0,0],
				category:'',
				isCal: 0,
				remarks:'',
				moneyNum:'',
				time:'今天',
				moneyCal:'',
				datas:[]
				
			}
		},
		onLoad(){
			console.log('onloading')
			uni.getStorage({
				key:'time',
				success:(res)=>{
					console.log(res.data)
					this.datas = res.data
					console.log(this.datas)
				}
			})
		},
		onShow(){
			this.isActive =  [1,0]
		},
		// 界面隐藏的时候取消所有选中按钮
		onHide(){
			this.payItemSelected = [0,0,0,0,0,0,0]
			this.earnItemSelected = [0,0,0,0]
			this.isCal = 0
		},
		methods: {
			// 选中按钮
			onClick(index){
				this.isCal= 0
				this.isActive=[0,0]
				this.payItemSelected = [0,0,0,0,0,0,0]
				this.earnItemSelected = [0,0,0,0]
				this.isActive[index]= 1
				this.remarks = ''
				this.moneyNum = ''
			},
			paySelected(index){
				this.payItemSelected = [0,0,0,0,0,0,0]
				this.isCal= 0
				this.payItemSelected[index] = 1
				this.category = this.payName[index]
			},
			earnSelected(index){
				this.earnItemSelected = [0,0,0,0]
				this.earnItemSelected[index] = 1
				this.isCal= 0
				this.category = this.earnName[index]
			},
			showCalculate(index){
				this.isCal = 1
				uni.hideTabBar({
				})
			},
			completeBtn(){
				// 计算总金额
				var seperator = ['支出','收入']
				var queue = null
				if(this.isActive[0]==1){
					queue = 0
				}else{
					queue = 1
				}
				
				var datas = {
					'remark':this.remarks,
					'money':eval(this.moneyNum),
					'category':this.category,
					'seperate':seperator[queue],
					'time':this.time
				}
				if(datas.time == '今天'){
					var today = new Date()
					var month = today.getMonth()+1
					if(month<=9){
						month = '0'+month
					}
					var date = today.getDate()
					if(date<=9){
						date = '0'+date
					}
					datas.time = today.getFullYear()+"-"+month+'-'+date
					
				}
				datas.year = datas.time.substring(0,4)
				datas.month = datas.time.substring(5,7)
				datas.date = datas.time.substring(8)
				this.datas.push(datas)
				console.log(this.datas)
				this.moneyNum = ''
				this.time= "今天"
				uni.setStorage({
					key:'time',
					data:this.datas
				})
				uni.showTabBar({
				})
				this.isCal = 0
			},
			calnum(str){
				this.moneyNum+=str
			},
			back(){
				this.moneyNum = this.moneyNum.substring(0,this.moneyNum.length-1)
			},
			show() {
			    this.$refs.picker.show()
			},
			confirm(e) {
				this.time = e.value
			    console.log(this.time)
			}
		}
	}
</script>

<style lang="scss">
	.top{
		position: fixed;
		width: 750rpx;
		top: 0;
		text-align: center;
		background-color: $ycolor;
		button{
			display: inline-block;
			height: 58rpx;
			width: 272rpx;
			line-height: 58rpx;
			border: 2rpx solid;
		}
	}
	.bottom{
		margin-top: 58rpx;
	}
	//选中按钮后的样式
	.active{
		background: $gcolor;
		color: #fcdb4d;
	}
	.inactive{
		background-color: $ycolor;
		color: #31333b;
	}
	.item{
		display: inline-block;
		text-align: center;
		.img_view{
			background-color: #f5f5f5;
			border-radius: 108rpx;
			width: 108rpx;
			image {
				margin: 19rpx;
				width: 70rpx;
				height: 70rpx;
			}
			margin:20rpx 36rpx 0;
		}
		text{
			display: inline-block;
			font-size: 26rpx;
			font-weight: 400;
		}
	}
	// 选中明细按钮后改变颜色
	.selected {
		.img_view{
			background-color: $ycolor;
		}
	}
	.unselected{
		.img_view{
			background-color: #f5f5f5;
		}
	}
	.wholeKeyBoard{
		
		position: fixed;
		bottom: 0;
		background-color: #fafafa;
		width: 750rpx;
		height: 558rpx;
		.keyButtonSelected{
			background-color: #dfdfdf;
			
		}
	}
	.remarks{
		width: 750rpx;
		height: 100rpx;
		border: 2rpx  solid  #dfdfdf;
		line-height: 100rpx;
		box-sizing: border-box;
		.remarkArea{
			position: fixed;
			left: 90rpx;
			width: 440rpx;
			height: 100rpx;
			box-sizing: border-box;
		}
		.remark{
			left: 10rpx;
			position: fixed;
			margin: 0 auto;
		}
		.calnum{
			right: 0;
			position: fixed;
			font-weight: bold;
			font-size: 40rpx;
		}
	}
	.secondLine{
		height: 115rpx;
		width: 750rpx;
		box-sizing: border-box;
		display: flex;
		.keyBoardButton{
			flex: 1;
			text-align: center;
			line-height: 115rpx;
			border: 1rpx solid #dfdfdf;
			display:inline-block;
			width:187.5rpx;
			height: 100%;
			box-sizing: border-box ;
		}
	}
	.thirdLine{
		height: 115rpx;
		width: 750rpx;
		box-sizing: border-box;
		display: flex;
		.keyBoardButton{
			flex: 1;
			text-align: center;
			line-height: 115rpx;
			border: 1rpx solid #dfdfdf;
			display:inline-block;
			width:187.5rpx ;
			height: 100%;
			box-sizing: border-box ;
		}
	}
	.fourthLine{
		height: 115rpx;
		width: 750rpx;
		box-sizing: border-box;
		display: flex;
		.keyBoardButton{
			flex: 1;
			text-align: center;
			line-height: 115rpx;
			border: 1rpx solid #dfdfdf;
			display:inline-block;
			width:187.5rpx ;
			height: 100%;
			box-sizing: border-box ;
		}
	}
	.fifthLine{
		height: 115rpx;
		width: 750rpx;
		box-sizing: border-box;
		display: flex;
		.keyBoardButton{
			flex: 1;
			text-align: center;
			line-height: 115rpx;
			border: 1rpx solid #dfdfdf;
			display:inline-block;
			width:187.5rpx ;
			height: 100%;
			box-sizing: border-box ;
			.keyboardPic{
				margin: 30rpx 0;
				width: 35%;
				height: 50%;
			}
		}
		.complete{
			font-size: 35rpx;
			font-weight: bold;
			background-color: $ycolor;
		}
	}
	
	
	
</style>
