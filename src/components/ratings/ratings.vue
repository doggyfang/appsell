<template>
	<div class="ratings" ref="ratings">
		<div class="ratings-content">
			<div class="overview">
				<div class="overview-left">
					<div class="score">{{seller.score}}</div>
					<h1 class="title">综合评分</h1>
					<h1 class="rank">高于周边商家{{seller.rankRate}}%</h1>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<div class="title">服务态度</div>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class="title">商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="delivery-wrapper">
						<div class="title">送达时间</div>
						<span class="delivery">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<split></split>
			<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings" v-on:ratingtype-select="ratingtypeSelect"
					v-on:content-toggle="contentToggle">
			</ratingselect>
			<div class="rating-wrapper">
					<ul>
						<li v-show="needShow(rating.rateType,rating.text)"  v-for="rating in ratings" class="rating-item">
							<div class="avatar">
								<img class="avatar" width="28" height="28" :src="rating.avatar">
							</div>
							<div class="content">
								<h1 class="username">{{rating.username}}</h1>
								<div class="star-wrapper">
									<star :size="24" :score=rating.score></star>
									<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
								</div>
								<div class="text">{{rating.text}}</div>
								<div class="recommend" v-show="rating.recommend && rating.recommend.length">
									<span class="icon-thumb_up"></span>
									<span class="item" v-for="item in rating.recommend">{{item}}</span>
								</div>
								<div class="time">{{rating.rateTime | formatDate}}</div>
							</div>
						</li>
					</ul>
				</div>
		</div>
	</div>
</template>
<script>
	import BScroll from "better-scroll";
	import Vue from 'vue';
	import {formatDate} from "../../common/js/data.js";
	import star from '../star/star';
	import split from '../split/split';
	import ratingselect from "../ratingselect/ratingselect";;

	const POSITIVE=0;
	const NEGATIVE=1;
	const ALL=2;


	export default {
		props:{
			// seller:{
			// 	type:Object
			// }
		},
		data(){
			return{
				seller:"",
				ratings:[],
				selectType:ALL,
				onlyContent:true,
				desc:{
					all:"全部",
					positive:"满意",
					negative:"不满意"
				}
			}
		},
		methods:{
			getData(){
		      let vm=this
		      vm.axios.get('http://192.168.43.165:8081/static/data.json')
		      .then(function(response){
		        vm.ratings=response.data.ratings;
		        vm.seller=response.data.seller;
		        vm.$nextTick(()=>{
		        	vm.scroll=new BScroll(vm.$refs.ratings,{
		        		click:true
		        	})
		        })
		      })
		      .catch(function(error){
		        console.log(error);
		      });
		    },
		    needShow(type,text){
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
				this.selectType=type;
				this.$nextTick(()=>{
					this.scroll.refresh();
				})
			},
			contentToggle(){
				this.onlyContent=onlyContent;
				this.$nextTick(()=>{
					this.scroll.refresh();
				})
			}
		},
		mounted(){
		    this.getData()
		},
		filters:{
			formatDate(time){
				let date=new Date();
				return formatDate(date,'yyyy-MM-dd hh:mm');
			}
		},
		components:{
			star,
			split,
			ratingselect
		}	
	};
</script>
<style>
@import "../../common/style.css";
.ratings{
	position: absolute;
	top:178px;
	left:0;
	bottom:0;
	width: 100%;
	overflow: hidden;
}
.ratings .overview{
	padding:18px 0;
	display: flex;
}
.ratings .overview .overview-left{
	flex:0 0 137px;
	width: 275px;
	padding:6px 0;
	border-right:1px solid rgba(7,17,27,0.1);
}
.ratings .overview .overview-left @media only screen and (max-width:320px){
	flex: 0 0 120px;
	width: 120px;
}
.ratings .overview .overview-left .score{
	margin-bottom: 6px;
	line-height: 28px;
	font-size: 24px;
	text-align: center;
	color:rgb(255,153,0);
}
.ratings .overview .overview-left .title{
	margin-bottom: 8px;
	line-height: 12px;
	font-size: 12px;
	text-align: center;
	color:rgb(7,17,27);
}
.ratings .overview .overview-left .rank{
	line-height: 10px;
	font-size: 10px;
	text-align: center;
	color:rgb(147,153,159);
}
.ratings .overview .overview-right{
	flex:1;
	padding:6px 0 6px 24px;
}
.ratings .overview .overview-right @media only screen and (max-width:320px){
	padding-left: 6px;
}
.ratings .overview .overview-right .score-wrapper{
	margin-bottom: 8px;
	line-height: 18px;
	font-size: 0;
}
.ratings .overview .overview-right .score-wrapper .title{
	display: inline-block;	
	line-height: 18px;
	font-size: 12px;
	font-weight: 700;
	color:rgb(7,17,27);
}
.ratings .overview .overview-right .score-wrapper .star{
	display: inline-block;
	margin:0 12px;
}
.ratings .overview .overview-right .score-wrapper .score{
	display: inline-block;
	line-height: 18px;
	font-size: 12px;
	color:rgb(255,153,0);	
}
.ratings .overview .overview-right .delivery-wrapper{
	font-size: 0;
}
.ratings .overview .overview-right .delivery-wrapper .title{
	display: inline-block;	
	line-height: 18px;
	font-size: 12px;
	font-weight: 700;
	color:rgb(7,17,27);
	margin-right:12px;
}
.ratings .overview .overview-right .delivery-wrapper .delivery{
	display: inline-block;
	line-height: 18px;
	font-size: 12px;
	color:rgb(147,153,159);	
}
.ratings .rating-wrapper{
	padding: 0 18px;
}
.ratings .rating-wrapper .rating-item{
	padding: 18px 0;
	display: flex;
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.ratings .rating-wrapper .rating-item .avatar{
	flex: 0 0 28px;
	width: 28px;
	margin-right: 12px;
	border-radius: 50%;
}
.ratings .rating-wrapper .rating-item .content{
	position: relative;
	flex: 1;
}
.ratings .rating-wrapper .rating-item .content .username{
	margin-bottom: 4px;
	line-height: 12px;
	font-size: 10px;
	color: rgb(7, 17, 27);	
}
.ratings .rating-wrapper .rating-item .content .star-wrapper{
	margin-bottom: 6px;
	line-height: 12px;
	font-size: 0;
}
.ratings .rating-wrapper .rating-item .content .star-wrapper .star{
	display: inline-block;
	vertical-align: top;
}
.ratings .rating-wrapper .rating-item .content .star-wrapper .delivery{
	margin-left: 6px;
	line-height: 12px;
	vertical-align: top;
	font-size: 10px;
	color: rgb(147, 153, 159);
}
.ratings .rating-wrapper .rating-item .content .text{
	margin-bottom: 8px;
	line-height: 18px;
	font-size: 12px;
	color: rgb(7, 17, 27);
}
.ratings .rating-wrapper .rating-item .content .recommend{
	line-height: 16px;
	font-size: 0;
}
.ratings .rating-wrapper .rating-item .content .recommend .icon-thumb_up{
	display: inline-block;
	margin: 0 8px 4px 0;
	line-height: 16px;
	font-size: 12px;
	color: rgb(0, 160, 220);
}
.ratings .rating-wrapper .rating-item .content .recommend .item{
	display: inline-block;
	padding: 0 6px;
	margin: 0 8px 4px 0;
	line-height: 16px;
	font-size: 9px;
	color: rgb(147, 153, 159);
	background-color: #fff;
	border-radius: 1px;
	border: 1px solid rgba(7, 17, 27, 0.1);
}
.ratings .rating-wrapper .rating-item .content .recommend .item:last-child{
	margin-right:0;
}
.ratings .rating-wrapper .rating-item .content .time{
	position: absolute;
	top: 0;
	right: 0;
	line-height: 12px;
	font-size: 10px;
	color: rgb(147, 153, 159);
}
</style>
