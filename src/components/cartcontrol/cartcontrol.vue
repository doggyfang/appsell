<template>
	<div class="cartcontrol">
		<transition name="move">	
			<div class="cart-decrease" v-if="food.count>0" @click.stop.prenvent="decreaseCart">
				<span class="inner icon-remove_circle_outline"></span>
			</div>
		</transition>
		<div class="cart-count" v-show="food.count>0">{{food.count}}</div>
		<div class="cart-add icon-add_circle" @click.stop.prenvent="addCart">
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
	import Vue from 'vue';
	export default{
		props:{
			food:{
				type:Object
			}
		},
		methods:{
			addCart(event){
				if(!event._constructed){
					return;
				}
				if(!this.food.count){
					Vue.set(this.food,"count",1);
				}else{
					this.food.count++;
				}
				this.$emit('cart-add',event.target);
			},
			decreaseCart(event){
				if(!event._constructed){
					return;
				}
				if(this.food.count){
					this.food.count--;
				}
			}

		}
	}
</script>
<style scoped>
@import "../../common/style.css";
.cartcontrol{
	/*font-size: 0;*/
}	
.cartcontrol .cart-decrease{
	display: inline-block;
	padding:6px;
	transition: all 0.4s linear;
}
.cartcontrol .cart-decrease.move-enter-active,.cartcontrol .cart-decrease.move-leave-active{
	opacity: 1;
	transform: translate3d(0,0,0);
}
.cartcontrol .cart-decrease .inner{
	display: inline-block;
	font-size: 24px;
	line-height: 24px;
	color:rgb(0,160,220);
	transition: all 0.4s linear;
	transform: rotate(0);
}
.cartcontrol .cart-decrease.move-enter,.cartcontrol .cart-decrease.move-leave-to{
	opacity: 0;
	transform: translate3d(24px,0,0);
}
.cartcontrol .cart-decrease.move-enter .inner,.cartcontrol .cart-decrease.move-leave-to .inner{
	transform: rotate(180deg);
}
.cartcontrol .cart-count{
	display: inline-block;
	font-size: 10px;
	vertical-align: top;
	width: 12px;
	padding-top: 6px;
	line-height: 24px;
	text-align: center;
	color:rgb(147,153,159);
}
.cartcontrol .cart-add{
	display: inline-block;
	font-size: 24px;
	line-height: 24px;
	color:rgb(0,160,220);
	padding:6px;
}
</style>