<template>
  <div class="app-container">
    <el-button type="primary" @click="seeBatteryHistory()">点击查看历史轨迹</el-button>
    <!-- 查看电池历史轨迹 -->
    <el-dialog :visible.sync="dialogVisible" @close="closeBatteryHistroy" width="70%">
      <!-- 日期选择 -->
      <div class="filter-container" style="text-align:center">
        <span class="selecte-date-tit">请选择时间范围：</span>
        <el-date-picker
          v-model="selectedDate"
          type="datetimerange"
          align="right"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :default-time="['00:00:00', '23:59:59']"
          @change="changeDate"
          value-format="timestamp"
        ></el-date-picker>
      </div>
      <div id="map-container" style="width:100%; height:500px"></div>
    </el-dialog>
  </div>
</template>

<!--
使用说明
index.html引入 
‘<script type="text/javascript" src="https://webapi.amap.com/maps?v=1.4.15&key=872d315a8f55a1da909c08dc50e36a4f"></script>’

vue.config.js引入
module.exports = {
  configureWebpack: {
    externals: {
      'AMap': 'AMap' // 高德地图配置
    }
  }
}

官方实例https://lbs.amap.com/api/javascript-api/example/marker/replaying-historical-running-data/?sug_index=6
-->
<script>
//引入高德地图
import AMap from "AMap";
export default {
  data() {
    return {
      listLoading: true,
      dialogVisible: false, //电池轨迹dialog
      //地图
      map: null,
      marker: null,
      polyline: null,
      passedPolyline: null,
      selectedDate: [],
      markerArr: [], //电池经纬度,也用于画线
      startIcon: new AMap.Icon({
        //起点图标
        // 图标尺寸
        size: new AMap.Size(25, 34),
        // 图标的取图地址
        image:
          "//a.amap.com/jsapi_demos/static/demo-center/icons/dir-marker.png",
        // 图标所用图片大小
        imageSize: new AMap.Size(135, 40),
        // 图标取图偏移量
        imageOffset: new AMap.Pixel(-9, -3)
      }),
      endIcon: new AMap.Icon({
        //终点图标
        size: new AMap.Size(25, 34),
        image:
          "//a.amap.com/jsapi_demos/static/demo-center/icons/dir-marker.png",
        imageSize: new AMap.Size(135, 40),
        imageOffset: new AMap.Pixel(-95, -3)
      })
    };
  },
  methods: {
    //点击查看历史轨迹
    seeBatteryHistory() {
      let that = this;
      that.dialogVisible = true;
      that.$nextTick(() => {
        //初始化地图
        that.initMap();
      });
      //初始化地图
    },
    //初始化地图
    initMap() {
      let that = this;
      that.map = new AMap.Map("map-container", {
        resizeEnable: true,
        zoom: 7,
        center: [120.6641561, 31.3063851] //地图中心点
      });
      AMap.plugin(["AMap.ToolBar"], function() {
        //加载工具条
        var tool = new AMap.ToolBar();
        that.map.addControl(tool);
      });
      setTimeout(function() {
        that.$message({
          title: "提醒",
          message: "请选择时间范围！",
          type: "warning",
          duration: 2000
        });
      }, 500);
    },
    //选择时间范围后
    changeDate(value) {
      let that = this;
      //格式为时间戳格式
      this.selectedDate = value;
      //清除地图的所有覆盖物
      that.map.clearMap();
      //电池经纬度,也用于画线
      this.markerArr = [];
      //获取数据
      this.GetBatteryHistory();
    },
    //获取轨迹数据
    GetBatteryHistory() {
      let that = this;
      this.listLoading = true;
      //接口获得数据后转换，高德接受的数据格式为[[111.11,122.22],[111.11,123.33]]
      setTimeout(() => {
        that.listLoading = false;
        //坐标点数组转换
        let temp = [
          {
            latitude: 120.6304938,
            longitude: 31.3043409,
            timestramp: "1587433337000",
            speed: 33,
            direction: 249
          },
          {
            latitude: 120.6302744,
            longitude: 31.3042127,
            timestramp: "1587433340000",
            speed: 27,
            direction: 251
          },
          {
            latitude: 120.6300442,
            longitude: 31.3042245,
            timestramp: "1587433343000",
            speed: 22,
            direction: 268
          },
          {
            latitude: 120.6298966,
            longitude: 31.3045871,
            timestramp: "1587433347000",
            speed: 30,
            direction: 330
          },
          {
            latitude: 120.6298828,
            longitude: 31.3049016,
            timestramp: "1587433350000",
            speed: 35,
            direction: 350
          },
          {
            latitude: 120.6298541,
            longitude: 31.3057237,
            timestramp: "1587433365000",
            speed: 19,
            direction: 1
          },
          {
            latitude: 120.6296779,
            longitude: 31.3071743,
            timestramp: "1587433380000",
            speed: 26,
            direction: 352
          },
          {
            latitude: 120.6294535,
            longitude: 31.3084467,
            timestramp: "1587433485000",
            speed: 36,
            direction: 354
          },
          {
            latitude: 120.6293506,
            longitude: 31.309149,
            timestramp: "1587433500000",
            speed: 27,
            direction: 353
          },
          {
            latitude: 120.6292006,
            longitude: 31.3102171,
            timestramp: "1587433515000",
            speed: 20,
            direction: 354
          },
          {
            latitude: 120.6290133,
            longitude: 31.3113246,
            timestramp: "1587433530000",
            speed: 37,
            direction: 351
          },
          {
            latitude: 120.6287725,
            longitude: 31.3129596,
            timestramp: "1587433545000",
            speed: 50,
            direction: 351
          },
          {
            latitude: 120.6284252,
            longitude: 31.3145874,
            timestramp: "1587433560000",
            speed: 43,
            direction: 349
          },
          {
            latitude: 120.6281168,
            longitude: 31.3161233,
            timestramp: "1587433575000",
            speed: 36,
            direction: 352
          },
          {
            latitude: 120.6280098,
            longitude: 31.3165385,
            timestramp: "1587433582000",
            speed: 15,
            direction: 352
          },
          {
            latitude: 120.6278226,
            longitude: 31.3175211,
            timestramp: "1587433616000",
            speed: 34,
            direction: 349
          },
          {
            latitude: 120.6274974,
            longitude: 31.318934,
            timestramp: "1587433631000",
            speed: 40,
            direction: 350
          },
          {
            latitude: 120.6271214,
            longitude: 31.3207473,
            timestramp: "1587433646000",
            speed: 41,
            direction: 353
          },
          {
            latitude: 120.6268624,
            longitude: 31.3220272,
            timestramp: "1587433662000",
            speed: 36,
            direction: 348
          },
          {
            latitude: 120.6267154,
            longitude: 31.3224337,
            timestramp: "1587433667000",
            speed: 29,
            direction: 341
          },
          {
            latitude: 120.6266853,
            longitude: 31.3226654,
            timestramp: "1587433671000",
            speed: 17,
            direction: 336
          },
          {
            latitude: 120.6266274,
            longitude: 31.3240542,
            timestramp: "1587433746000",
            speed: 35,
            direction: 350
          },
          {
            latitude: 120.6264865,
            longitude: 31.3255303,
            timestramp: "1587433761000",
            speed: 24,
            direction: 0
          },
          {
            latitude: 120.6264695,
            longitude: 31.3257157,
            timestramp: "1587433765000",
            speed: 18,
            direction: 352
          },
          {
            latitude: 120.6264037,
            longitude: 31.326169,
            timestramp: "1587433780000",
            speed: 3,
            direction: 346
          },
          {
            latitude: 120.625615,
            longitude: 31.3280294,
            timestramp: "1587433810000",
            speed: 22,
            direction: 334
          },
          {
            latitude: 120.6254224,
            longitude: 31.3284136,
            timestramp: "1587433816000",
            speed: 27,
            direction: 341
          },
          {
            latitude: 120.6253192,
            longitude: 31.3286723,
            timestramp: "1587433820000",
            speed: 29,
            direction: 341
          },
          {
            latitude: 120.6247851,
            longitude: 31.3299991,
            timestramp: "1587433835000",
            speed: 45,
            direction: 327
          },
          {
            latitude: 120.6237449,
            longitude: 31.331224,
            timestramp: "1587433850000",
            speed: 31,
            direction: 328
          },
          {
            latitude: 120.6231733,
            longitude: 31.3322706,
            timestramp: "1587433865000",
            speed: 19,
            direction: 337
          },
          {
            latitude: 120.6229902,
            longitude: 31.3327565,
            timestramp: "1587433910000",
            speed: 5,
            direction: 336
          },
          {
            latitude: 120.6229411,
            longitude: 31.3329163,
            timestramp: "1587433940000",
            speed: 18,
            direction: 351
          },
          {
            latitude: 120.6228034,
            longitude: 31.3338817,
            timestramp: "1587433955000",
            speed: 22,
            direction: 344
          },
          {
            latitude: 120.6224747,
            longitude: 31.3353199,
            timestramp: "1587433970000",
            speed: 45,
            direction: 348
          },
          {
            latitude: 120.6221307,
            longitude: 31.3358988,
            timestramp: "1587433977000",
            speed: 32,
            direction: 314
          },
          {
            latitude: 120.621912,
            longitude: 31.3359609,
            timestramp: "1587433980000",
            speed: 24,
            direction: 292
          },
          {
            latitude: 120.6216941,
            longitude: 31.3359306,
            timestramp: "1587433983000",
            speed: 26,
            direction: 272
          },
          {
            latitude: 120.6213223,
            longitude: 31.3355603,
            timestramp: "1587433988000",
            speed: 32,
            direction: 213
          },
          {
            latitude: 120.6212557,
            longitude: 31.3353749,
            timestramp: "1587433990000",
            speed: 37,
            direction: 197
          },
          {
            latitude: 120.6212968,
            longitude: 31.3350623,
            timestramp: "1587433993000",
            speed: 38,
            direction: 180
          },
          {
            latitude: 120.621236,
            longitude: 31.3347587,
            timestramp: "1587433996000",
            speed: 34,
            direction: 194
          },
          {
            latitude: 120.6210703,
            longitude: 31.3345879,
            timestramp: "1587433999000",
            speed: 26,
            direction: 234
          },
          {
            latitude: 120.6206787,
            longitude: 31.3345277,
            timestramp: "1587434003000",
            speed: 36,
            direction: 264
          },
          {
            latitude: 120.6203465,
            longitude: 31.3345235,
            timestramp: "1587434006000",
            speed: 38,
            direction: 267
          },
          {
            latitude: 120.6188644,
            longitude: 31.3345659,
            timestramp: "1587434018000",
            speed: 43,
            direction: 270
          },
          {
            latitude: 120.6134097,
            longitude: 31.3338014,
            timestramp: "1587434052000",
            speed: 34,
            direction: 266
          },
          {
            latitude: 120.6123001,
            longitude: 31.3337782,
            timestramp: "1587434067000",
            speed: 0,
            direction: 256
          },
          {
            latitude: 120.611307,
            longitude: 31.3339852,
            timestramp: "1587434127000",
            speed: 41,
            direction: 274
          },
          {
            latitude: 120.6087645,
            longitude: 31.3335788,
            timestramp: "1587434142000",
            speed: 68,
            direction: 257
          },
          {
            latitude: 120.606156,
            longitude: 31.3330094,
            timestramp: "1587434157000",
            speed: 56,
            direction: 256
          },
          {
            latitude: 120.6038035,
            longitude: 31.332444,
            timestramp: "1587434172000",
            speed: 59,
            direction: 255
          },
          {
            latitude: 120.6020844,
            longitude: 31.3320873,
            timestramp: "1587434187000",
            speed: 43,
            direction: 256
          },
          {
            latitude: 120.5998843,
            longitude: 31.331643,
            timestramp: "1587434202000",
            speed: 51,
            direction: 256
          },
          {
            latitude: 120.5975542,
            longitude: 31.3311264,
            timestramp: "1587434217000",
            speed: 62,
            direction: 257
          },
          {
            latitude: 120.5946317,
            longitude: 31.3306503,
            timestramp: "1587434232000",
            speed: 63,
            direction: 261
          },
          {
            latitude: 120.5915698,
            longitude: 31.3305287,
            timestramp: "1587434247000",
            speed: 60,
            direction: 272
          },
          {
            latitude: 120.5889929,
            longitude: 31.3309686,
            timestramp: "1587434262000",
            speed: 61,
            direction: 281
          },
          {
            latitude: 120.5870388,
            longitude: 31.3312739,
            timestramp: "1587434277000",
            speed: 34,
            direction: 280
          },
          {
            latitude: 120.5856371,
            longitude: 31.3314953,
            timestramp: "1587434292000",
            speed: 35,
            direction: 281
          },
          {
            latitude: 120.5843173,
            longitude: 31.3316641,
            timestramp: "1587434307000",
            speed: 30,
            direction: 282
          },
          {
            latitude: 120.5829957,
            longitude: 31.3318794,
            timestramp: "1587434322000",
            speed: 41,
            direction: 284
          },
          {
            latitude: 120.580538,
            longitude: 31.3322526,
            timestramp: "1587434337000",
            speed: 59,
            direction: 279
          },
          {
            latitude: 120.5779413,
            longitude: 31.3329875,
            timestramp: "1587434352000",
            speed: 66,
            direction: 298
          },
          {
            latitude: 120.5754747,
            longitude: 31.3344231,
            timestramp: "1587434367000",
            speed: 68,
            direction: 315
          },
          {
            latitude: 120.5734649,
            longitude: 31.3364262,
            timestramp: "1587434382000",
            speed: 75,
            direction: 318
          },
          {
            latitude: 120.5712534,
            longitude: 31.338553,
            timestramp: "1587434397000",
            speed: 76,
            direction: 317
          },
          {
            latitude: 120.5688994,
            longitude: 31.3407357,
            timestramp: "1587434412000",
            speed: 81,
            direction: 316
          },
          {
            latitude: 120.5662482,
            longitude: 31.3428359,
            timestramp: "1587434427000",
            speed: 77,
            direction: 309
          },
          {
            latitude: 120.5635204,
            longitude: 31.3446724,
            timestramp: "1587434442000",
            speed: 81,
            direction: 309
          },
          {
            latitude: 120.5610095,
            longitude: 31.3468685,
            timestramp: "1587434457000",
            speed: 84,
            direction: 318
          },
          {
            latitude: 120.558771,
            longitude: 31.3492322,
            timestramp: "1587434472000",
            speed: 82,
            direction: 322
          },
          {
            latitude: 120.5566138,
            longitude: 31.3515453,
            timestramp: "1587434487000",
            speed: 74,
            direction: 321
          },
          {
            latitude: 120.5545481,
            longitude: 31.3532879,
            timestramp: "1587434502000",
            speed: 60,
            direction: 294
          },
          {
            latitude: 120.5524829,
            longitude: 31.3538959,
            timestramp: "1587434515000",
            speed: 51,
            direction: 314
          },
          {
            latitude: 120.5523557,
            longitude: 31.3542046,
            timestramp: "1587434517000",
            speed: 54,
            direction: 330
          },
          {
            latitude: 120.5522591,
            longitude: 31.3544664,
            timestramp: "1587434519000",
            speed: 53,
            direction: 339
          },
          {
            latitude: 120.5522492,
            longitude: 31.3550096,
            timestramp: "1587434523000",
            speed: 49,
            direction: 2
          },
          {
            latitude: 120.5523648,
            longitude: 31.3552919,
            timestramp: "1587434525000",
            speed: 54,
            direction: 15
          },
          {
            latitude: 120.5525141,
            longitude: 31.3555416,
            timestramp: "1587434527000",
            speed: 55,
            direction: 25
          },
          {
            latitude: 120.5545663,
            longitude: 31.3570621,
            timestramp: "1587434542000",
            speed: 58,
            direction: 55
          },
          {
            latitude: 120.556913,
            longitude: 31.3588531,
            timestramp: "1587434557000",
            speed: 77,
            direction: 47
          },
          {
            latitude: 120.5596781,
            longitude: 31.3604666,
            timestramp: "1587434572000",
            speed: 74,
            direction: 59
          },
          {
            latitude: 120.5625593,
            longitude: 31.3616497,
            timestramp: "1587434587000",
            speed: 66,
            direction: 68
          },
          {
            latitude: 120.565336,
            longitude: 31.3625862,
            timestramp: "1587434602000",
            speed: 71,
            direction: 65
          },
          {
            latitude: 120.567817,
            longitude: 31.3639618,
            timestramp: "1587434617000",
            speed: 67,
            direction: 53
          },
          {
            latitude: 120.5697462,
            longitude: 31.3654326,
            timestramp: "1587434632000",
            speed: 52,
            direction: 57
          },
          {
            latitude: 120.5703011,
            longitude: 31.3656006,
            timestramp: "1587434636000",
            speed: 49,
            direction: 76
          },
          {
            latitude: 120.5705702,
            longitude: 31.365589,
            timestramp: "1587434638000",
            speed: 45,
            direction: 89
          },
          {
            latitude: 120.5708186,
            longitude: 31.365538,
            timestramp: "1587434640000",
            speed: 43,
            direction: 102
          },
          {
            latitude: 120.5710293,
            longitude: 31.3654316,
            timestramp: "1587434642000",
            speed: 40,
            direction: 117
          },
          {
            latitude: 120.5711732,
            longitude: 31.365284,
            timestramp: "1587434644000",
            speed: 37,
            direction: 134
          },
          {
            latitude: 120.5713869,
            longitude: 31.3650545,
            timestramp: "1587434647000",
            speed: 38,
            direction: 135
          },
          {
            latitude: 120.5721847,
            longitude: 31.3639476,
            timestramp: "1587434662000",
            speed: 27,
            direction: 148
          },
          {
            latitude: 120.5730112,
            longitude: 31.3628365,
            timestramp: "1587434677000",
            speed: 39,
            direction: 155
          },
          {
            latitude: 120.5731275,
            longitude: 31.3620011,
            timestramp: "1587434685000",
            speed: 45,
            direction: 188
          },
          {
            latitude: 120.5730225,
            longitude: 31.3617722,
            timestramp: "1587434687000",
            speed: 48,
            direction: 201
          },
          {
            latitude: 120.572813,
            longitude: 31.3615526,
            timestramp: "1587434689000",
            speed: 51,
            direction: 218
          },
          {
            latitude: 120.57252,
            longitude: 31.3614426,
            timestramp: "1587434691000",
            speed: 51,
            direction: 246
          },
          {
            latitude: 120.5721676,
            longitude: 31.3613827,
            timestramp: "1587434693000",
            speed: 56,
            direction: 258
          },
          {
            latitude: 120.571812,
            longitude: 31.3614035,
            timestramp: "1587434695000",
            speed: 57,
            direction: 271
          },
          {
            latitude: 120.5714753,
            longitude: 31.361473,
            timestramp: "1587434697000",
            speed: 57,
            direction: 282
          },
          {
            latitude: 120.5686247,
            longitude: 31.3630252,
            timestramp: "1587434712000",
            speed: 92,
            direction: 305
          },
          {
            latitude: 120.5653013,
            longitude: 31.3655003,
            timestramp: "1587434727000",
            speed: 101,
            direction: 313
          },
          {
            latitude: 120.5624426,
            longitude: 31.3680268,
            timestramp: "1587434742000",
            speed: 88,
            direction: 318
          },
          {
            latitude: 120.5600308,
            longitude: 31.3709374,
            timestramp: "1587434757000",
            speed: 106,
            direction: 327
          },
          {
            latitude: 120.5576199,
            longitude: 31.3746515,
            timestramp: "1587434772000",
            speed: 113,
            direction: 334
          },
          {
            latitude: 120.5558045,
            longitude: 31.3784725,
            timestramp: "1587434787000",
            speed: 111,
            direction: 339
          },
          {
            latitude: 120.554392,
            longitude: 31.3825621,
            timestramp: "1587434802000",
            speed: 110,
            direction: 342
          },
          {
            latitude: 120.5529202,
            longitude: 31.3867906,
            timestramp: "1587434817000",
            speed: 122,
            direction: 342
          },
          {
            latitude: 120.5512987,
            longitude: 31.3910729,
            timestramp: "1587434832000",
            speed: 120,
            direction: 342
          },
          {
            latitude: 120.5495969,
            longitude: 31.3953188,
            timestramp: "1587434847000",
            speed: 116,
            direction: 338
          },
          {
            latitude: 120.5476313,
            longitude: 31.3993878,
            timestramp: "1587434862000",
            speed: 116,
            direction: 336
          },
          {
            latitude: 120.5453392,
            longitude: 31.4034743,
            timestramp: "1587434877000",
            speed: 118,
            direction: 332
          },
          {
            latitude: 120.5428094,
            longitude: 31.4073724,
            timestramp: "1587434892000",
            speed: 115,
            direction: 329
          },
          {
            latitude: 120.540371,
            longitude: 31.4105315,
            timestramp: "1587434907000",
            speed: 82,
            direction: 326
          },
          {
            latitude: 120.5383589,
            longitude: 31.4132913,
            timestramp: "1587434922000",
            speed: 97,
            direction: 329
          },
          {
            latitude: 120.5359012,
            longitude: 31.4172231,
            timestramp: "1587434938000",
            speed: 103,
            direction: 331
          },
          {
            latitude: 120.5340705,
            longitude: 31.4205508,
            timestramp: "1587434953000",
            speed: 91,
            direction: 339
          },
          {
            latitude: 120.5327255,
            longitude: 31.4239228,
            timestramp: "1587434968000",
            speed: 93,
            direction: 340
          },
          {
            latitude: 120.5316288,
            longitude: 31.4271083,
            timestramp: "1587434983000",
            speed: 83,
            direction: 344
          },
          {
            latitude: 120.5303673,
            longitude: 31.4301486,
            timestramp: "1587434998000",
            speed: 94,
            direction: 340
          },
          {
            latitude: 120.5289837,
            longitude: 31.4333711,
            timestramp: "1587435013000",
            speed: 91,
            direction: 339
          },
          {
            latitude: 120.5271306,
            longitude: 31.4370699,
            timestramp: "1587435028000",
            speed: 109,
            direction: 336
          },
          {
            latitude: 120.525314,
            longitude: 31.4402316,
            timestramp: "1587435043000",
            speed: 76,
            direction: 330
          },
          {
            latitude: 120.5239284,
            longitude: 31.4424058,
            timestramp: "1587435058000",
            speed: 62,
            direction: 331
          },
          {
            latitude: 120.5225113,
            longitude: 31.444426,
            timestramp: "1587435073000",
            speed: 65,
            direction: 328
          },
          {
            latitude: 120.5210482,
            longitude: 31.4464808,
            timestramp: "1587435088000",
            speed: 64,
            direction: 326
          },
          {
            latitude: 120.5198156,
            longitude: 31.4479198,
            timestramp: "1587435103000",
            speed: 26,
            direction: 322
          },
          {
            latitude: 120.518934,
            longitude: 31.4489626,
            timestramp: "1587435118000",
            speed: 46,
            direction: 326
          },
          {
            latitude: 120.5177793,
            longitude: 31.4503952,
            timestramp: "1587435133000",
            speed: 50,
            direction: 324
          },
          {
            latitude: 120.5160264,
            longitude: 31.4522293,
            timestramp: "1587435148000",
            speed: 64,
            direction: 322
          },
          {
            latitude: 120.5144035,
            longitude: 31.4539226,
            timestramp: "1587435163000",
            speed: 38,
            direction: 320
          },
          {
            latitude: 120.5132143,
            longitude: 31.4551357,
            timestramp: "1587435178000",
            speed: 45,
            direction: 318
          },
          {
            latitude: 120.5121295,
            longitude: 31.4561295,
            timestramp: "1587435193000",
            speed: 38,
            direction: 315
          },
          {
            latitude: 120.5106733,
            longitude: 31.457474,
            timestramp: "1587435208000",
            speed: 51,
            direction: 315
          },
          {
            latitude: 120.5088574,
            longitude: 31.458954,
            timestramp: "1587435223000",
            speed: 55,
            direction: 312
          },
          {
            latitude: 120.5071145,
            longitude: 31.4603507,
            timestramp: "1587435238000",
            speed: 55,
            direction: 312
          },
          {
            latitude: 120.5048145,
            longitude: 31.4622451,
            timestramp: "1587435268000",
            speed: 3,
            direction: 308
          },
          {
            latitude: 120.503954,
            longitude: 31.4629951,
            timestramp: "1587435301000",
            speed: 19,
            direction: 315
          },
          {
            latitude: 120.5035452,
            longitude: 31.4634135,
            timestramp: "1587435316000",
            speed: 9,
            direction: 316
          },
          {
            latitude: 120.5030736,
            longitude: 31.463837,
            timestramp: "1587435331000",
            speed: 14,
            direction: 313
          },
          {
            latitude: 120.5025622,
            longitude: 31.464379,
            timestramp: "1587435346000",
            speed: 19,
            direction: 320
          },
          {
            latitude: 120.5021824,
            longitude: 31.4647314,
            timestramp: "1587435361000",
            speed: 0,
            direction: 316
          },
          {
            latitude: 120.501738,
            longitude: 31.4651871,
            timestramp: "1587435391000",
            speed: 11,
            direction: 318
          },
          {
            latitude: 120.5013104,
            longitude: 31.4656544,
            timestramp: "1587435406000",
            speed: 30,
            direction: 322
          },
          {
            latitude: 120.5003694,
            longitude: 31.4666364,
            timestramp: "1587435421000",
            speed: 31,
            direction: 319
          },
          {
            latitude: 120.4995206,
            longitude: 31.4676127,
            timestramp: "1587435436000",
            speed: 34,
            direction: 322
          },
          {
            latitude: 120.498372,
            longitude: 31.4688427,
            timestramp: "1587435451000",
            speed: 44,
            direction: 324
          },
          {
            latitude: 120.4971831,
            longitude: 31.4702741,
            timestramp: "1587435466000",
            speed: 49,
            direction: 323
          },
          {
            latitude: 120.4959413,
            longitude: 31.4718522,
            timestramp: "1587435481000",
            speed: 53,
            direction: 326
          },
          {
            latitude: 120.4946789,
            longitude: 31.4737228,
            timestramp: "1587435496000",
            speed: 55,
            direction: 329
          },
          {
            latitude: 120.4934565,
            longitude: 31.4755727,
            timestramp: "1587435511000",
            speed: 55,
            direction: 330
          },
          {
            latitude: 120.4923379,
            longitude: 31.4773674,
            timestramp: "1587435526000",
            speed: 53,
            direction: 330
          },
          {
            latitude: 120.491147,
            longitude: 31.4792423,
            timestramp: "1587435541000",
            speed: 60,
            direction: 331
          },
          {
            latitude: 120.489895,
            longitude: 31.4812322,
            timestramp: "1587435556000",
            speed: 62,
            direction: 332
          },
          {
            latitude: 120.4886423,
            longitude: 31.4832045,
            timestramp: "1587435571000",
            speed: 54,
            direction: 330
          },
          {
            latitude: 120.4876163,
            longitude: 31.484728,
            timestramp: "1587435586000",
            speed: 45,
            direction: 328
          },
          {
            latitude: 120.4866543,
            longitude: 31.4860346,
            timestramp: "1587435601000",
            speed: 30,
            direction: 327
          },
          {
            latitude: 120.4860212,
            longitude: 31.4868333,
            timestramp: "1587435617000",
            speed: 25,
            direction: 324
          },
          {
            latitude: 120.4852825,
            longitude: 31.4878958,
            timestramp: "1587435632000",
            speed: 38,
            direction: 325
          },
          {
            latitude: 120.4840498,
            longitude: 31.489434,
            timestramp: "1587435647000",
            speed: 53,
            direction: 325
          },
          {
            latitude: 120.4826449,
            longitude: 31.4911216,
            timestramp: "1587435662000",
            speed: 56,
            direction: 321
          },
          {
            latitude: 120.4810466,
            longitude: 31.4927607,
            timestramp: "1587435677000",
            speed: 54,
            direction: 320
          },
          {
            latitude: 120.4794185,
            longitude: 31.49454,
            timestramp: "1587435692000",
            speed: 68,
            direction: 321
          },
          {
            latitude: 120.477771,
            longitude: 31.4963937,
            timestramp: "1587435707000",
            speed: 59,
            direction: 323
          },
          {
            latitude: 120.4765694,
            longitude: 31.4984609,
            timestramp: "1587435722000",
            speed: 70,
            direction: 345
          },
          {
            latitude: 120.4766613,
            longitude: 31.5012505,
            timestramp: "1587435737000",
            speed: 81,
            direction: 13
          },
          {
            latitude: 120.4779811,
            longitude: 31.5041902,
            timestramp: "1587435752000",
            speed: 91,
            direction: 26
          },
          {
            latitude: 120.4799923,
            longitude: 31.5073482,
            timestramp: "1587435767000",
            speed: 92,
            direction: 27
          },
          {
            latitude: 120.4821525,
            longitude: 31.5107482,
            timestramp: "1587435782000",
            speed: 112,
            direction: 27
          },
          {
            latitude: 120.4844122,
            longitude: 31.5144046,
            timestramp: "1587435797000",
            speed: 107,
            direction: 28
          },
          {
            latitude: 120.4867024,
            longitude: 31.5181118,
            timestramp: "1587435812000",
            speed: 116,
            direction: 28
          },
          {
            latitude: 120.4890519,
            longitude: 31.5219617,
            timestramp: "1587435827000",
            speed: 115,
            direction: 27
          },
          {
            latitude: 120.4914832,
            longitude: 31.5257784,
            timestramp: "1587435842000",
            speed: 117,
            direction: 28
          },
          {
            latitude: 120.4938827,
            longitude: 31.5295657,
            timestramp: "1587435857000",
            speed: 113,
            direction: 28
          },
          {
            latitude: 120.4962479,
            longitude: 31.5333605,
            timestramp: "1587435872000",
            speed: 117,
            direction: 28
          },
          {
            latitude: 120.4986752,
            longitude: 31.5371967,
            timestramp: "1587435887000",
            speed: 116,
            direction: 28
          },
          {
            latitude: 120.5012315,
            longitude: 31.5409102,
            timestramp: "1587435902000",
            speed: 114,
            direction: 31
          },
          {
            latitude: 120.5040233,
            longitude: 31.5444862,
            timestramp: "1587435917000",
            speed: 112,
            direction: 36
          },
          {
            latitude: 120.5064765,
            longitude: 31.5471952,
            timestramp: "1587435932000",
            speed: 75,
            direction: 37
          },
          {
            latitude: 120.5081163,
            longitude: 31.5488441,
            timestramp: "1587435947000",
            speed: 45,
            direction: 40
          },
          {
            latitude: 120.509463,
            longitude: 31.5500941,
            timestramp: "1587435962000",
            speed: 50,
            direction: 40
          },
          {
            latitude: 120.5110885,
            longitude: 31.5515948,
            timestramp: "1587435977000",
            speed: 58,
            direction: 41
          },
          {
            latitude: 120.5129688,
            longitude: 31.5533231,
            timestramp: "1587435992000",
            speed: 66,
            direction: 42
          },
          {
            latitude: 120.5153963,
            longitude: 31.5555512,
            timestramp: "1587436007000",
            speed: 96,
            direction: 42
          },
          {
            latitude: 120.518382,
            longitude: 31.558309,
            timestramp: "1587436022000",
            speed: 104,
            direction: 41
          },
          {
            latitude: 120.5213711,
            longitude: 31.561157,
            timestramp: "1587436037000",
            speed: 102,
            direction: 42
          },
          {
            latitude: 120.5241372,
            longitude: 31.5637695,
            timestramp: "1587436052000",
            speed: 83,
            direction: 40
          },
          {
            latitude: 120.5263244,
            longitude: 31.5663771,
            timestramp: "1587436067000",
            speed: 84,
            direction: 31
          },
          {
            latitude: 120.5281534,
            longitude: 31.5692285,
            timestramp: "1587436082000",
            speed: 88,
            direction: 25
          },
          {
            latitude: 120.529652,
            longitude: 31.5723777,
            timestramp: "1587436097000",
            speed: 93,
            direction: 18
          },
          {
            latitude: 120.530786,
            longitude: 31.5762677,
            timestramp: "1587436112000",
            speed: 114,
            direction: 10
          },
          {
            latitude: 120.5313117,
            longitude: 31.5805571,
            timestramp: "1587436127000",
            speed: 119,
            direction: 5
          },
          {
            latitude: 120.5320507,
            longitude: 31.5849722,
            timestramp: "1587436142000",
            speed: 117,
            direction: 10
          },
          {
            latitude: 120.5332895,
            longitude: 31.5893929,
            timestramp: "1587436157000",
            speed: 121,
            direction: 14
          },
          {
            latitude: 120.5349798,
            longitude: 31.5938523,
            timestramp: "1587436172000",
            speed: 119,
            direction: 20
          },
          {
            latitude: 120.5367485,
            longitude: 31.5976527,
            timestramp: "1587436187000",
            speed: 102,
            direction: 20
          },
          {
            latitude: 120.5382292,
            longitude: 31.6013459,
            timestramp: "1587436202000",
            speed: 101,
            direction: 15
          },
          {
            latitude: 120.5389361,
            longitude: 31.6050953,
            timestramp: "1587436217000",
            speed: 107,
            direction: 5
          },
          {
            latitude: 120.5390498,
            longitude: 31.6090216,
            timestramp: "1587436232000",
            speed: 99,
            direction: 357
          },
          {
            latitude: 120.5387724,
            longitude: 31.6122427,
            timestramp: "1587436247000",
            speed: 59,
            direction: 355
          },
          {
            latitude: 120.538794,
            longitude: 31.6141762,
            timestramp: "1587436262000",
            speed: 51,
            direction: 11
          },
          {
            latitude: 120.5398729,
            longitude: 31.6156392,
            timestramp: "1587436276000",
            speed: 45,
            direction: 20
          },
          {
            latitude: 120.5398908,
            longitude: 31.6158672,
            timestramp: "1587436278000",
            speed: 44,
            direction: 1
          },
          {
            latitude: 120.5398056,
            longitude: 31.616073,
            timestramp: "1587436280000",
            speed: 43,
            direction: 340
          },
          {
            latitude: 120.5395479,
            longitude: 31.6163203,
            timestramp: "1587436283000",
            speed: 44,
            direction: 310
          },
          {
            latitude: 120.5392784,
            longitude: 31.6164268,
            timestramp: "1587436285000",
            speed: 47,
            direction: 295
          },
          {
            latitude: 120.5389769,
            longitude: 31.6165049,
            timestramp: "1587436287000",
            speed: 51,
            direction: 286
          },
          {
            latitude: 120.5386453,
            longitude: 31.6165693,
            timestramp: "1587436289000",
            speed: 54,
            direction: 285
          },
          {
            latitude: 120.5364305,
            longitude: 31.6173391,
            timestramp: "1587436304000",
            speed: 48,
            direction: 314
          },
          {
            latitude: 120.535739,
            longitude: 31.6188074,
            timestramp: "1587436319000",
            speed: 31,
            direction: 339
          },
          {
            latitude: 120.5350856,
            longitude: 31.6200709,
            timestramp: "1587436334000",
            speed: 43,
            direction: 340
          },
          {
            latitude: 120.5347885,
            longitude: 31.6224311,
            timestramp: "1587436349000",
            speed: 69,
            direction: 1
          },
          {
            latitude: 120.5349635,
            longitude: 31.6234286,
            timestramp: "1587436356000",
            speed: 47,
            direction: 19
          },
          {
            latitude: 120.5351021,
            longitude: 31.6236166,
            timestramp: "1587436358000",
            speed: 44,
            direction: 34
          },
          {
            latitude: 120.535298,
            longitude: 31.6237319,
            timestramp: "1587436360000",
            speed: 40,
            direction: 54
          },
          {
            latitude: 120.5355278,
            longitude: 31.6237758,
            timestramp: "1587436362000",
            speed: 37,
            direction: 76
          },
          {
            latitude: 120.5358794,
            longitude: 31.6236595,
            timestramp: "1587436365000",
            speed: 35,
            direction: 115
          },
          {
            latitude: 120.5360544,
            longitude: 31.6233812,
            timestramp: "1587436368000",
            speed: 36,
            direction: 156
          },
          {
            latitude: 120.536048,
            longitude: 31.6230594,
            timestramp: "1587436371000",
            speed: 38,
            direction: 190
          },
          {
            latitude: 120.5358884,
            longitude: 31.6227536,
            timestramp: "1587436374000",
            speed: 43,
            direction: 210
          },
          {
            latitude: 120.5356788,
            longitude: 31.6225602,
            timestramp: "1587436376000",
            speed: 49,
            direction: 223
          },
          {
            latitude: 120.535416,
            longitude: 31.6223839,
            timestramp: "1587436378000",
            speed: 54,
            direction: 230
          },
          {
            latitude: 120.5331515,
            longitude: 31.6213511,
            timestramp: "1587436393000",
            speed: 52,
            direction: 249
          },
          {
            latitude: 120.5308565,
            longitude: 31.6209446,
            timestramp: "1587436408000",
            speed: 55,
            direction: 262
          },
          {
            latitude: 120.5284696,
            longitude: 31.6207731,
            timestramp: "1587436423000",
            speed: 48,
            direction: 266
          },
          {
            latitude: 120.5274841,
            longitude: 31.6209465,
            timestramp: "1587436439000",
            speed: 6,
            direction: 305
          },
          {
            latitude: 120.5277662,
            longitude: 31.6208191,
            timestramp: "1587436453000",
            speed: 10,
            direction: 263
          },
          {
            latitude: 120.5270289,
            longitude: 31.6207809,
            timestramp: "1587436468000",
            speed: 14,
            direction: 300
          },
          {
            latitude: 120.5269455,
            longitude: 31.6211394,
            timestramp: "1587436474000",
            speed: 31,
            direction: 356
          },
          {
            latitude: 120.5269455,
            longitude: 31.6211394,
            timestramp: "1587436477000",
            speed: 30,
            direction: 356
          },
          {
            latitude: 120.5268765,
            longitude: 31.6233002,
            timestramp: "1587436492000",
            speed: 60,
            direction: 357
          },
          {
            latitude: 120.5268589,
            longitude: 31.6242142,
            timestramp: "1587436507000",
            speed: 7,
            direction: 349
          },
          {
            latitude: 120.5267969,
            longitude: 31.6252332,
            timestramp: "1587436522000",
            speed: 58,
            direction: 357
          },
          {
            latitude: 120.5266555,
            longitude: 31.6275033,
            timestramp: "1587436537000",
            speed: 35,
            direction: 352
          },
          {
            latitude: 120.5266704,
            longitude: 31.6278729,
            timestramp: "1587436582000",
            speed: 26,
            direction: 357
          },
          {
            latitude: 120.5265876,
            longitude: 31.6296479,
            timestramp: "1587436597000",
            speed: 56,
            direction: 357
          },
          {
            latitude: 120.5265187,
            longitude: 31.6317287,
            timestramp: "1587436612000",
            speed: 54,
            direction: 358
          },
          {
            latitude: 120.5264736,
            longitude: 31.6330893,
            timestramp: "1587436627000",
            speed: 22,
            direction: 358
          },
          {
            latitude: 120.526452,
            longitude: 31.6336126,
            timestramp: "1587436672000",
            speed: 22,
            direction: 356
          },
          {
            latitude: 120.5264124,
            longitude: 31.6344749,
            timestramp: "1587436687000",
            speed: 26,
            direction: 354
          },
          {
            latitude: 120.5263787,
            longitude: 31.6352835,
            timestramp: "1587436702000",
            speed: 21,
            direction: 356
          },
          {
            latitude: 120.5263267,
            longitude: 31.6364322,
            timestramp: "1587436717000",
            speed: 36,
            direction: 358
          },
          {
            latitude: 120.5263007,
            longitude: 31.6378804,
            timestramp: "1587436732000",
            speed: 29,
            direction: 359
          },
          {
            latitude: 120.5262951,
            longitude: 31.6383922,
            timestramp: "1587436747000",
            speed: 5,
            direction: 353
          },
          {
            latitude: 120.5262812,
            longitude: 31.6385927,
            timestramp: "1587436807000",
            speed: 9,
            direction: 359
          },
          {
            latitude: 120.5262197,
            longitude: 31.6392187,
            timestramp: "1587436822000",
            speed: 18,
            direction: 1
          },
          {
            latitude: 120.5262241,
            longitude: 31.6398234,
            timestramp: "1587436927000",
            speed: 33,
            direction: 356
          },
          {
            latitude: 120.5239905,
            longitude: 31.686592,
            timestramp: "1587437404000",
            speed: 33,
            direction: 5
          },
          {
            latitude: 120.5240009,
            longitude: 31.6869087,
            timestramp: "1587437407000",
            speed: 39,
            direction: 4
          },
          {
            latitude: 120.5242119,
            longitude: 31.6895062,
            timestramp: "1587437423000",
            speed: 62,
            direction: 5
          },
          {
            latitude: 120.5242242,
            longitude: 31.689828,
            timestramp: "1587437425000",
            speed: 60,
            direction: 5
          },
          {
            latitude: 120.525034,
            longitude: 31.6979052,
            timestramp: "1587437508000",
            speed: 38,
            direction: 3
          },
          {
            latitude: 120.5250361,
            longitude: 31.6981463,
            timestramp: "1587437511000",
            speed: 39,
            direction: 3
          },
          {
            latitude: 120.5250065,
            longitude: 31.6987419,
            timestramp: "1587437518000",
            speed: 23,
            direction: 343
          },
          {
            latitude: 120.5248047,
            longitude: 31.698906,
            timestramp: "1587437522000",
            speed: 28,
            direction: 292
          },
          {
            latitude: 120.5243343,
            longitude: 31.6989705,
            timestramp: "1587437526000",
            speed: 41,
            direction: 275
          },
          {
            latitude: 120.5092162,
            longitude: 31.700633,
            timestramp: "1587437762000",
            speed: 11,
            direction: 3
          },
          {
            latitude: 120.509415,
            longitude: 31.7014512,
            timestramp: "1587438348000",
            speed: 2,
            direction: 46
          },
          {
            latitude: 120.5102815,
            longitude: 31.7013752,
            timestramp: "1587527665000",
            speed: 0,
            direction: 0
          }
        ];
        if (temp.length > 0) {
          temp.map(ele => {
            let tempArr = [];
            tempArr.push(ele.latitude);
            tempArr.push(ele.longitude);
            //格式转换后push进电池经纬度
            that.markerArr.push(tempArr);
          });
          that.$nextTick(() => {
            //画线
            that.playLine();
          });
        } else {
          that.$notify({
            title: "提醒",
            message: "此电池此时间范围内暂无轨迹！",
            type: "warning",
            duration: 4000
          });
        }
      }, 1000);
    },
    //画线
    playLine() {
      let that = this;
      //初始化起点终点
      that.initStartEnd();
      that.marker = new AMap.Marker({
        map: that.map,
        //小车出初始位置
        position: that.markerArr[0],
        icon: "https://webapi.amap.com/images/car.png",
        offset: new AMap.Pixel(-26, -13),
        autoRotation: true,
        angle: -90
      });
      // 绘制轨迹
      that.polyline = new AMap.Polyline({
        map: that.map,
        path: that.markerArr,
        showDir: true,
        strokeColor: "#28F", //线颜色
        // strokeOpacity: 1,     //线透明度
        strokeWeight: 6 //线宽
        // strokeStyle: "solid"  //线样式
      });
      //小车移动的轨迹线
      that.passedPolyline = new AMap.Polyline({
        map: that.map,
        // path: that.markerArr,
        strokeColor: "#AF5", //线颜色
        // strokeOpacity: 1,     //线透明度
        strokeWeight: 6 //线宽
        // strokeStyle: "solid"  //线样式
      });
      //marker移动时把经纬度传给小车移动的轨迹线
      that.marker.on("moving", function(e) {
        //设置组成该折线的节点数组
        that.passedPolyline.setPath(e.passedPath);
      });
      // marker开始移动
      that.marker.moveAlong(that.markerArr, 200);
      //自适应放大
      setTimeout(() => {
        that.map.setFitView();
      }, 500);
    },
    //初始化起点终点
    initStartEnd() {
      let that = this;
      //将icon添加进marker
      let startMarker = new AMap.Marker({
        position: new AMap.LngLat(that.markerArr[0][0], that.markerArr[0][1]),
        icon: that.startIcon,
        offset: new AMap.Pixel(-13, -30)
      });
      //将icon添加进marker
      let endMarker = new AMap.Marker({
        position: new AMap.LngLat(
          that.markerArr.pop()[0],
          that.markerArr.pop()[1]
        ),
        icon: that.endIcon,
        offset: new AMap.Pixel(-13, -30)
      });
      // 将 markers 添加到地图
      that.map.add([startMarker, endMarker]);
    },
    //关闭弹窗时
    closeBatteryHistroy() {
      this.selectedDate = [];
      this.markerArr = []; //电池经纬度,也用于画线
    }
  }
};
</script>

<style scoped>
.selecte-date-tit {
  font-size: 15px;
  font-weight: 550;
}
</style>
