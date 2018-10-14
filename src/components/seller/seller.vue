<template>
	<div class="seller" ref="seller">
		<div class="seller-content">
			<div class="overview">
				<h1 class="title">{{seller.name}}</h1>
				<div class="desc">
					<star :size="36" :score="seller.score"></star>
					<span class="text">({{seller.ratingCount}})</span>
					<span class="text">月售{{seller.sellCount}}单</span>
				</div>
				<ul class="remark">
					<li class="block">
						<h2>起送价</h2>
						<div class="content">
							<span class="stress">{{seller.minPrice}}</span>元
						</div>
					</li>
					<li class="block">
						<h2>商家配送</h2>
						<div class="content">
							<span class="stress">{{seller.deliveryPrice}}</span>元
						</div>
					</li>
					<li class="block">
						<h2>平均配送时间</h2>
						<div class="content">
							<span class="stress">{{seller.deliveryTime}}</span>元
						</div>
					</li>
				</ul>
				<div class="favorite" @click="toggleFavorite">
					<span class="icon-favorite" :class="{'active':favorite}"></span>
					<span class="text">{{favoriteText}}</span>
				</div>
			</div>
			<split></split>
			<div class="bulletin">
				<h1 class="title">公告与活动</h1>
				<div class="content-wrapper">
					<p class="text">{{seller.bulletin}}</p>
				</div>
			</div>
			<ul v-if="seller.supports" class="supports">
    			<li v-for="(item,index) in seller.supports" class="support-item">
    				<span class="icon" :class="classMap[seller.supports[index].type]"></span>
    				<span class="text">{{seller.supports[index].description}}</span>
    			</li>
    		</ul>
    		<split></split>
    		<div class="pics">
    			<h1 class="title">商家实景</h1>
    			<div class="pic-wrapper" ref="picWrapper">
    				<ul class="pic-list" ref="picList">
    					<li class="pic-item" v-for="pic in seller.pics">
    						<img :src="pic" alt="" width="120" height="90">
    					</li>
    				</ul>
    			</div>	
    		</div>
    		<split></split>
    		<div class="info">
    			<h1 class="title">商家信息</h1>
    			<ul>
    				<li class="info-item" v-for="info in seller.infos">{{info}}</li>
    			</ul>
    		</div>
		</div>
	</div>
</template>
<script>
import BScroll from "better-scroll";
// import {saveToLocal, loadFromLocal} from "../../common/js/store.js";
import star from "../star/star";
import split from "../split/split";
	export default{
		data(){
			return{
				seller:"",
				favorite:false
			};
		},
		computed:{
			favoriteText(){
				return this.favorite?"已收藏":"收藏"
			}
		},
		created(){
		  	this.classMap=["decrease","discount","special","invoice","guarantee"]
		},
		methods:{
			getData(){
		      let vm=this
		      vm.axios.get('http://192.168.43.165:8081/static/data.json')
		      .then(function(response){
		        vm.seller=response.data.seller;
		        vm.$nextTick(()=>{
		        	vm.scroll=new BScroll(vm.$refs.seller,{
		        		click:true
		        	});
		        	vm._initPics()
		        })
		      })
		      .catch(function(error){
		        console.log(error);
		      });
		    },
		    _initPics(){
		    	if(this.seller.pics){
					let picWidth=120;
					let margin=6;
					let width=(picWidth+margin)*this.seller.pics.length-margin;
					this.$refs.picList.style.width=width+"px";
					this.$nextTick(()=>{
						this.picScroll=new BScroll(this.$refs.picWrapper,{
							scrollX:true,
							eventPassthrough:'vertical'
						})
					})
				}
		    },
		    toggleFavorite(event){
		    	if(!event._constructed){
		    		return;
		    	}
		    	this.favorite=!this.favorite;
		    	 // saveToLocal(this.seller.id, 'favorite', this.favorite);
		    }
		},
		mounted(){
		    this.getData()
		},
		components:{
			star,
			split
		}
	};
</script>
<style>
@import "../../common/style.css";
.seller{
	position: absolute;
	top:178px;
	left:0;
	bottom:0;
	width: 100%;
	overflow: hidden;
}
.seller .overview{
	position: relative;
	padding:18px;
}
.seller .overview .title{
	margin-bottom:8px;
	line-height: 14px;
	font-size: 14px;
	color: rgb(7, 17, 27);
}
.seller .overview .desc{
	padding-bottom: 18px;
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
	font-size: 0;
}
.seller .overview .desc .star{
	margin-right: 8px;
	display: inline-block;
	vertical-align: top;
}
.seller .overview .desc .text{
	display: inline-block;
	margin-right: 12px;
	vertical-align: top;
	line-height: 18px;
	font-size: 10px;
	color: rgb(77, 85, 93);
}
.seller .overview .remark{
	display: flex;
	padding-top: 18px;
}
.seller .overview .remark .block{
	flex: 1;
	text-align: center;
	border-right: 1px solid rgba(7, 17, 27, 0.1);
}
.seller .overview .remark .block:last-child{
	border: 0;
}
.seller .overview .remark .block h2{
	margin-bottom: 4px;
	line-height: 10px;
	font-size: 10px;
	color: rgb(147, 153, 159);
}
.seller .overview .remark .block .content {
	line-height: 24px;
	font-size: 10px;
	color:rgb(7, 17, 27);
}
.seller .overview .remark .block .content .stress{
	font-size: 24px; 
}
.seller .overview .favorite{
	position: absolute;
	right: 11px;
	top: 18px;
	width: 50px;
	text-align: center;
}
.seller .overview .favorite .icon-favorite{
	display: block;
	margin-bottom: 4px;
	line-height: 24px;
	font-size: 24px;
	color:#d4d6d9;
}
.seller .overview .favorite .icon-favorite.active{
	color: rgb(240, 20, 20);
}
.seller .overview .favorite .text{
	display: block;
	line-height: 10px;
	font-size: 10px;
	color: rgb(77, 85, 93);
}
.seller .bulletin{
	padding:0 18px;
}
.seller .bulletin .title{
	margin: 18px 0 8px 0;
	line-height: 14px;
	font-size: 14px;
	color: rgb(7, 17, 27);
}
.seller .bulletin .content-wrapper{
	padding: 0 12px 16px 12px;
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.seller .bulletin .content-wrapper .text{
	line-height: 24px;
	font-size: 12px;
	color: rgb(240, 20, 20);
}
.seller .supports{
	padding: 0 18px;
}
.seller .supports .support-item{
	padding: 16px 12px;
	font-size: 0;
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.seller .supports .support-item:last-child{
	border-bottom: 0px;
}
.seller .supports .support-item .icon{
	display: inline-block;
	vertical-align: top;
	width: 16px;
	height: 16px;
	background-repeat: no-repeat;
	background-size:16px 16px ;
	margin-right: 6px;
}
.seller .supports .support-item .icon.decrease{
	background-image: url('decrease_4@2x.png');
}
.seller .supports .support-item .icon.discount{
	background-image: url('discount_4@2x.png');
}
.seller .supports .support-item .icon.guarantee{
	background-image: url('guarantee_4@2x.png');
}
.seller .supports .support-item .icon.invoice{
	background-image: url('invoice_4@2x.png');
}
.seller .supports .support-item .icon.special{
	background-image: url('special_4@2x.png');
}
.seller .supports .support-item .text{
	line-height: 16px;
	font-size: 12px;
	color: rgb(7, 17, 27);
}
.seller .pics{
	padding: 18px;
}
.seller .pics .title{
	margin-bottom: 12px;
	line-height: 14px;
	font-size: 14px;
	color: rgb(7, 17, 27);
}
.seller .pics .pic-wrapper{
	width: 100%;
	overflow: hidden;
	white-space: nowrap;
}
.seller .pics .pic-wrapper .pic-list{
	font-size: 0;
}
.seller .pics .pic-wrapper .pic-list .pic-item{
	display: inline-block;
	margin-right: 6px;
	width: 120px;
	height: 90px;
}
.seller .pics .pic-wrapper .pic-list .pic-item:last-child{
	margin-right: 0;
}
.seller .info{
	padding: 0 18px;
}
.seller .info .title{
	padding:18px 0 12px 0;
	line-height: 14px;
	font-size: 14px;
	color: rgb(7, 17, 27);
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.seller .info .info-item{
	padding: 16px 12px;
	line-height: 16px;
	font-size: 12px;
	color: rgb(7, 17, 27);
	border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.seller .info .info-item:last-child{
	border: 0;
}
</style>
