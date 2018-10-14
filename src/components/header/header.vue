<template>
  <div class="headers">
    <div class="content-wrapper">
    	<div class="avatar">
    		<img src="http://static.galileo.xiaojukeji.com/static/tms/seller_avatar_256px.jpg" width="64" height="64" alt="">
    	</div>
    	<div class="content">
    		<div class="title">
    			<span class="brand"></span>
    			<span class="name">{{ seller.name }}</span>
    		</div>
    		<div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
    		<div class="supports" v-if="seller.supports">
    			<span class="icon" :class="classMap[seller.supports[0].type]"></span>
    			<span class="text">{{seller.supports[0].description}}</span>
    		</div>
    	</div>
    	<div v-if="seller.supports" class="support-count" @click="showDetail">
    		<span class="count">{{seller.supports.length}}个</span>
    		<i class="icon-keyboard_arrow_right"></i>
    	</div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
    	<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
    	<i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
    	<img :src="seller.avatar" width="100%" height="100%" alt="">
    </div>
    <div class="detail" v-show="detailShow">
    	<div class="detail-wrapper">
    		<div class="detail-main">
    			<h1 class="main-title">{{seller.name}}</h1>
    			<div class="star-wrapper">
    				<star :size="48" :score="seller.score"></star>	
    			</div>
    			<div class="title">
    				<div class="line"></div>
    				<div class="text">优惠信息</div>
    				<div class="line"></div>
    			</div>	
    			<ul v-if="seller.supports" class="supports">
    				<li v-for="(item,index) in seller.supports" class="support-item">
    					<span class="icon" :class="classMap[seller.supports[index].type]"></span>
    					<span class="text">{{seller.supports[index].description}}</span>
    				</li>
    			</ul>
    			<div class="title">
    				<div class="line"></div>
    				<div class="text">商家公告</div>
    				<div class="line"></div>
    			</div>	
    			<div class="bulletin">
    				<p>{{seller.bulletin}}</p>
    			</div>		
    		</div>
    	</div>
    	<div class="detail-close">
    		<span class="close" @click="closeDetail" ><i class="icon-close"></i></span>
    	</div>
    </div>
  </div>
</template>

<script>
import star from '../star/star';
export default {
  name: 'Header',
    data () {
    return {
      seller: '',
      detailShow:false

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

      })
      .catch(function(error){
        console.log(error);
      });
    },
    showDetail(){
    	this.detailShow=true
    },
    closeDetail(){
    	this.detailShow=false
    }
  },
  mounted(){
    this.getData()
  },
  components:{
  	star
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import "../../common/style.css";
.headers{
	color:#fff;
	position: relative;
	overflow: hidden;
	background:rgba(7,17,27,0.5);
}
.headers .content-wrapper{
	padding:24px 12px 18px 24px;
	position: relative;
}
.headers .content-wrapper .avatar{
	display: inline-block;
}
.headers .content-wrapper .avatar img{
	border-radius: 2px;
}
.headers .content-wrapper .content{
	display: inline-block;
	margin-left: 16px;
	font-size: 14px;
}
.headers .content-wrapper .content .title{
	margin:2px 6px 8px 0px;
}
.headers .content-wrapper .content .title .brand{
	display: inline-block;
	vertical-align: top;
	width: 30px;
	height: 18px;
	background-image: url('brand@2x.png');
	background-repeat: no-repeat;
	background-size:30px 18px ;
}
.header .content-wrapper .content .title .name{
	display: inline-block;
	font-size: 16px;
	font-weight: bold;
	line-height: 18px;
}
.headers .content-wrapper .content .description{
	margin-bottom: 10px;
	font-size: 12px;
	line-height: 12px;
}
.headers .content-wrapper .content .supports .icon{
	display: inline-block;
	vertical-align: top;
	width: 12px;
	height: 12px;
	background-repeat: no-repeat;
	background-size:12px 12px ;
}
.headers .content-wrapper .content .supports .icon.decrease{
	background-image: url('decrease_1@2x.png');
}
.headers .content-wrapper .content .supports .icon.discount{
	background-image: url('discount_1@2x.png');
}
.headers .content-wrapper .content .supports .icon.guarantee{
	background-image: url('guarantee_1@2x.png');
}
.headers .content-wrapper .content .supports .icon.invoice{
	background-image: url('invoice_1@2x.png');
}
.headers .content-wrapper .content .supports .icon.special{
	background-image: url('special_1@2x.png');
}
.headers .content-wrapper .content .supports .text{
	vertical-align: top;
	font-size: 10px;
	line-height: 12px;
}

.headers .content-wrapper .support-count{
	position: absolute;
	right:12px;
	bottom:14px;
	padding:0 8px;
	height: 24px;
	line-height: 24px;
	border-radius: 14px;
	background: rgba(0,0,0,0.2);
	text-align: center;	
}
.headers .content-wrapper .content .support-count .count{
	font-size: 10px;
}
.headers .content-wrapper .content .support-count .icon-keyboard_arrow_right{
	vertical-align: top;
	padding-top: 6px;
}
.headers .bulletin-wrapper{
	padding:0px 22px 0px 12px;
	height: 28px;
	line-height: 28px;
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
	background:rgba(7,17,27,0.5)
	/*font-size: 0;*/
}
.headers .bulletin-wrapper .bulletin-title{
	display: inline-block;
	vertical-align: top;
	margin-top: 8px;
	width: 22px;
	height: 12px;
	line-height: 28px;
	background-image: url('bulletin@2x.png');
	background-repeat: no-repeat;
	background-size:22px 12px ;
}
.headers .bulletin-wrapper .bulletin-text{
	vertical-align: top;
	margin:0 4px;
	font-size: 10px;
}
.headers .background{
	position: absolute;
	top:0;
	left:0;
	width: 100%;
	height:100%;
	z-index:-1;
	filter:blur(10px);
}
.headers .detail{
	position: fixed;
	z-index: 100;
	top: 0;
	left:0;
	width: 100%;
	height:100%;
	overflow: auto;
	background: rgba(7,17,27,0.8);
	/*filter:blur(10px);*/
} 
.headers .detail .detail-wrapper{
	min-height: 100%;
	width: 100%
}
.headers .detail .detail-wrapper .detail-main{
	margin-top: 64px;
	padding-bottom: 64px;
}
.headers .detail .detail-wrapper .detail-main .main-title{
	font-size: 16px;
	line-height: 16px;
	font-weight: 700;
	text-align: center;
}
.headers .detail .detail-wrapper .detail-main .star-wrapper{
	height:48px;
	margin-top: 18px;
	padding:2px 0;
	text-align: center;
	line-height: 48px;
}
.headers .detail .detail-wrapper .detail-main .title{
	display: flex;
	width: 80%;
	margin:28px auto 24px auto;
}
.headers .detail .detail-wrapper .detail-main .title .line{
	flex: 1;
	position: relative;
	top:-6px;
	border-bottom: 1px solid rgba(255,255,255,0.2);
}
.headers .detail .detail-wrapper .detail-main .title .text{
	padding:0 12px;
	font-size: 14px;
	font-weight: 700;
}
.headers .detail .detail-wrapper .detail-main .supports{
	width: 80%;
	margin:0 auto;
}
.headers .detail .detail-wrapper .detail-main .supports .support-item{
	padding: 0 12px;
	margin-bottom: 12px;
	font-size: 0;
}
.headers .detail .detail-wrapper .detail-main .supports .support-item:last-child{
	margin-bottom: 0px;
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .icon{
	display: inline-block;
	vertical-align: top;
	width: 16px;
	height: 16px;
	background-repeat: no-repeat;
	background-size:16px 16px ;
	margin-right: 6px;
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .icon.decrease{
	background-image: url('decrease_2@2x.png');
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .icon.discount{
	background-image: url('discount_2@2x.png');
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .icon.guarantee{
	background-image: url('guarantee_2@2x.png');
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .icon.invoice{
	background-image: url('invoice_2@2x.png');
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .icon.special{
	background-image: url('special_2@2x.png');
}
.headers .detail .detail-wrapper .detail-main .supports .support-item .text{
	font-size: 12px;
	line-height: 12px;
}
.headers .detail .detail-wrapper .detail-main .bulletin{
	width: 80%;
	font-size: 12px;
	line-height: 24px;
	margin:0 auto;
}
.headers .detail .detail-wrapper .detail-main .bulletin p{
	padding:0 12px; 
}
.headers .detail .detail-close{
	position: relative;
	width: 32px;
	height: 32px;
	font-size: 32px;
	margin:-64px auto 0 auto;
	clear: both;
	/*text-align: center;*/
}
</style>
