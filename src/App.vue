<template>
  <div id="app">
    <!-- <img src="./assets/logo.png"> -->
    <v-header></v-header>
    <div class="tabs">
      <div class="item-tabs">
        <router-link :to="'/goods'">商品</router-link>
      </div>
      <div class="item-tabs">
        <router-link :to="'/ratings'">评论</router-link>
      </div>
      <div class="item-tabs">
        <router-link :to="'/seller'">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view />
    </keep-alive>
  </div>
</template>

<script>
import Header from './components/header/header';
// import {urlParse} from './common/js/util.js';
export default {
  name: 'App',
  // data() {
  //     return {
  //       seller: {
  //         id: (() => {
  //           let queryParam = urlParse();
  //           return queryParam.id;
  //         })()
  //       }
  //     };
  //   },
  //  methods:{
  //     getData(){
  //       this.axios.get('http://localhost:8080/static/data.json'+ '?id=' + this.seller.id)
  //     .then(function(response){
  //       this.seller = Object.assign({}, this.seller, response.data.seller);
  //     })
  //     .catch(function(error){
  //       console.log(error);
  //     });
  //     }  
  //   },
  //   mounted(){
  //       this.getData()
  //   },
  components: {
      'v-header': Header
    }
};
</script>

<style>
#app .tabs{
  display: flex;
  width: 100%;
  height: 40px;
  line-height: 40px;
  text-align:center;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
  /*border-1px(rgba(7, 17, 27, 0.1));*/
 }
 .item-tabs{
  flex:1;
  display: block;
  font-size: 14px;
 }
 .item-tabs .router-link-active{
  color: rgb(240, 20, 20);
 }
</style>
