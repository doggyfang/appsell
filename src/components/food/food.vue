<template>
	<transition name="move">
		<div class="food" v-if="showFlag" ref="food">
			<div class="food-content">
				<div class="image-header">
					<img :src="food.image">
					<div class="back" @click.stop.prevent="hide">
						<i class="icon-arrow_lift"></i>
					</div>
				</div>
				<div class="content">
					<h1 class="title">{{food.name}}</h1>
					<div class="detail">
						<span class="sell-count">月售{{food.sellCount}}份</span>
						<span class="rating">好评率 {{food.rating}}%</span>
					</div>
					<div class="price">
						<span class="now">￥{{food.price}}</span>
						<span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
					</div>
					<div class="cartcontrol-wrapper">
							<cartcontrol :food="food"></cartcontrol>
						</div>
						<transition name="fade">
							<div @click.stop.prenvent="addFirst" class="buy" v-show="!food.count || food.count===0">加入购物车</div>
						</transition>
				</div>
				<split v-show="food.info"></split>
				<div class="info">
					<h1 class="title">商品信息</h1>
					<p class="text" v-show="food.info">{{food.info}}</p>
				</div>
				<split></split>
				<div class="rating">
					<h1 class="title">商品评价</h1>
					<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings" v-on:ratingtype-select="ratingtypeSelect"
					v-on:content-toggle="contentToggle"></ratingselect>
				</div>
				<div class="rating-wrapper">
					<ul v-show="food.ratings && food.ratings.length">
						<li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
							<div class="user">
								<div class="name">{{rating.username}}</div>
								<img class="avatar" width="12" height="12" :src="rating.avatar">
							</div>
							<div class="time">{{rating.rateTime | formatDate}}</div>
							<p class="text">
								<span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
							</p>
						</li>
					</ul>
					<div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
				</div>
			</div>
		</div>
	</transition>
</template>
<script>
import BScroll from "better-scroll";
import Vue from 'vue';
import {formatDate} from "../../common/js/data.js";
import cartcontrol from "../cartcontrol/cartcontrol";
import split from "../split/split";
import ratingselect from "../ratingselect/ratingselect";

	const POSITIVE=0;
	const NEGATIVE=1;
	const ALL=2;

	export default{
		props:{
			food:{
				type:Object
			}
		},
		data(){
			return{
				showFlag:false,
				selectType:ALL,
				onlyContent:true,
				desc:{
					all:"全部",
					positive:"推荐",
					negative:"吐槽"
				}
			}
		},
		methods:{
			show(){
				this.showFlag=true;
				this.selectType=ALL;
				this.onlyContent=true;
				this.$nextTick(()=>{
					if(!this.scroll){
						this.scroll= new BScroll(this.$refs.food,{
							click:true
						})
					}else{
						this.scroll.refresh();
					}
				});
			},
			hide(){
				this.showFlag=false;
			},
			addFirst(event){
				if(!event._constructed){
					return;
				}
				Vue.set(this.food,'count',1)
			},
			needShow(type,text){
				console.log("aa")
				if(this.onlyContent && !text){
					return false;
				}
				if(this.selectType===ALL){
					return true
				}else{
					return type===this.selectType;
				}
			},
			ratingtypeSelect(type){
				console.log("ss")
				this.selectType=type;
				this.$nextTick(()=>{
					this.scroll.refresh();
				})
			},
			contentToggle(onlyContent){
				this.onlyContent=onlyContent;
				this.$nextTick(()=>{
					this.scroll.refresh();
				})
			}
		},
		events:{
			
		},
		filters:{
			formatDate(time){
				let date=new Date();
				return formatDate(date,'yyyy-MM-dd hh:mm');
			}
		},
		components:{
			cartcontrol,
			split,
			ratingselect
		}
	}
</script>
<style scoped>
@import "../../common/style.css";
.food{
	position: fixed;
	left: 0;
	top: 0;
	bottom: 48px;
	background-color: #fff;
	z-index: 30;
	width: 100%;
	}
.food.move-enter-active,.food.move-leave-active{
	transform: translate3d(0, 0, 0);
	transition: all 0.2s linear;
}
.food.move-enter,.food.move-leave-to{
	transform: translate3d(100%, 0, 0);
}
.food .image-header{
	position: relative;
	width: 100%;
	height: 0;
	padding-top: 100%;
}
.food .image-header img{
	position: absolute;
	left: 0;
	top: 0;
	width: 100%;
	height: 100%;
}
.food .image-header .back{
	position: absolute;
	top: 10px;
	left: 0;
}
.food .image-header .back .icon-arrow_lift{
	display: block;
	padding: 10px;
	font-size: 20px;
	color: #fff;
}
.food .content{
	position: relative;
	padding: 18px;
}
.food .content .title{
	line-height: 14px;
	margin-bottom: 8px;
	font-size: 14px;
	font-weight: 700;
	color: rgb(7, 17, 27)
}
.food .content .detail{
	margin-bottom: 18px;
	line-height: 10px;
	height: 10px;
	font-size: 0;
}
.food .content .detail .sell-count,.food .content .detail .rating{
	font-size: 10px;
	color: rgb(147, 153, 159);
}
.food .content .detail .sell-count{
	margin-right: 12px;
}
.food .content .price{
	font-weight: 700;
	line-height: 24px;
}
.food .content .price .now{
	font-size: 14px;
	color:rgb(240,20,20);
}
.food .content .price .old{
	font-size: 10px;
	color:rgb(147,153,159);
}
.food .cartcontrol-wrapper{
	position: absolute;
	right: 12px;
	bottom: 12px;
}
.food .buy{
	position: absolute;
	right:18px;
	bottom:18px;
	z-index: 10;
	height:24px;
	line-height: 24px;
	padding:0 12px;
	box-sizing: border-box;
	font-size: 10px;
	border-radius: 12px;
	color:#fff;
	background-color: rgb(0,160,220);
}
.food .buy.fade-enter-active,.food .buy.fade-leave-active{
    transition: all 0.2s;
    opacity: 1;
}
.food .buy.fade-enter, &.fade-leave-to{
    opacity: 0;
}
.food .info{
	padding:18px;	
}
.food .info .title{
	line-height: 14px;
	margin-bottom: 6px;
	font-size: 14px;
	color:rgb(7,17,27);
}
.food .info .text{
	padding:0 8px;
	line-height: 24px;
	font-size: 12px;
	color:rgb(77,85,93);
}
.food .rating .title{
	line-height: 14px;
	margin-left: 18px;
	margin-top: 18px;
	font-size: 14px;
	color:rgb(7,17,27);
}
.food .rating-wrapper{
	padding: 0 18px;
}
.food .rating-wrapper .rating-item{
	position: relative;
	padding: 16px 0;
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.food .rating-wrapper .rating-item .user{
	position: absolute;
	right: 0;
	top:16px;
	line-height: 12px;
	font-size: 0;
}
.food .rating-wrapper .rating-item .user .name{
	display: inline-block;
	margin-right: 6px;
	vertical-align: top;
	font-size: 10px;
	color: rgb(147, 153, 159);
}
.food .rating-wrapper .rating-item .user .avatar{
	border-radius: 50%;
}
.food .rating-wrapper .rating-item .time{
	margin-bottom: 6px;
	font-size: 10px;
	line-height: 12px;
	color: rgb(147, 153, 159);
}
.food .rating-wrapper .rating-item .text{
	line-height: 16px;
	font-size: 12px;
	color: rgb(7, 17, 27);
}
.food .rating-wrapper .rating-item .text .icon-thumb_up,.food .rating-wrapper .rating-item .text .icon-thumb_down{
	line-height: 24px;
	margin-right: 4px;
	font-size: 12px;
}
.food .rating-wrapper .rating-item .text .icon-thumb_up{
	color: rgb(0, 160, 220);
}
.food .rating-wrapper .rating-item .text .icon-thumb_down{
	color: rgb(147,153,159);
}
.food .rating-wrapper .no-rating{
	padding: 16px 0;
	font-size: 12px;
	color: rgb(147,153,159)
}
</style>
