<template>
  <div id="app">
    <VectorCanvas :data="randomData" :config="config" :factor="isRetina"/>
  </div>
</template>

<script>
	import VectorCanvas from './components/VectorCanvas.vue';
	import { range as d3Range } from 'd3';

	const conf = {
		'width': 900,
		'height': 600,
		'pointsWidth': 6,
		'pointsMargin': 4,
	}

	export default {
	name: 'app',
	components: {
		VectorCanvas
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
			d3Range(5000).forEach((index) => {
				arr.push({
					value: Math.random(),
					index: index 
				})
			});

			const arrPos = this.gridLayout(arr, conf.pointsWidth + conf.pointsMargin, conf.width, conf.height);
			const arrNetwork = this.networks(arrPos);
			return arrNetwork;
		},
	},
	methods: {
		gridLayout(points, pointsWidth, gridWidth, gridHeight) {
			const pointsHeight = pointsWidth;
			const pointsPerRow = Math.floor(gridWidth/pointsWidth);
			const numRows = points.length / pointsPerRow;

			points.forEach((point, i) => {
				point.x = pointsWidth * (i % pointsPerRow);
				point.y = pointsHeight * Math.floor(i / pointsPerRow);
			});

			return points;
		},
		networks(points) {
			const numNetworks = 10;
			const networkSize = 20;

			let arr = [];

			d3Range(numNetworks).forEach((index) => {
				let randomPointStart = this.randomPoint(points);
				let randomNetworkSize = Math.floor(networkSize * Math.random());

				for (let index = 0; index <= randomNetworkSize; index++) {
					let randomPointEnd = this.randomPoint(points);

					arr.push({
						startX: randomPointStart.x,
						startY: randomPointStart.y,
						endX: randomPointEnd.x,
						endY: randomPointEnd.y,
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
	#app {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
	}
</style>
