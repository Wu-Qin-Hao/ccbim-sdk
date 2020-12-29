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
  <!--id用于区分不同的渲染，样式要传绝对定位，宽高的渲染区域-->
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

# 注意事项

> ccbim.iife.js中有引入两个特殊的文件(没有被打包进去，需要特殊引入和修改文件路径)：ComputeWireframe.js(worker线程计算模型线框)和gentilis_regular.typeface.josn(字体)

* 这两个文件不影响加载，但是影响线框模式和测量的结果字体显示。

* 目前的解决方案：将这两个文件更新到项目里，打开ccbim.iife.js搜索这两个文件名，改成正确的文件路径

* 后期的解决方案：解决worker线程和字体的引入问题，都打包进ccbim.js中，无需特殊引入。提供npm安装的模式。

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

## renderFinish

> 绑定加载完成事件

```javascript
  viewRender.addEventListener('renderFinish', function (event) {
    console.log(event)
  });
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

* 添加标记点的业务流程如下
* 1. 模型里绑定选中构件事件监听 selectedEntity（图纸里绑定选中位置事件监听 selectedEntity）
* 2. 获取返回的信息，做业务端流程操作，按照业务存储有效信息
* 3. 存储成功后获取列表信息，按规定的数据结构传入setMarkPointList方法
* 4. 显示标记点

> 删除标记点

* 删除属于业务端操作，只要传入setMarkPointList方法的列表少一条，标记点显示就少一个
* 1. 业务端删除存储的列表里的某条标记信息
* 2. 删除成功后获取最新的列表信息，传入setMarkPointList方法

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
    color: '标注点颜色',
    photo: '图片信息',
    labelText: '文本信息',
    selfDefiningData: {} // '自定义存储信息'
    clickPointCallback: (result) => { console.log(result) } // '点击标记点触发函数'
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

  // clickPointCallback 回调函数result返回值说明
  {
    type: 'markClick',
    left: '标记点left值',
    top: '标记点top值',
    data: 'selfDefiningData自定义信息'
  }

```

## bounceMarkPoint

> 跳动标注点

```javascript
  viewRender.interfaceApi.bounceMarkPoint(id)
```

## setMarkVisible

> 显隐所有标记点

```javascript
  /**
   * @param {String} domId 渲染区域的元素id
   * @param {Boolean} visible 显隐
   */
  viewRender.interfaceApi.setMarkVisible(domId, visible)
```

## setModelViewInfo

> 设置模型viewInfo视口（高亮构件，视角改变，剖切状态，楼层显隐等状态）

```javascript
  /**
   * 设置模型viewInfo视口接口（以下信息都能在selectedEntity中找到）
   * @param {String} floorID 楼层id
   * @param {String} handle 构件handle
   * @param {String} floorName 楼层name(兼容老数据)可传空
   * @param {String} flatBuffer viewInfo数据
   */
  viewRender.interfaceApi.setModelViewInfo(floorID, handle, floorName, flatBuffer)
```

## setSelectGeometry

> 单独设置高亮构件

```javascript
  /**
   * 设置模型构件高亮（以下信息都能在selectedEntity中找到）
   * @param {String} floorID 楼层id
   * @param {String} handle 构件handle
   * @param {String} floorName 楼层name(兼容老数据)可传空
   */
  viewRender.interfaceApi.setSelectGeometry(floorID, handle, floorName)
```

## clearAllHighlightEntity

> 清空高亮构件

```javascript
  viewRender.interfaceApi.clearAllHighlightEntity()
```

## setCameraModelView

> 单独设置照相机位置

```javascript
  /**
   * 设置照相机位置（以下信息都能在selectedEntity中找到）
   * @param {Vector3} up 照相机up方向
   * @param {Vector3} target 照相机target方向
   * @param {Vector3} position 照相机位置信息
   */
  viewRender.interfaceApi.setCameraModelView(up, target, position)
```

## setCameraCADView

> 设置图纸viewInfo视口（视角改变）

```javascript
  /**
   * 设置图纸viewInfo视口接口（以下信息都能在addDwgMark中找到）
   * @param {String} port 视口信息
   * @param {String} markPoint 图纸标记点位置
   */
  viewRender.interfaceApi.setCameraCADView(port, markPoint)

  // port 如何生成
  let port = `${addDwgMark.cameraPosition.x},${addDwgMark.cameraPosition.y},${addDwgMark.outWidth.width},${addDwgMark.outWidth.height}`

  // markPoint 如何生成
  let markPoint = JSON.stringify({
    position: `${addDwgMark.DWGMark.x},${addDwgMark.DWGMark.y}`,
    offset: addDwgMark.offset,
  })
```

## getViewInfo

> 模型 在没有点选构件的情况下获取视口相关信息(图纸此方法无效，获取的数据不完整)

```javascript
  let viewInfo = viewRender.interfaceApi.getViewInfo()

  // return 返回的数据结构内容如下
  {
    cameraPosition: '照相机位置',
    cameraTarget: '照相机target方向',
    cameraUp: '照相机up方向',
    flatBuffer: '记录视口信息，经过了编码，包含照相机位置，剖切信息，楼层构件显隐信息等'
  }
```

## canvasToImageBlod

> 获取当前canvas的图片的Blob数据

```javascript
  viewRender.interfaceApi.canvasToImageBlod()
```

## canvasToImage

> 获取当前canvas的图片

```javascript
  viewRender.interfaceApi.canvasToImage()
```

## openModelRotate

> 开启模型自动旋转

```javascript
  viewRender.interfaceApi.openModelRotate()
```

## closeModelRotate

> 关闭模型自动旋转

```javascript
  viewRender.interfaceApi.closeModelRotate()
```

## setSingleClick

> 是否开启单击选中构件

```javascript
  viewRender.interfaceApi.setSingleClick(visible)
```

## 是否开启phone端dwg图纸打标记点

> 是否开启单击选中构件

```javascript
  viewRender.interfaceApi.setIsPhoneDwgAddMark(visible)
```

## closeBarAllOperation

> 关闭操作栏所有已开启的功能

```javascript
  viewRender.interfaceApi.closeBarAllOperation()
```

## setVisibleBar

> 显隐底下操作栏

```javascript
  /**
   * @param {String} domId 渲染区域的元素id
   * @param {Boolean} visible 显隐
   */
  viewRender.interfaceApi.setVisibleBar(domId, visible)
```

## dispose

> 清空模型图纸，释放内存

```javascript
  viewRender.interfaceApi.dispose()
```