<template>
	<div>
		<div class="header" :style="{backgroundColor: backgroundColor, height: height}">
			<image :src="icon_left" class="icon" @click="pop"></image>
			<text :style="{fontSize:fontSize, color: color, fontWeight: fontWeight}">{{title}}</text>
			<image :src="icon_right" class="icon"></image>
		</div>
		<div class="blank" :style="{height:height}"></div>
	</div>
</template>
<style>
	.header {
		width: 750px;
		position: fixed;
		top: 0;
		padding-top: 50px;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
	}
	.blank {
		width: 750px;
	}
	.icon {
		width: 35px;
		height: 50px;
		margin-left: 30px;
	}
</style>
<script>
  const navigator = weex.requireModule('navigator')
	module.exports = {
		props: {
			backgroundColor: { default: '#F50' },
			height: { default: '150px' },
			title: { default: '荣药在线' },
			icon_left: { default: 'http://file.ry600.com/snapshot//files/di/di70684438lrfavs/2017-07-14/6aprilsm5xkr0qmo.png' },
			icon_right: {},
			fontSize:{ default: '45px' },
			color:{ default: '#FFF' },
			fontWeight: { default: 'bold' }
		},
		methods: {
			pop () {
				navigator.pop({'animated': 'true'});
			}
		}
	}
</script>