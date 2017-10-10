<template>
  <div class="uk-width-1-1 container">
    <div class="uk-float-left uk-width-1-10 left">
      Legend View
    </div>
    <div class="uk-float-right uk-width-9-10 right">
      <div class="uk-width-1-1 timelineTop">
      </div>
      <div class="uk-width-1-1 timelineBottom">
      </div>
    </div>
  </div>
</template>
<script>
//  import {month, sensor, chemical, factory} from '../vuex/getters'
//  import {switchMonth, switchChemical, switchFactory, switchSensor, addSCTChart, addDiffChart, addSCTCharts} from '../vuex/actions'
//  import config from '../commons/config'
  import $ from 'jquery'
  import d3 from 'd3'
  export default {
    vuex: {
      // getters: { month, sensor, chemical, factory },
      // actions: { switchMonth, switchChemical, switchFactory, switchSensor, addSCTChart, addDiffChart, addSCTCharts }
    },
    data () {
      return {
      }
    },
    methods: {
      init () {
        this.initParam('timelineTop')
        this.initAxis()
        this.initBar()
      },
      initParam (content) {
        self.content = '.' + content
        self.height = $(self.content).outerHeight()
        self.width = $(self.content).outerWidth()
        self.margin = {top: 20, right: 20, bottom: 15, left: 40}
        self.width = self.width - self.margin.left - self.margin.right
        self.height = self.height - self.margin.top - self.margin.bottom
        self.xScale = d3.time.scale().range([0, self.width])
        self.yScale = d3.scale.linear().range([self.height, 0])
      },
      initAxis () {
        let timeFormatYear = d3.time.format('%Y')
        let timeFormatMonth = d3.time.format('%m/%d')
        // let timeFormatDay = d3.time.format('%d %H:%M')
        let timeFormatHour = d3.time.format('%H:%M')
        self.xAxis = d3.svg.axis().scale(self.xScale)
          .orient('bottom')
          .tickFormat(function (d, i) {
            let ticks = self.xAxis.scale().ticks()
            if (i === 0 || i === ticks.length - 1) {
              return ''
            } else {
              if (d.getFullYear() - ticks[i - 1].getFullYear() !== 0) {
                return timeFormatYear(d)
              } else if (d.getMonth() - ticks[i - 1].getMonth() !== 0) {
                return timeFormatMonth(d)
              } else if (d.getDay() - ticks[i - 1].getDay() !== 0) {
                return timeFormatMonth(d)
              } else {
                return timeFormatHour(d)
              }
            }
          })
        self.yAxis = d3.svg.axis().scale(self.yScale)
          .orient('left').ticks(3)
        self.svg = d3.select(self.content)
          .append('svg')
          .attr('class', 'timelineTop')
          .attr('width', self.width + self.margin.left + self.margin.right)
          .attr('height', self.height + self.margin.top + self.margin.bottom)
          .append('g')
          .attr('transform', 'translate(' + self.margin.left + ',' + self.margin.top + ')')
      },
      initBar () {
        console.log(1111, this.test)
      }
    },
    ready () {
      this.init()
    }
  }
</script>
<style lang="less" scoped>
  .container {
    height: 100%;
    width: 100%;
    .left {
      height: 100%;
      background: red;
    }
    .right {
      height: 100%;
    }
    .timelineTop {
      height: 50%;
      background: blue;
    }
    .timelineBottom {
      height: 50%;
      background: yellow;
    }
  }
</style>
