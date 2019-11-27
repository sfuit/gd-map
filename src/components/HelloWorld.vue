<template>
  <div class="box">
    <div id="container" style="width:100%;height:500px;position:relative;"></div>
  </div>
</template>
<script>
import AMap from "AMap";
export default {
  data() {
    return {
      lng: "",
      lat: "",
      cneter: "",
      city: ""
    };
  },
  mounted() {
    this.getLocal();
  },
  methods: {
    getLocal() {
      let that = this;
      var map = new AMap.Map("container", {
        resizeEnable: true
      });
      AMap.plugin("AMap.Geolocation", function() {
        var geolocation = new AMap.Geolocation({
          enableHighAccuracy: true, //是否使用高精度定位，默认:true
          timeout: 10000, //超过10秒后停止定位，默认：5s
          buttonPosition: "RB", //定位按钮的停靠位置
          buttonOffset: new AMap.Pixel(10, 20), //定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
          zoomToAccuracy: true //定位成功后是否自动调整地图视野到定位点
        });
        map.addControl(geolocation);
        geolocation.getCurrentPosition(function(status, result) {
          if (status == "complete") {
            that.onComplete(result);
            // that.aaa(result.position.lat,result.position.lng)
          } else {
            that.onError(result);
          }
        });
      });
      AMap.plugin(["AMap.ToolBar", "AMap.Scale"], function() {
        map.addControl(new AMap.ToolBar());
        map.addControl(new AMap.Scale());
      });
    },
    aMapSearchNearBy(centerPoint, city) {
      AMap.service(["AMap.PlaceSearch"], function() {
        var placeSearch = new AMap.PlaceSearch({
          pageSize: 10, // 每页10条
          pageIndex: 1, // 获取第一页
          city: city // 指定城市名(如果你获取不到城市名称，这个参数也可以不传，注释掉)
        });

        // 第一个参数是关键字，这里传入的空表示不需要根据关键字过滤
        // 第二个参数是经纬度，数组类型
        // 第三个参数是半径，周边的范围
        // 第四个参数为回调函数
        placeSearch.searchNearBy("", centerPoint, 1000, function(
          status,
          result
        ) {
          if (result.info === "OK") {
            var locationList = result.poiList.pois; // 周边地标建筑列表
            window.console.log(locationList);
            // 生成地址列表html
            // createLocationHtml(locationList);
          } else {
            window.console.log("获取位置信息失败!");
          }
        });
      });
    },
    //解析定位结果
    onComplete(data) {
      window.console.log(data);
      this.lat = data.position.lat;
      this.lng = data.position.lng;
      this.center = [this.lng, this.lat];
      this.city = data.addressComponent.city;
      this.aMapSearchNearBy([this.lng, this.lat], data.addressComponent.city);
      // var acc=data.position
      // // document.getElementById('status').innerHTML='定位成功'
      // var str = [];
      // str.push('定位结果：' + data.position);
      // str.push('定位类别：' + data.location_type);
      // if(data.accuracy){
      //   str.push('精度：' + data.accuracy + ' 米');
      // }//如为IP精确定位结果则没有精度信息
      // str.push('是否经过偏移：' + (data.isConverted ? '是' : '否'));
      // alert(str)
      // document.getElementById('result').innerHTML = str.join('<br>');
    },
    //解析定位错误信息
    onError(data) {
      window.console.log(data);
      alert("地理信息获取失败");
    }
  }
};
</script>