<!-- 账单 -->
<template>
	<view>
		<button type="default" @click="clickme()">lalala</button>
		 
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		
		methods: {
			clickme(){
				 uni.getStorage({
					key : 'time',
					success:function(res){
						console.log(res)
					}
				})
			}
		}
	}
</script>

<style>

</style>
