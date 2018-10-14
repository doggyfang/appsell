<template>
	<div class="goods">
		<div class="menu-wrapper" ref="menuWrapper">
			<ul>
				<li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
					<span class="text">
					    <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<div class="food-wrapper" ref="foodWrapper">
			<ul>
				<li  v-for="item in goods" class="food-list food-list-hook" >
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item">
							<div class="icon">
								<img :src="food.icon" alt="" width="57" height="57">
							</div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="deccription">{{food.description}}</p>
								<div class="extra">
									<span>月售{{food.sellCount}}份</span>
									<span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span class="now">￥{{food.price}}</span>
									<del class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</del>
								</div>
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food" @cart-add="cartAdd"></cartcontrol>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<shopcart ref="shopcart" :select-foods="selectFoods"></shopcart>
		<food :food="selectedFood" ref="food"></food>
	</div>
	
</template>
<script>
	import BScroll from "better-scroll";
	import shopcart from "../shopcart/shopcart";
	import cartcontrol from "../cartcontrol/cartcontrol";
	import food from "../food/food";

	export default{
		props:{
			seller:{
				type:Object
			}
		},
		data(){
			return{
				goods:[],
				listHeight:[],
				scrollY:0,
				selectedFood:{}
			}
		},
		computed:{
			currentIndex(){
				for(let i=0;i<this.listHeight.length;i++){
					let height1=this.listHeight[i];
					let height2=this.listHeight[i+1];
					if((this.scrollY>=height1 && this.scrollY<height2) || !height2){
						console.log(i)
						return i;
					}
				}
				return 0;	
			},
			selectFoods(){
				let foods=[];
				this.goods.forEach((good)=>{
					good.foods.forEach((food)=>{
						if(food.count){
							foods.push(food);
						}
					})// statements
				});
				return foods;
			}
		},
		created(){
		  	this.classMap=["decrease","discount","special","invoice","guarantee"]
		},
		methods:{
		    getData(){
		      let vm=this;
		      vm.axios.get('http://192.168.43.165:8081/static/data.json')
		      .then(function(response){
		        vm.goods=response.data.goods;
		        vm.$nextTick(function () {
       			  	vm._initScroll();
       			  	vm._calculateHeight();
       			  	// => '更新完成'
     			})
		       }) 
		      .catch(function(error){
		        console.log(error);
		      });
		    },
		    selectMenu(index,event){
		    	if(!event._constructed){
		    		return
		    	};
		    	let foodList=this.$refs.foodWrapper.getElementsByClassName("food-list-hook");
		    	let el=foodList[index];
		    	this.foodScroll.scrollToElement(el,30); 
		    	// console.log(index)
		    },
		    selectFood(food,event){
		    	if(!event._constructed){
		    		return;
		    	};
		    	this.selectedFood=food;
		    	this.$refs.food.show();
		    },
		 //    cartAdd (el) {		　  
			//     // 调用shopcart组件的drop()函数
			// 	this.$nextTick(() => {
			// 		this.$refs['shopcart'].drop(el);　//调用shopcart组件的drop()函数
			// 	});
			// },
			cartAdd(target) {
		        this._drop(target);
		     },
		     _drop(target) {
		        // 体验优化,异步执行下落动画
		        this.$nextTick(() => {
		          this.$refs.shopcart.drop(target);
		        });
		     },

		    _initScroll(){
		    	this.menuScroll=new BScroll(this.$refs.menuWrapper,{
		    		click:true
		    	});
		    	this.foodScroll=new BScroll(this.$refs.foodWrapper,{
		    		click:true,
		    		probeType:3
		    	});
		    	this.foodScroll.on('scroll',(pos)=>{
		    		this.scrollY=Math.abs(Math.round(pos.y));
		    	})
		    },
		    _calculateHeight(){
		    	let foodList=this.$refs.foodWrapper.getElementsByClassName("food-list-hook");
		    	let height=0;
		    	this.listHeight.push(height);
		    	for(let i = 0;i <foodList.length;i++){
		    		let item=foodList[i];
		    		height+=item.clientHeight;
		    		this.listHeight.push(height);
		    	}
		    }

		},
		mounted(){
		    this.getData()
  		},
  		components:{
  			shopcart,
  			cartcontrol,
  			food
  		}
	};
</script>
<style scoped>
.goods{
	display: flex;
	position: absolute;
	top:178px;
	bottom:46px;
	overflow: hidden;
}
.goods .menu-wrapper{
	flex: 0 0 80px;
	width: 80px;
	background: #f3f5f7;
    }
	.goods .menu-wrapper .menu-item{
		display: table;
		height: 54px;
		line-height: 14px;
		padding:0 12px;
		border-bottom: 1px solid rgba(7,17,27,0.1);
	}
	.goods .menu-wrapper .menu-item.current{
		background: #fff;
		margin-top: -1px;
		font-weight: 700;
		z-index: 10;
		border-bottom: 0;
	}
	.goods .menu-wrapper .menu-item .icon{
		display: inline-block;
		vertical-align: top;
		width: 12px;
		height: 12px;
		background-repeat: no-repeat;
		background-size:12px 12px ;
		margin-right: 2px;
	}
	.goods .menu-wrapper .menu-item .icon.decrease{
		background-image: url('decrease_3@2x.png');
	}
	.goods .menu-wrapper .menu-item .icon.discount{
		background-image: url('discount_3@2x.png');
	}
	.goods .menu-wrapper .menu-item .icon.guarantee{
		background-image: url('guarantee_3@2x.png');
	}
	.goods .menu-wrapper .menu-item .icon.invoice{
		background-image: url('invoice_3@2x.png');
	}
	.goods .menu-wrapper .menu-item .icon.special{
		background-image: url('special_3@2x.png');
	}
	.goods .menu-wrapper .menu-item .text{
		display: table-cell;
		width: 56px;
		vertical-align: middle;
		font-size: 12px;
		
	}
.goods .food-wrapper{
	flex: 1;
}
.goods .food-wrapper .food-list .title{
	padding-left: 14px;
	font-size: 12px;
	height: 26px;
	background: #f3f5f7;
	color:rgb(147,153,159);
	line-height: 26px;
	border-left: 2px solid #d9dde1;
}
.goods .food-wrapper .food-list .food-item{
	display: flex;
	margin-top:18px;
	border-bottom: 1px solid rgba(7,17,27,0.1);
	padding-bottom: 18px;
	padding-right: 18px;
}
.goods .food-wrapper .food-list .food-item:last-child{
	border-bottom:0;
	margin-bottom: 0;
}
.goods .food-wrapper .food-list .food-item .icon{
	flex: 0 0 57px;
	margin-left: 18px;
}
.goods .food-wrapper .food-list .food-item .content{
	position: relative;
	flex: 1;
	margin-left: 10px;
}
.goods .food-wrapper .food-list .food-item .content .name{
	font-size: 14px;
	line-height: 14px;
	margin: 2px 0 8px 0;
}
.goods .food-wrapper .food-list .food-item .content .deccription{
	margin-bottom:8px;
	color:rgb(147,153,159);
	font-size: 10px;
	line-height: 12px;
}
.goods .food-wrapper .food-list .food-item .content .extra{
	margin-bottom:8px;
	color:rgb(147,153,159);
	font-size: 10px;
	line-height: 10px;
}
.goods .food-wrapper .food-list .food-item .content .extra span{
 	margin-right:12px;
}
.goods .food-wrapper .food-list .food-item .content .price{
		line-height: 24px;
		font-weight: 700;
}
.goods .food-wrapper .food-list .food-item .content .price .now{
	font-size: 14px;
	color:rgb(240,20,20);
}
.goods .food-wrapper .food-list .food-item .content .price .old{
	font-size: 10px;
	color:rgb(147,153,159);
}
.goods .food-wrapper .food-list .food-item .content .cartcontrol-wrapper{
	position: absolute;
	right:0;
	bottom:0;
}
</style>
