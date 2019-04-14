<template>
    <svg id="vis-text" :width="width" :height="height" :viewBox="viewBox">
    </svg>
</template>

<script>
    import { 
        select as d3Select,
    } from 'd3';

    export default {
        name: 'SvgText',
        props: ['width', 'height', 'data'],
        data() {
            return {
                filtered: null
            }
        },
        computed: {
            viewBox() {
                return `0 0 ${this.width} ${this.height}`;
            },
            filteredData() {
                const filtered = this.data.filter((point,i) => {
                    if (i == 0) {
                        return point;
                    } else if (i > 0 && point.startX != this.data[i - 1].startX) {
                        console.log(point);
                        return point;
                    }
                });

                return filtered;
            }
        },
        methods: {
            drawAnchors() {
                d3Select('#vis-text').selectAll('circle')
                    .data(this.filteredData)
                    .enter()
                    .append('circle')
                    .classed('vis-anchor', true)
                    .attr('cx', d => { return d.startX })
                    .attr('cy', d => { return d.startY })
                    .attr('r', 6)
                    .attr('fill', 'white')
                    .attr('stroke-width', '2px')
                    .attr('stroke', 'red')
                    
            },
            drawText() {

                d3Select('#vis-text').selectAll('text')
                    .data(this.filteredData)
                    .enter()
                    .append('text')
                    .classed('vis-label', true)
                    .attr('x', d => { return d.startX})
                    .attr('y', d => { return d.startY + 30 })
                    .attr('text-anchor', 'middle')
                    .text(d => { return d.name })
                    
            },
        },
        mounted() {
            this.drawText();
            this.drawAnchors();
        }
    }
</script>

<style lang="scss" scoped>
    @import "./../style/_main.scss";

    #vis-text {
        position: absolute;
        top: 0;
        left: 0;
    }

    svg {
        text.vis-label {
           font-family: 'iA Writer Quattro';
           font-weight: 400;
           font-style: normal; 
        }
    }

</style>


