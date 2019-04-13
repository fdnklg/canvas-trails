<template>
    <div ref="vis" id="vis" class="vis-wrapper"></div>
</template>

<script>
    import { 
        select as d3Select,
        timer as d3Timer,
        interval as d3Interval,
        easeCubic as d3EaseCubic,
        easeLinear as d3EaseLinear,
    } from 'd3';
    export default {
        name: 'VectorCanvas',
        props: ['data', 'config', 'factor'],
        data() {
            return {
                canvas: null,
                width: this.config.width,
                height: this.config.height,
                pointsWidth: this.config.pointsWidth,
                context: null,
                animAge: 0,
                maxAge: 100,
                frameRate: 30,
                age: [],
                duration: 6000,
                ease: null
            }
        },
        methods: {
            init() {
                this.canvas = d3Select('#vis').append('canvas')
                    .classed('vis-canvas', true)
                    .attr('width', this.width * this.factor)
                    .attr('height', this.height * this.factor)

                this.canvas
                    .attr('style', `width: ${this.width}px; height: ${this.height}px`)

                this.context = this.canvas.node().getContext('2d');
                this.context.save();
                this.context.clearRect(0, 0, this.width, this.height);
                this.context.scale(this.factor, this.factor);

                this.context.fillStyle = "rgba(0, 0, 0, 0.5)";
                this.context.lineWidth = 2;
                this.context.globalCompositeOperation = "source-over";
                this.ease = d3EaseLinear;
            },
            animationInit() {

                let timer = d3Timer((elapsed) => {
                    const t = Math.min(1, this.ease(elapsed / this.duration));

                    this.data.forEach((point) => {
                        point.x = point.startX * (1 - t) + point.endX * t;
                        point.y = point.startY * (1 - t) + point.endY * t;
                    });

                    if (t === 1) {
                        // stop this timer since we are done animating.
                        timer.stop();
                    }

                    this.drawPoints()
                })
            },
            drawPoints() {
                this.context.fillStyle = 'rgba(255,255,255,.05)';
                this.context.fillRect(0, 0, this.width, this.height);
                
                this.data.forEach((point) => {
                    this.context.fillStyle = '#FF0000';
                    this.context.fillRect(point.x, point.y, this.pointsWidth, this.pointsWidth);
                })
            },
            drawLines() {
                this.context.fillRect(0, 0, this.width, this.height);
 
                this.data.forEach((item, i) => {
                    this.context.beginPath();
                    this.context.moveTo(item.startX, item.startY);
                    this.context.lineTo(item.endX, item.endY);
                    this.context.stroke();

                    if (this.age[i]++ > this.maxAge) {
                        this.age[i] = this.randomAge();
                    }
                })


            }
        },
        mounted() {
            this.init();
            d3Interval(this.animationInit, 500)
        }
    }
</script>

<style lang="scss" scoped>
    .vis-wrapper {
        width: 100%;
        height: 100%;
    }
</style>

