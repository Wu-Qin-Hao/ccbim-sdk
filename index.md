# 基本使用

## 1.采用script标签引入的方式

> 引入ccbim.iife.js后window下就有`$CCBIM$`对象

```html
<!DOCTYPE html>
<html>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
<head>
  <title>CCBIM_WEBVIEW_SDK</title>
</head>
<style>
  * {
    margin: 0;
    padding: 0;
  }
  body {
    overflow: hidden;
  }
</style>

<body>
  <div id="bimView" style="position: relative;width: 100vw;height: 100vh;"></div>
</body>
<script src="./.temp/ccbim.iife.js"></script>
<script>
  // 渲染区域
  let root = document.getElementById('bimView')
  // 配置项
  let options = {
    urlIp: 'https://ccbim.pinming.cn',
    versionId: 'a8c500bf-7a90-423e-f70a-9aba6d439569',
    type: 'model', // 注意加载模型用model，加载图纸用dwg
    operationBar: {
      showBar: true,
    },
    selectEntityAction: [
      {
        name: '发起任务',
        key: 1,
        action: () => {console.log('发起任务')}
      },
      {
        name: '添加信息',
        key: 2,
        action: () => {console.log('添加信息')}
      },
      {
        name: '标记评论',
        key: 3,
        action: () => {console.log('标记评论')}
      },
      {
        name: '二维码',
        key: 4,
        action: () => {console.log('二维码')}
      }
    ]
  }

  // 传入渲染区域和配置项目，new出实例对象
  const viewRender = new $CCBIM$.ViewRender(root, options)
  // 执行run加载
  viewRender.run()

  // 绑定模型里的选中构件事件
  viewRender.addEventListener('selectedEntity', (data) => {
    console.log(data)
  })
  // 绑定图纸里的选中位置事件
  // viewRender.addEventListener('addDwgMark', (data) => {
  //   console.log(data)
  // })
  // 绑定加载完成事件
  viewRender.addEventListener('renderFinish', function (event) {
    console.log(event)
  });
</script>
<!-- <script>
  // 全景的加载（全景只能用于策划软件导出的全景）
  let root = document.getElementById('bimView')
  let options = {
    urlIp: 'https://ccbim.pinming.cn',
    versionId: 'aa0e00f6-202b-1eb6-ee7b-1a2e874ea572',
  }
  const panorama = new $CCBIM$.Panorama(root, options)
  panorama.run()
</script> -->
</html>
```

# 配置项说明

`$CCBIM$.ViewRender`用来加载模型图纸，`$CCBIM$.Panorama`用来加载全景

## root

> 渲染区域。类似于 let root = document.getElementById('bimView')

## options

> 加载配置项

```javascript
let options = {
  urlIp: '模型图纸请求的地址',
  versionId: '模型图纸的唯一标识符',
  type: '类型:"model"|"dwg"',
  operationBar: { // 底部操作栏配置
    showBar: true, // 默认true显示
  },
  selectEntityAction: { // 自定义右键弹窗功能
    visible: false, // 默认false不显示
    list: [
      {
        name: '发起任务', // 显示的名称
        key: 1, // key区分
        action: () => { console.log('发起任务') } // 回调事件
      },
      {
        name: '添加信息',
        key: 2,
        action: () => { console.log('添加信息') }
      },
      {
        name: '标记评论',
        key: 3,
        action: () => { console.log('标记评论') }
      },
      {
        name: '二维码',
        key: 4,
        action: () => { console.log('二维码') }
      }
    ]
  }
}
```

# 绑定事件监听

## selectedEntity

> 绑定模型里的选中构件事件

```javascript
  viewRender.addEventListener('selectedEntity', (data) => {
    console.log(data.object)
    //  {
    //    cameraPosition: '照相机位置',
    //    cameraTarget: '照相机target方向',
    //    cameraUp: '照相机up方向',
    //    clickPointPosition: '点击点位置信息',
    //    comid: 'comid',
    //    comtype: 'comtype',
    //    flatBuffer: '记录视口信息，经过了编码，包含照相机位置，剖切信息，楼层构件显隐信息等',
    //    floorID: '楼层id',
    //    floorName: '楼层名称',
    //    handle: '构件handle，和floorID组合用来查找高亮构件'
    //  }
  })
```

## addDwgMark

> 绑定图纸里的选中位置事件

```javascript
  viewRender.addEventListener('addDwgMark', (data) => {
    console.log(data.object)
    // {
    //   DWGMark: '点击点位置信息',
    //   cameraPosition: '照相机位置',
    //   cameraTarget: '照相机target方向',
    //   cameraUp: '照相机up方向',
    //   isDWG: '是否是图纸',
    //   offset: '同一图纸的不同版本的偏移值',
    //   outWidth: '视口宽高',
    //   scale: '图纸缩放比'
    // }
  })
```

# API接口

> API接口在interfaceApi原型链上

## setBgColor

> 设置背景色

```javascript
  /**
   * topColor与bottomColor不一样则背景色显示渐变色，一样则显示纯色
   * @param {String} topColor 顶部颜色'#fff'
   * @param {String} bottomColor 底部颜色'#333
   */
  viewRender.interfaceApi.setBgColor(topColor, bottomColor)
```

## setViewRenderWith

> 修改渲染区域宽度

```javascript
  /**
   * @param {Number} width 宽度值
   */
  viewRender.interfaceApi.setViewRenderWith(width)
```

## setViewRenderHeight

> 修改渲染区域高度

```javascript
  /**
   * @param {Number} height 高度值
   */
  viewRender.interfaceApi.setViewRenderHeight(height)
```

## setMarkPointList

> 设置标注点

```javascript
  /**
   * @param {Array} arr 标记点数组
   */
  viewRender.interfaceApi.setMarkPointList(arr)

  // 标记点数组要按规定的数据结构。
  {
    id: '区分标注点', // 必传
    floorId: '楼层id', // 必传
    floorName: '楼层名称',
    markPosition: // 必传 -- 模型构标记点位置（"2035D;-7,1,9"）/图纸标记点位置（"{"position":"-9,-2","offset":[2,2,0]}"）, 
    iconText: '标注点上的文字',
    iconImg: '标注点的图片样式',
    photo: '图片信息',
    labelText: '文本信息',
    selfDefiningData: '自定义存储信息'
    clickPointCallback: '点击标记点触发函数'
  }

  // markPosition 模型如何生成
  let markPosition =
    selectedEntity.handle + ';' +
    selectedEntity.clickPointPosition.x + ',' +
    selectedEntity.clickPointPosition.y + ',' +
    selectedEntity.clickPointPosition.z

  // markPosition 图纸如何生成
  let markPosition = JSON.stringify({
    position: `${addDwgMark.DWGMark.x},${addDwgMark.DWGMark.y}`,
    offset: addDwgMark.offset,
  })

```