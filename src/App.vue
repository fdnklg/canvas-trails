<template>
	<div id="app">
		<div class="vis-wrapper">
			<SvgText :data="randomData" :width="config.width" :height="config.height"/>
			<VectorCanvas :data="randomData" :config="config" :factor="isRetina"/>
		</div>
	</div>
</template>

<script>
	import VectorCanvas from './components/VectorCanvas.vue';
	import SvgText from './components/SvgText.vue';

	import { range as d3Range } from 'd3';

	const conf = {
		'width': 900,
		'height': 900,
		'pointsWidth': 2,
		'pointsMargin': 25,
	}

	export default {
	name: 'app',
	components: {
		VectorCanvas,
		SvgText
	},
	computed: {
		isRetina() {
			return (window.devicePixelRatio > 1 ? 2 : 1);
		},
		config() {
			return conf;
		},
		randomData() {
			let arr = [];
			d3Range(1000).forEach((index) => {
				arr.push({
					value: Math.random(),
					index: index,
					name: `item ${index}`
				})
			});

			const arrPos = this.gridLayout(arr, conf.pointsWidth + conf.pointsMargin, conf.width, conf.height);
			const arrNetwork = this.networks(arrPos);
			return arrNetwork;
		},
	},
	methods: {
		gridLayout(points, pointsWidth, gridWidth) {
			const pointsHeight = pointsWidth;
			const pointsPerRow = Math.floor(gridWidth/pointsWidth);

			points.forEach((point, i) => {
				point.x = pointsWidth * (i % pointsPerRow);
				point.y = pointsHeight * Math.floor(i / pointsPerRow);
			});

			return points;
		},
		networks(points) {
			const numNetworks = 5;
			const networkSize = 20;

			let arr = [];

			d3Range(numNetworks).forEach((i) => {
				let randomPointStart = this.randomPoint(points);
				let randomNetworkSize = Math.floor(networkSize * Math.random());

				for (let index = 0; index <= randomNetworkSize; index++) {
					let randomPointEnd = this.randomPoint(points);

					arr.push({
						startX: randomPointStart.x,
						startY: randomPointStart.y,
						endX: randomPointEnd.x,
						endY: randomPointEnd.y,
						value: Math.random(),
						index: i,
						name: `item ${i}`
					});
				}
			});

			return arr;
		},
		randomPoint(arr) {
			const randomIndex = Math.floor(arr.length * Math.random());
			return arr[randomIndex];
		}
	},
	mounted() {
	}
	}
</script>

<style lang="scss">
	@import "./style/_main.scss";
	#app {
		font-family: 'iA Writer Quattro';
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
	}

	.vis-wrapper {
		position: relative;
		width: 100%;
		height: 100%;
	}
</style>
