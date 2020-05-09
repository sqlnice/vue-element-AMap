
## 使用说明
在index.html中引入 (自己去高德官网申请key，注意申请的key类型要为WEB端)
```
<script type="text/javascript" src="https://webapi.amap.com/maps?v=1.4.15&key=yourkey"></script>
```
在vue.config.js添加
```
module.exports = {
  configureWebpack: {
    externals: {
      'AMap': 'AMap' // 高德地图配置
    }
  }
```
}
## 效果图
![image](https://zechao-resources.oss-cn-shanghai.aliyuncs.com/tempfile/map/index.png)

![image](https://zechao-resources.oss-cn-shanghai.aliyuncs.com/tempfile/map/index2.png)

![image](https://zechao-resources.oss-cn-shanghai.aliyuncs.com/tempfile/map/index3.png)
## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```