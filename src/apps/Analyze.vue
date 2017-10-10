<template>
  <div id="App"
       class="uk-width-1-1"
       namespace="App">
    <!--<div class="uk-navbar uk-width-1-1">
      <b class="uk-navbar-brand app-title">Air Traffic Control Visual Analysis System</b>
    </div>-->
    <div class="app-top">
      <div class="uk-float-left app-title">
        Air Traffic Control Visual Analysis System
      </div>
      <div class="area-map uk-width-1-1">
          <area-map></area-map>
      </div>
    </div>
    <div class="app-bottom">
        <time-line></time-line>
    </div>
  </div>
</template>
        <!--<div class="time-line uk-width-1-1 uk-panel">-->
<!--  <area-map></area-map>
  <!--<div class= "{{ style['area']}}" id="{{ style['area-map']}}">
    <area-map :map-option="mapOption"></area-map>
  </div>-->
        <!--</div>
        <!--<div class="uk-width-1-1 full-height">
          <component :is="activeCal.comp"></component>
        </div>-->
      <!--</div>
      <!--<div class="iso-map uk-width-1-2">
        <iso-map></iso-map>
      </div>-->
      <!--<div class="distribute uk-width-1-6 uk-panel-box">-->
      <!--<distribute-view></distribute-view>-->
      <!--</div>-->


      <!--<div class="project uk-width-1-3 uk-panel">
        <project></project>
      </div>
      <div class="time-line uk-width-2-3 uk-panel">
        <time-line></time-line>
      </div>-->

  <!--::-webkit-scrollbar {-->
  <!--width: @scrollbar-width;-->
  <!--height: @scrollbar-height;-->
  <!--}-->
  <!--::-webkit-scrollbar-thumb {-->
  <!--background: @scrollbar-fg-color;-->
  <!--}-->
  <!--::-webkit-scrollbar-track-piece {-->
  <!--background: @scrollbar-bg-color;-->
  <!--}-->
  <!--<div id="SkyeyeTooltip"></div>-->

<script>
  import DistributeView from '../components/DistributeView.vue'
  import SelectMenu from '../components/SelectMenu.vue'
  import TimeLine from '../components/TimeLine.vue'
  import Calendar from '../components/Calendar.vue'
  import Correlation from '../components/Correlation.vue'
  import IsoMap from '../components/IsoMap.vue'
  import Project from '../components/Project.vue'
  import AreaMap from '../components/AreaMap'
  import Test from '../components/Test.vue'

  import sensorData from '../data/sensor.json'
  import storage from '../commons/storage'
  import Process from './dataProcess.worker'
  import banner from '../../assets/images/display.jpg'
  import {setSCTToken, setChemicalToken, setTimeToken, setCorrelation} from '../vuex/actions'
  import {selectedHour, timeLineState} from '../vuex/getters'
  import config from '../commons/config'

  let colorMap = config.colorMap

  export default{
    vuex: {
      actions: { setSCTToken, setChemicalToken, setTimeToken, setCorrelation },
      getters: {selectedHour, timeLineState}
    },
    components: { IsoMap, SelectMenu, TimeLine, DistributeView, Calendar, Correlation, Project, AreaMap, Test },
    data () {
      return {
        banner,
        calenderView: [ {
          value: 'calendar',
          text: 'Calendar',
          comp: 'Calendar'
        }, {
          value: 'correlation',
          text: 'Correlation',
          comp: 'Correlation'
        } ],
        activeCal: {
          value: 'calendar',
          text: 'Calendar',
          comp: 'Calendar'
        },
        colorsArr: Object.keys(colorMap).map((d) => {
          return {
            name: d,
            color: colorMap[ d ]
          }
        })
      }
    },
    methods: {
      switchCalendar (op) {
        this.activeCal = op
      }
    },
    ready () {
      this.$fLogs.info('[APP]Analyze is ready !!!')
    },
    created () {
      let wk = new Process()
      wk.postMessage({ sensorData })
      wk.onmessage = (evt) => {
        let { bySensor, byChemical, byTime, pearsonSameChemical, mdsArr, timesArr } = evt.data
        mdsArr
        timesArr
        console.log(timesArr)
        console.log(mdsArr)
        let dataToken = storage.set(bySensor, 'sctData')
        this.setSCTToken(dataToken)
        let chemicalToken = storage.set(byChemical, 'byChemical')
        this.setChemicalToken(chemicalToken)
        let timeToken = storage.set(byTime, 'byTime')
        this.setTimeToken(timeToken)

        let pearsonToken = storage.set(pearsonSameChemical, 'pearsonSameChemical')
        this.setCorrelation(pearsonToken)
      }
    }
  }
</script>
<style lang="less">
  @import "../commons/base.less";
  @import "../commons/base.vars.less";
  @title-h: 40px;
  @body-top-h: 80%;
  #App {
    color:white;
    height: 100%;
    /*.center {
      width: 10px;
      i {
        color: @color-main;
      }
    }
    /*.current {
      line-height: @title-h;
      color: #00a8e6;
      margin-left: 0;
      margin-bottom: 0;
    }*/
    .app-top {
      height: 80%;
      .app-title{
          padding: 10px;
      }
      .area-map{
          height:100%;
      }
      /*.calendar {*/
        /*height: 100%;*/
        /*border-right: 1px dashed #ddd;*/
      /*}*/
    }
    .app-bottom {
      /*margin-top: 8px;*/
      height: 20%;
      /*border-top: 1px dashed #ddd;*/
      background-color: #333;
      /*.time-line {*/
        /*height: 100%;*/
        /*background-color: #fff;*/
        /*border-left: 1px dashed #ddd;*/
      /*}*/
    }
    // .app-down {
    //   margin-top: 8px;
    //   height: calc(~"39% - 20px - " @title-h);
    // }
    // .app-bottom-top {
    //   margin-top: calc(~"-38% + 40px");
    //   height: 100%;
    //   background: #fff;
    // }
    .clear-grid-margin {
      margin-left: 0;
    }
    .app-title {
      height: @title-h;
      width:auto;
      height:auto;
      color: #999;
      font-size: 1.5em;
        background: #000000;
    }
    .full-height {
      height: 100%;
    }
    .legend {
      width: 550px;
      margin-top: 10px;
      .legend-item {
        height: 100%;
        width: 30px;
        border-radius: 10px;
      }
    }
  }
  #SkyeyeTooltip {
    position: fixed;
    z-index: 1010;
    line-height: @font-size-l;
    font-weight: bold;
    padding: @margin-m;
    color: #fff;
    border: 1px solid @color-bd;
    background: @color-card-bg;
  }
</style>
