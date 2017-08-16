<template>
  <scroller>
  <div class="wrapper account">
    <div class="title"><text class="text_title">我的账户</text></div>
    <div class="info">
      <div class="avatar" @click="login()">
        <image class="user_img" :src="user_img"></image>
      </div>
      <div class="detail">
        <div><text class="text_detail">账号：{{userName}}</text></div>
        <div><text class="text_detail">姓名：{{user_fullName}}</text></div>
        <div><text class="text_detail">昵称：{{user_givenName}}</text></div>
      </div>
    </div>
   <!--  <div class="menu">
      <div class="menu_item" v-for="item in menuList">
        <text>{{item.name}}</text>
      </div>
    </div> -->
    <div><text class="text_detail">{{resData}}</text></div>
  </div>
  </scroller>
</template>

<style>
  .wrapper{
    background-color: #EEE;
  }
  .title{
    width: 750px;
    height: 100px;
    background-color: #F50;
    justify-content: center;
    align-items: center;
  }
  .text_title{
    color:#FFF;
    font-size: 40px;
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

  export default {
    data () {
      return {
        user_serviceId: '',
        user_img: 'http://m.ry600.com/files/User/2j/2jf5qju123movd7q/photo.jpg',
        user_givenName: 'null',
        user_fullName: 'null',
        userName: 'null',
        warningMsg: '',
        resData: 'resData',
        postResult: 'aaa',
        serviceId:'',
        menuList: [{'name':'我的订单'},{'name':'我的退换货'},{'name':'订购申请'},{'name':'我的账簿'},{'name':'收货地址'},{'name':'我的评价'},{'name':'我的留言'},{'name':'我的收藏'}]
      }
    },
    methods: {
      login () {
        console.log("kkk");
        let vm = this;
        let psd = "e10adc3949ba59abbe56e057f20f883e";
        stream.fetch({
          method: 'GET',
          type: 'json',
          url: 'https://www.yr600.com/fish/login?userName=testgs&password=' + psd + '&_=' + new Date().getTime()
        }, function (res) {
          if (res.ok) {
            if (res.data.success) {
              // 登录成功
              vm.warningMsg = '登录成功';
              storage.setItem('serviceId', res.data._serviceId, event => {
                 storage.getItem('serviceId', event => {
                    console.log('get value:', event.data);
                    vm.user_serviceId = event.data;
                    vm.getData();
                 });
              });
            } else {
              // 登录失败
              vm.warningMsg = res.data.message;
            }
          } else {
            vm.warningMsg = '网络错误';
          }
        });
      },
      getData () {
      const vm = this;
      // vm.user_serviceId = "74081028a7b84ed7ae9f37002be87916";
      console.log(vm.user_serviceId);
      stream.fetch({
        headers: {'Cookie':'_serviceId=' + vm.user_serviceId},
        method: 'POST',
        type: 'json',
        url: 'https://www.yr600.com/jsonaction/websiteaction.action',
        body: 'action=VSUser.getBasicInfo'
      }, function (res) {
        if(!res.ok){
          vm.postResult = "request failed";
        }else{
          let info = res.data.results[0];
          vm.resData = info;
          console.log(info);
          vm.user_img = "http://m.ry600.com/" + info.photoUrl;
          vm.userName = info.userName;
          vm.user_fullName = info.fullName;
          vm.user_givenName = info.givenName;
          console.log(weex.config.bundleUrl);
          console.log(vm.$getConfig().bundleUrl);
          console.log(info);
        }
      }, function (err) {
      });
    }
  }
  }
</script>
