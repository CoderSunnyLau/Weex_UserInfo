<template>
  <div class="wrapper">
    <ry-header title="登录"></ry-header>
    <image class="mainPic" v-bind:src="mainSrc"></image>
    <div class="login-wrapper">
      <div><text class="warning-msg">{{warningMsg}}</text></div>
      <div class="login-row"><input class="input" type="text" placeholder="用戶名" v-model="userName"/></div>
      <div class="login-row"><input class="input" type="password" placeholder="密碼" v-model="password"/></div>
    </div>
    <div class="login-btn" v-on:click="login"><text class="login-text">立即登录</text></div>
  </div>
</template>

<style>
  .wrapper{
    flex-direction: column;
    align-items: center;
  }
  .mainPic{
    width: 750px;
    height: 220px;
  }
  .login-wrapper{
    margin-top: 100px;
    flex-direction: column;
    align-items: center;
  }
  .warning-msg{
    font-size: 35px;
    height: 80px;
    margin-bottom: 20px;
    color: red;
    line-height: 80px;
  }
  .login-row{
    width: 750px;
    height: 100px;
    margin-bottom: 10px;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }
  .input{
    width: 550px;
    height: 100px;
    border-width: 1px;
    border-color: #CCC;
    border-radius: 10px;
    padding-left: 20px;
    font-size: 35px;
  }
  .login-btn{
    width: 550px;
    height: 100px;
    margin-top: 50px;
    background-color: #F50;
    justify-content: center;
    align-items: center;
    border-radius: 10px;
  }
  .login-text{
    color: #FFF;
    font-size: 40px;
  }
</style>

<script>
  import md5 from './md5.js'
  const stream = weex.requireModule('stream')
  const storage = weex.requireModule('storage')
  const navigator = weex.requireModule('navigator')

  export default {
    data () {
      return {
        mainSrc: 'http://m.ry600.com/snapshot/vms/site/di/di70684438lrfavs/82ojywir5vqogktm/image/login_ad.jpg',
        userName: '',
        password: '',
        warningMsg: ''
      }
    },
    components: {
      ryHeader: require('../../components/ry-header.vue')
    },
    methods: {
      login: function () {
        const vm = this;

        // 登录校验
        if (vm.userName == '') {
          vm.warningMsg = '请输入用戶名';
          return false;
        } else if (vm.password == '') {
          vm.warningMsg = '请输入密码';
          return false;
        } else {
          vm.warningMsg = '';
        }

        let psd = md5(vm.password);
        // 登录
        stream.fetch({
          method: 'GET',
          type: 'json',
          url: 'https://www.yr600.com/test/login?userName=' + vm.userName + '&password=' + psd + '&_=' + new Date().getTime()
        }, function (res) {
          if (res.ok) {
            if (res.data.success) {
              // 登录成功
              vm.warningMsg = '登录成功';
              storage.setItem('serviceId', res.data._serviceId, event => {
                 storage.getItem('serviceId', event => {
                 });
              });

              // 页面跳转
              let url = vm.$getConfig().bundleUrl;
              url = url.split('/').slice(0,-1).join('/')+'/account.weex.js';
              let params = {
                'url': url,
                'animated' : 'true',
              }
              navigator.push(params,function(){});
            } else {
              // 登录失败
              vm.warningMsg = res.data.message;
            }
          } else {
            vm.warningMsg = '网络错误';
          }
        });
      }
    }
  }
</script>
