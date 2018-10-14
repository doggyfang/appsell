<template>
	<div>
	<div class="shopcart">
		<div class="content" @click="toggleList">
			<div class="content-left">
				<div class="logo-wrapper">
					<div class="logo" :class="{'highlight':totalCount>0}">
						<span class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></span>
					</div>
					<div class="num" :class="{'highlight':totalCount>0}" v-if="totalCount>0">{{totalCount}}</div>
				</div>
				<div class="price">￥{{totalPrice}}</div>
				<div class="description">另需配送费￥{{seller.deliveryPrice}}元</div>
			</div>
			<div class="content-right" @click.stop.prevent="pay">
				<div class="pay" :class="payClass">{{payDesc}}</div>
			</div>
		</div>
		<div class="ball-container">
			<div v-for="ball in balls">
				<transition name="drop" @before-enter='beforeDrop' @enter='dropping' @after-enter="afterDrop">
					<div class="ball" v-show="ball.show" >
						<div class="inner inner-hook"></div>
					</div>
				</transition>
			</div>			
		</div>
		<transition name="fold">
			<div class="shopcart-list" v-show="listShow">
				<div class="list-header">
					<h1 class="title">购物车</h1>
					<span class="empty" @click="empty">清空</span>
				</div>
				<div class="list-content" ref="listContent">
					<ul>
						<li class="food" v-for="food in selectFoods">
							<span class="name">{{food.name}}</span>
							<div class="price">
								<span>￥{{food.price*food.count}}</span>
							</div>
							<div class="cartcontrol-wrapper">
								<cartcontrol :food="food" @cart-add="cartAdd"></cartcontrol>
							</div>
						</li>
					</ul>
				</div>
			</div>
		</transition>	
	</div>
	<transition name="fade">
			<div class="list-mask" v-show="listShow" @click="hideList"></div>
	</transition>
	</div>
</template>
<script>
import BScroll from "better-scroll";
import cartcontrol from "../cartcontrol/cartcontrol";
	export default {
		data () {
		    return {
		      seller: '',
		      balls:[
			      {
			      	show:false
			      },
			      {
			      	show:false
			      },
			      {
			      	show:false
			      },
			      {
			      	show:false
			      },
			      {
			      	show:false
			      }
		      ],
		      dropBalls:[],
		      fold:true
		    }
		},
		props:{
			selectFoods:{
				type:Array,
				default:function(){
					return [{}];
				}
			}
		},
		computed:{
			totalPrice(){
				let total=0;
				this.selectFoods.forEach( function(food) {
					total+=food.price*food.count;
				});
				return total;
			},
			totalCount(){
				let count=0;
				this.selectFoods.forEach( (food)=>{
					count+=food.count;
				});
				return count;

			},
			payDesc(){
				if(this.totalPrice===0){
					return '￥20元起送';
				}else if(this.totalPrice<20){
					let diff=20-this.totalPrice;
					return `还差￥${diff}起送`;
				}else{
					return '去结算';
				}
			},
			payClass(){
				if(this.totalPrice>=20){
					return 'enough';
				}else{
					return 'not-enough';
				}
			},
			listShow(){
				if(!this.totalCount){
					this.fold=true;
					return false;
				}
				let show=!this.fold;
				if(show){
					this.$nextTick(()=>{
						if(!this.scroll){
							this.scroll=new BScroll(this.$refs.listContent,{
								click:true
							});
						}else{
							this.scroll.refresh();
						}	
					});
				}
				return show;
			}
		},
		methods:{
			getData(){
		      let vm=this
		      vm.axios.get('http://192.168.43.165:8081/static/data.json')
		      .then(function(response){
		        vm.seller=response.data.seller;
		      })
		      .catch(function(error){
		        console.log(error);
		      });
		    },
		    drop(el){	
		    	for(let i=0;i<this.balls.length;i++){
		    		let ball=this.balls[i];
		    		if(!ball.show){
		    			ball.show=true;
		    			ball.el=el;
		    			this.dropBalls.push(ball);
		    			return;
		    		}
		    	}
		    },
		    beforeDrop(el){
				let count=this.balls.length;
				while (count--) {
					let ball=this.balls[count];
					if(ball.show){
						let rect=ball.el.getBoundingClientRect();
						let x=rect.left-32;
						let y=-(window.innerHeight-rect.top-22);
						el.style.diaplay="";
						el.style.webkitTransform=`translate3d(0,${y}px,0)`;
						el.style.transform=`translate3d(0,${y}px,0)`;
						let inner=el.getElementsByClassName("inner-hook")[0];
						inner.style.webkitTransform=`translate3d(${x}px,0,0)`;
						inner.style.transform=`translate3d(${x}px,0,0)`;
					}
				}
			},
			dropping(el, done) {
		        /* eslint-disable no-unused-vars */
		        let rf = el.offsetHeight;
		        this.$nextTick(() => {
		          el.style.webkitTransform = 'translate3d(0,0,0)';
		          el.style.transform = 'translate3d(0,0,0)';
		          let inner = el.getElementsByClassName('inner-hook')[0];
		          inner.style.webkitTransform = 'translate3d(0,0,0)';
		          inner.style.transform = 'translate3d(0,0,0)';
		          el.addEventListener('transitionend', done);
		        });
		     },
			afterDrop(el){
				let ball=this.dropBalls.shift();
				if(ball){
					ball.show=false;
					el.style.display = 'none';
				}
			},
			cartAdd(target) {
		        this.drop(target);
		    },
		    toggleList(){
		    	if(!this.totalCount){
					return;
				}
				this.fold=!this.fold;
		    },
		    empty(){
		    	this.selectFoods.forEach((food)=>{
		    		food.count=0// statements
		    	});
		    },
		    pay(){
		    	if(this.totalPrice<20){
		    		return;
		    	}
		    	window.alert(`支付${this.totalPrice}元`)
		    },
		    hideList(){
		    	this.fold=true;
		    }
		},
		mounted(){
		    this.getData();
		},
		components:{
  			cartcontrol
  		}
	}
</script>
<style scoped>
@import "../../common/style.css";
.shopcart{
	position: fixed;
	left: 0;
	bottom: 0;
	width: 100%;
	height: 48px;
	z-index: 50;
}
.shopcart .content{
	display: flex;
	background-color: #141d27;
	font-size: 0;
}
.shopcart .content .content-left{
	flex: 1;
}
.shopcart .content .content-left .logo-wrapper{
	display: inline-block;
	position: relative;
	top: -10px;
	margin: 0 12px;
	padding: 6px;
	width: 56px;
	height: 56px;
	box-sizing:border-box;
	border-radius: 50%;
	vertical-align: top;
	background-color: #141d27;
}
.shopcart .content .content-left .logo-wrapper .num{
	position: absolute;
	top: 0;
	right: 0;
	width: 24px;
	height: 16px;
	line-height: 16px;
	text-align: center;
	border-radius: 16px;
	color: #fff;
	background-color: rgb(240, 20, 20);
	font-size: 9px;
	font-weight: 700;
	box-shadow: 0 4px 8px 0 rgb(0, 0, 0,0.4);
}
.shopcart .content .content-left .logo-wrapper .logo{
	width: 100%;
	height: 100%;
	border-radius: 50%;
	background-color: #2b343c;
	text-align: center;
}
.shopcart .content .content-left .logo-wrapper .logo.highlight{
	background-color: rgb(0, 160, 220);
}
.shopcart .content .content-left .logo-wrapper .logo .icon-shopping_cart{
	color: #80858a;
	font-size: 24px;
	
	line-height: 44px;
}
.shopcart .content .content-left .logo-wrapper .logo .icon-shopping_cart.highlight{
	color: #fff;
}
.shopcart .content .content-left .price{
	display: inline-block;
	vertical-align: top;
	font-size: 16px;
	line-height: 24px;
	margin-top: 12px;
	color: rgb(255, 255, 255,0.4);
	font-weight: 700;
	padding-right: 12px;
	box-sizing: border-box;
	border-right: 1px solid rgb(255,255, 255,0.1);
}
.shopcart .content .content-left .price.highlight{
	color: #fff;
}
.shopcart .content .content-left .description{
	display: inline-block;
	vertical-align: top;
	color: rgb(255, 255, 255,0.4);
	line-height: 24px;
	font-size: 10px;
	margin: 12px 0 0 12px;
}
.shopcart .content .content-right{
	flex: 0 0 105px;
	width: 105px;
}
.shopcart .content .content-right .pay{
	height: 48px;
	line-height: 48px;
	text-align: center;
	color: rgb(255, 255, 255,0.4);
	font-size: 12px;
	height: 48px;
	background-color: #2b333b;
}
.shopcart .content .content-right .pay.not-enough{
	background-color: #2b333b;
}
.shopcart .content .content-right .pay.enough{
	background-color: #00b43c;
	color: #fff;
}
.shopcart .ball-container .ball{
	position: fixed;
	left:32px;
	bottom:22px;
	z-index: 200;
	transition: all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41);
}
.shopcart .ball-container .ball .inner{
	width: 16px;
	height: 16px;
	border-radius: 50%;
	background-color: rgb(0,160,220);
	transition: all 0.4s linear;
}
.shopcart .shopcart-list{
	position: absolute;
	left: 0;
	top: 0;
	z-index: -1;
	width: 100%;
	transform: translate3d(0, -100%, 0)
	/* height:-100%; */
}
.shopcart .shopcart-list.fold-enter-active,.shopcart .shopcart-list.fold-leave-active{
	transition: all 0.5s;

}
.shopcart .shopcart-list.fold-enter,.shopcart .shopcart-list.fold-leave-active{
	transform: translate3d(0, 0, 0);
}
.shopcart .shopcart-list .list-header{
	height: 40px;
	line-height: 40px;
	padding: 0 18px;
	background-color: #f3f5f7;
	border-bottom: 1px solid rgba(7, 17, 27,0.4);
}
.shopcart .shopcart-list .list-header .title{
	float: left;
	font-size: 14px;
	color: rgb(7, 17, 27);
}
.shopcart .shopcart-list .list-header .empty{
	float: right;
	font-size: 12px;
	color: rgb(0, 160, 220);
}
.shopcart .shopcart-list .list-content{
	padding: 0 18px;
	max-height: 217px;
	background-color: #fff;
	overflow: hidden;
}
.shopcart .shopcart-list .list-content .food{
	position: relative;
	padding: 12px 0;
	box-sizing: border-box;
	border-bottom: 1px solid rgba(7, 17, 27,0.1)
}
.shopcart .shopcart-list .list-content .food .name{
	line-height: 24px;
	font-size: 14px;
	color: rgb(7, 17, 27);
}
.shopcart .shopcart-list .list-content .food .price{
	position:absolute;
	right: 90px;
	bottom: 12px;
	line-height: 24px;
	font-size: 14px;
	color: rgb(240, 20, 20);
	font-weight: 700;
}
.shopcart .shopcart-list .list-content .food .cartcontrol-wrapper{
	position: absolute;
	right: 0;
	bottom: 6px;
}
.list-mask{
	position: fixed;
	left:0;
	top:0;
	width: 100%;
	height: 100%;
	z-index: 40;
	opacity: 1;
	backdrop-filter:blur(10px);
	background-color: rgba(7,17,27,0.6);
}
.list-mask.fade-enter-active,.list-mask.fade-leave-active{
	transition: all 0.5s;	
}
.list-mask.fade-enter,.list-mask.fade-leave-active{
	opacity: 0;
	background-color: rgba(7,17,27,0);
}
</style>
