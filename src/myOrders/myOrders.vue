<template>
  <scroller>
    <div class="wrapper">
    	<ry-header title="我的订单"></ry-header>
    	<div class="total"><text>共 {{total}} 张订单</text></div>
    	<div class="bill" v-for="order in orders" v-on:click="orderDetail(order.billId, order.payment, true)">
    		<div class="order_top">
    			<text>{{order.billId}}</text>
    			<text class="order_state">{{order.state}}</text>
    		</div>
    		<div class="order_cnt">
	    		<div><image class="pdt_img" :src="order.products[0].img"></image></div>
	    		<div class="order_info">
	    			<text class="order_text">{{order.seller}}</text>
	    			<text class="order_text">{{order.date}}</text>
	    			<text class="order_text">{{order.payState}}</text>
	    		</div>
	    	</div>
    		<div class="order_bottom">
    			<text>共 {{order.products.length}} 件</text>
    			<text class="money">{{order.payment}}</text>
	    	</div>
    	</div>
    	<div class="order_detail" v-if="isShow" :style="{height:Height}">
          <scroller>
      		<div class="detail_title"><text class="detail_text">—— 订单详情 ——</text></div>
      		<div class="detail_cnt">
      			<div class="detail_info">
      				<div class="bill_id">
                <text class="text">单号：{{detail.bill[0]}}</text><text class="text bill_state">{{detail.bill[2]}}</text>
              </div>
      				<div><text class="text">时间：{{detail.bill[1]}}</text></div>
      				<div><text class="text">{{detail.sellerName}}</text></div>
      			</div>
      			<div class="detail_info">
      				<div><text class="text">收货信息：{{detail.address}}</text></div>
      				<div class="bill_phone">
                <text class="text">{{detail.mobile}}</text><text class="text shipping_method">{{detail.payType}}</text>
              </div>
      			</div>
      			<div class="detail_info">
      				<div><text class="text">发票信息：{{detail.invoice}}</text></div>
      			</div>
      			<div class="detail_info">
      				<div><text class="text">支付状态：{{detail.payInfo}}</text></div>
      				<div class="bill_count"><text class="text">订单总价：</text><text class="bill_payment">{{payment}}</text></div>
      			</div>
      			<div class="pdts_list">
              <div class="list_title"><text class="text">订单商品</text></div>
              <div v-for="pdt in pdts" class="pdt">
                <div class="pdt_left"><text class="text_2">{{pdt.productName}}</text></div>
                <div class="pdt_right">
                  <div><text class="text_2 pdt_amount">×{{pdt.amount}}{{pdt.packUnits}}</text></div>
                  <div><text class="text_2 pdt_price">￥{{pdt.price}}</text></div>
                </div>
              </div>
      			</div>
      		</div>
          <div class="close"><text class="text close_text" v-on:click="showHide(false)">×</text></div>
        </scroller>
    	</div>
    </div>
  </scroller>
</template>

<script>
  const stream = weex.requireModule('stream')
  const storage = weex.requireModule('storage')

  export default {
    data: {
        resData: 'resData',
        serviceId: '',
        total: '0',
        orders: [],
        detail: {'bill':[]},
        pdts: [],
        isShow: false,
        payment: 0,
        Height: 0
    },
    components: {
      ryHeader:require('../../components/ry-header.vue')
    },
    methods: {
    	orderDetail (id, payment, isShow) {
    		const vm = this;
        vm.detail= {'bill':[]};
        vm.pdts = [];
    		vm.showHide(isShow);
    		vm.payment = payment;
    		storage.getItem('serviceId', event => {
        vm.serviceId = event.data;
        stream.fetch({
          method: 'GET',
          type: 'json',
          url: 'https://www.yr600.com/test/orderitem?billId=' + id + '&serviceId=' + vm.serviceId
        }, function (res) {
          if(!res.ok){
              vm.postResult = "request failed";
          }else{
            vm.detail = res.data;
            vm.pdts = res.data.products;
            console.log(res.data);
          }
        }, function (err) {
        });
      });
    	},
      showHide (isShow) {
        this.isShow = isShow;
      }
    },
    created () {
      const vm = this;
      storage.getItem('serviceId', event => {
        vm.serviceId = event.data;
        stream.fetch({
          method: 'GET',
          type: 'json',
          url: 'https://www.yr600.com/test/order?serviceId=' + vm.serviceId
        }, function (res) {
          if(!res.ok){
              vm.postResult = "request failed";
          }else{
          	vm.total = res.data.total;
            vm.orders = res.data.data;
            console.log(res.data);
          }
        }, function (err) {
        });
      });
      let env = weex.config.env;
      vm.Height = 750/env.deviceWidth*env.deviceHeight - 150;
    }
  }
</script>

<style>
  .wrapper{
    background-color: #DDD;
    flex-direction: column;
    align-items: center;
    padding-top: 10px;
    padding-bottom: 10px;
  }
  .total {
    width: 710px;
    height: 50px;
    background-color: #FFF;
    align-items: center;
    justify-content: center;
  }
  .bill {
    width: 710px;
    margin-top: 10px;
    background-color: #FFF;
  }
  .order_top, .order_bottom {
    height: 80px;
    flex-direction: row;
    align-items: center;
    padding-left: 20px;
  }
  .order_bottom {
    justify-content: flex-end;
    padding-right: 20px;
  }
  .order_state {
    margin-left: 20px;
    background-color: #F50;
    color: #FFF;
    border-radius: 10px;
    padding-left: 5px;
    padding-right: 5px;
    padding-top: 5px;
    padding-bottom: 5px;
  }
  .order_cnt {
    background-color: #F5F5F5;
    padding-left: 20px;
    padding-right: 20px;
    flex-direction: row;
    align-items: center;
    height: 190px;
  }
  .pdt_img {
    width: 150px;
    height: 150px;
    border-width: 2px;
    border-color: #EEE;
  }
  .order_info {
    margin-left: 20px;
    width: 500px;
  }
  .order_text {
    width: 500px;
    overflow: hidden;
    height: 50px;
    line-height: 43px;
  }
  .money {
    color: #F50;
    font-size: 40px;
    margin-left: 20px;
  }
  .order_detail {
    width: 750px;
    background-color: rgba(7, 17, 27, 0.8);
    position: fixed;
    top: 150px;
    left: 0;
    padding-left: 40px;
    padding-right: 40px;
    padding-bottom: 150px;
    flex-direction:column;
  }
  .detail_title {
    align-items: center;
    margin-top: 30px;
    margin-bottom: 20px;
  }
  .detail_text{
    color: #FFF;
    font-size: 40px;
  }
  .text {
    color:#FFF;
    font-size: 36px;
    line-height: 45px;
  }
  .text_2 {
    color: #222;
    font-size: 36px;
    line-height: 45px;
  }
  .close {
    position: fixed;
    bottom: 30px;
    flex-direction: row;
    justify-content: center;
    width: 750px;
  }
  .close_text {
    font-size: 100px;
    line-height: 100px;
  }
  .bill_id, .bill_count {
    flex-direction: row;
    align-items: center;
  }
  .bill_state {
    background-color: rgba(255,85,0,0.6);
    padding-left: 10px;
    padding-right: 10px;
    border-radius: 20px;
    margin-left: 20px;
  }
  .detail_info {
    border-bottom-width: 2px;
    border-color: rgba(255,255,255,0.3);
    margin-bottom: 20px;
    padding-bottom: 20px;
  }
  .bill_phone {
    flex-direction: row;
    justify-content: space-between;
  }
  .bill_payment {
    color: #F50;
    font-size: 50px;
  }
  .list_title {
    align-items: center;
    margin-bottom: 10px;
  }
  .pdt {
    flex-direction: row;
    background-color: rgba(255,255,255,0.8);
    margin-bottom: 20px;
    padding-left: 20px;
    padding-right: 20px;
    padding-top: 20px;
    padding-bottom: 20px;
  }
  .pdt_left {
    flex: 5;
  }
  .pdt_right {
    flex: 2;
    align-items: flex-end;
    justify-content: space-between;
  }
  .pdt_amount {
    color: #666;
  }
  .pdt_price {
    font-size: 40px;
    color: #F50;
  }
</style>
