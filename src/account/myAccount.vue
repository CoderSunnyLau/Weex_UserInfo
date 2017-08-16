<template>
  <scroller>
    <div class="wrapper account">
      <ry-header title="我的账户"></ry-header>
      <div class="info">
        <div class="avatar">
          <image class="user_img" :src="user_img"></image>
        </div>
        <div class="detail">
          <div><text class="text_detail">账号：{{userName}}</text></div>
          <div><text class="text_detail">姓名：{{user_fullName}}</text></div>
          <div><text class="text_detail">昵称：{{user_givenName}}</text></div>
        </div>
      </div>
      <div class="menu">
        <div class="menu_item" v-for="item in menuList" @click="menu(item.href)">
          <text>{{item.name}}</text>
        </div>
      </div>
    </div>
  </scroller>
</template>

<style>
  .wrapper{
    background-color: #EEE;
  }
  .info{
    width: 750px;
    height: 200px;
    background-color: #FFF;
    flex-direction: row;
    align-items: center;
  }
  .avatar{
    margin-right: 20px;
    margin-left: 20px;
  }
  .user_img{
    width: 150px;
    height: 150px;
    border-radius: 75px;
  }
  .text_detail{
    line-height: 50px;
  }
  .menu{
    width: 750px;
    background-color: #FFF;
    margin-top: 20px;
  }
  .menu_item{
    height: 100px;
    border-width: 2px;
    border-color: #EEE;
    flex-direction: row;
    align-items: center;
    padding-left: 20px;
    padding-right: 20px;
  }
</style>

<script>
  const stream = weex.requireModule('stream')
  const storage = weex.requireModule('storage')
  const navigator = weex.requireModule('navigator')

  export default {
    data: {
      user_serviceId: '',
      user_img: 'http://m.ry600.com/files/User/2j/2jf5qju123movd7q/photo.jpg',
      user_givenName: '...',
      user_fullName: '...',
      userName: '...',
      warningMsg: '',
      resData: 'resData',
      postResult: 'aaa',
      serviceId:'',
      menuList: [{'name':'我的订单','href':'myOrders'},{'name':'我的退换货'},{'name':'订购申请'},{'name':'我的账簿'},{'name':'收货地址'},{'name':'我的评价'},{'name':'我的留言'},{'name':'我的收藏'}]
    },
    components: {
      ryHeader:require('../../components/ry-header.vue')
    },
    methods: {
      menu (href) {
        let vm = this;
        href = vm.$getConfig().bundleUrl.split('/').slice(0,-1).join('/')+'/' + href + '.weex.js';
        let params = {
          'url': href,
          'animated' : 'true',
        }
        navigator.push(params,function(){});
      }
    },
    created () {
      const vm = this;
      storage.getItem('serviceId', event => {
        vm.user_serviceId = event.data;
        stream.fetch({
          headers: {'Cookie':'_serviceId=' + event.data},
          method: 'POST',
          type: 'json',
          url: 'http://www.ry600.pub/jsonaction/websiteaction.action',
          body: 'action=VSUser.getBasicInfo'
        }, function (res) {
          if(!res.ok){
              vm.postResult = "request failed";
          }else{
            let info = res.data.results[0];
            vm.user_img = "http://m.ry600.com/" + info.photoUrl;
            vm.userName = info.userName;
            vm.user_fullName = info.fullName;
            vm.user_givenName = info.givenName;
          }
        }, function (err) {
        });
      });
    }
  }
</script>
