# Leaflet.Canvas-Flowmap-Layer

 `Leaflet.Canvas-Flowmap-Layer` 是一个[LeafletJS](http://leafletjs.com/)自定义图层，在HTML的Canvas画布上以贝塞尔曲线绘制事物、想法、人的流动。 GeoJSON 点被转换到像素空间，这样就可以将点和曲线绘制到 [HTMLCanvasElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement)上。

**演示**：查看 [功能比较演示](https://jwasilgeo.github.io/Leaflet.Canvas-Flowmap-Layer/docs/comparison) 和 [简单演示](https://jwasilgeo.github.io/Leaflet.Canvas-Flowmap-Layer/docs/main)。

**专业提示**：这是 [sarahbellum/Canvas-Flowmap-Layer](https://www.github.com/sarahbellum/Canvas-Flowmap-Layer)库的leaflet版本， [sarahbellum/Canvas-Flowmap-Layer](https://www.github.com/sarahbellum/Canvas-Flowmap-Layer)库是基于 ArcGIS API 的JavaScript库。你可以去那里学习更多！你可以阅读 [这篇博客公告](https://cerebellumaps.wordpress.com/2017/04/20/flow-mapping-with-leaflet/)。

**目录**
- [目的和背景](#目的和背景)
- [选项和更多开发者信息](#选项和更多开发者信息)
    - [数据关系](#数据关系)
    - [动画](#动画)
    - [交互](#交互)
    - [符号](#符号)
- [API](#api)
    - [构造函数概要](#构造函数概要)
    - [选项和属性概要](#选项和属性概要)
    - [方法概要](#方法概要)
    - [事件概要](#事件概要)
- [许可](#许可)

[![screenshot](https://raw.githubusercontent.com/jwasilgeo/Leaflet.Canvas-Flowmap-Layer/master/img/img_01.png)](https://jwasilgeo.github.io/Leaflet.Canvas-Flowmap-Layer/docs/main)

## 目的和背景

流地图是制图的必要内容，但仍然缺乏经验设计规则 [(珍妮等人. 2016)](http://cartography.oregonstate.edu/pdf/2017_Jenny_etal_DesignPrinciplesForODFlowMaps.pdf)。动态流映射的常见解决方案包括使用直线和测地线，两者都具有即时的视觉限制。使用贝塞尔曲线绘制流程图，其中使用相同的公式创建地图上的每个曲线，将重点放在两方面：

**1. 美学** 虽然直线并不是永远都很丑陋，但它们跨越全球数据集的重叠或融合就会很难看。使用相同公式创建的一系列贝塞尔曲线（即使显示过度丰富）有一个数学一致的流程，大大提高了地图的美学。

![canvas](https://raw.githubusercontent.com/sarahbellum/Canvas-Flowmap-Layer/master/img/img_01.png)  

**2. 定向符号系统** 曲线是凸的还是凹的取决于线的方向。这种符号系统可能是新的即时直观，但是这个规则是审美的真实性和一致性所必需的。它的价值是地图读者可以立即知道线的方向，而无需添加动画。

![canvas](https://raw.githubusercontent.com/sarahbellum/Canvas-Flowmap-Layer/master/img/img_02.png)

## 选项和更多开发者信息

### 数据关系

这个自定义图层支持 "One-to-many"、"many-to-one" 和 "one-to-one" 这三种起源到目的地的关系。

**重要信息**：开发人员必须格式化并提供特定格式的GeoJSON点要素集合。这个图层期望在每个GeoJSON点要素的属性中可以使用起始点和目标点的属性和空间坐标。

数据集合中单个GeoJSON点信息的示例，包括源和目标信息：

```json
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates":[109.6091129, 23.09653465]
  },
  "properties": {
    "origin_city_id": 238,
    "origin_city": "Hechi",
    "origin_country": "China",
    "origin_lon": 109.6091129,
    "origin_lat": 23.09653465,
    "destination_city_id": 1,
    "destination_city": "Sarh",
    "destination_country": "Chad",
    "destination_lon": 18.39002966,
    "destination_lat": 9.149969909
  }
}
```

更多信息请查看 [sarahbellum/Canvas-Flowmap-Layer#data-relationships](https://github.com/sarahbellum/Canvas-Flowmap-Layer#data-relationships) 。

### 动画

这个动画是依赖于 [tween.js 库](https://github.com/tweenjs/tween.js) 来帮助改变底层的行属性值，并提供许多不同的缓动功能和持续时间。查看下面的 `setAnimationDuration()` 和 `setAnimationEasing()` 方法介绍来了解更多信息。

**重要信息**：如果要使用动画，那么开发人员必须先加载tween.js库。

```html
<!-- 加载 tweening 动画库用于 CanvasFlowMapLayer -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/tween.js/16.6.0/Tween.min.js"></script>

<!-- 然后加载本插件 -->
<script src="your/path/to/src/CanvasFlowmapLayer.js"></script>
```

### 交互

你可以通过控制在任何时候显示或隐藏来改变用户和 `Leaflet.CanvasFlowmapLayer` 之间的交互。我们提供的演示案例展示了如何使用leaflet的`click` 和 `mouseover` 事件方法以及图层的`selectFeaturesForPathDisplay()`方法来实现这些功能。

举个例子，你可以在起始点元素（在地图上以`L.CircleMarker`的形式展示）上绑定一个 `click` 事件，然后 **添加** 你选择的贝塞尔曲线、 **减去** 你所选的或者建立一个 **新建** 选择。

另外，当构建图层来立即显示每个Bezier曲线你可以设置 `pathDisplayMode` 选项为 `'all'`。

### 符号

`Leaflet.Canvas-Flowmap-Layer` 有默认的符号来建立起始点的 `L.CircleMarker`标记、canvas贝塞尔曲线和运动的贝塞尔曲线。

默认的起始点`L.CircleMarker`标记样式可以使用图层构造器选项 `style()` 方法来更改，因为这个图层继承自 `L.GeoJSON`。

canvas贝塞尔曲线和运动的贝塞尔曲线的默认样式可以通过 `canvasBezierStyle` 和 `animatedCanvasBezierStyle`选项来修改，它是直接使用 HTMLCanvasElement stroke 和 line 样式属性名（而不是leaflet标记样式属性）。也可以通过唯一的属性值或类名来表示Bezier路径。

更多信息请看下面的API部分。

<!-- The provided demo pages show some examples of these symbol configuration objects (see config.js files). -->

## API

继承于 Leaflet v1 [`L.GeoJSON` layer](http://leafletjs.com/reference-1.0.3.html#geojson)。 `L.GeoJSON`层提供的所有属性、方法和事件都可以在`Leaflet.CanvasFlowmapLayer` 中适用，还具有下面描述的其他自定义功能。

### 构造函数概要

```javascript
var geoJsonFeatureCollection = {
  // 具有起止点属性信息的GeoJSON 点元素集合

  // 请参阅上面的讨论，演示和CSV示例数据源
};

var exampleFlowmapLayer = L.canvasFlowmapLayer(geoJsonFeatureCollection, {
  // 此自定义图层的必需属性，
  // 它依赖于你自己的数据的属性名称
  originAndDestinationFieldIds: {
    // 这里是最重要的配置信息...查看下面的文档说明

    // 然而，如果你的数据和源代码要求的数据格式相同，这个选项就不是必须的
  },

  // 一些自定义选项
  pathDisplayMode: 'selection',
  animationStarted: true,
  animationEasingFamily: 'Cubic',
  animationEasingType: 'In',
  animationDuration: 2000
}).addTo(map);
```

### 选项和属性概要

<table>
    <tr>
        <th>属性</th>
        <th>类型</th>
        <th>描述</th>
    </tr>
    <tr>
        <td><code>originAndDestinationFieldIds</code></td>
        <td><code>Object</code></td>
        <td>
            <strong>如果你的数据与图层源代码不具有相同的属性字段名称，则为必需。</strong> <br> 此对象告诉图层你唯一的起始和目标属性（字段）。起止点都需要拥有自己唯一的ID属性和几何定义。<a href="#originanddestinationfieldIds-示例代码">查看下面的示例代码</a>,其中包括最低要求的对象属性。
        </td>
    </tr>
    <tr>
        <td><code>style<code></td>
        <td><code>Function</code></td>
        <td>
            <strong>可选</strong> <br>
            此函数定义了起止点的符号样式属性。参见<a href="#style-示例代码">下面的示例代码</a>
        </td>
    </tr>
    <tr>
        <td><code>canvasBezierStyle<code></td>
        <td><code>Object</code></td>
        <td>
            <strong>可选</strong> <br>
            此对象定义在连接起止点的画布上绘制的非动画贝塞尔曲线的符号属性。参见上面的<a href="#符号">符号介绍</a>
       </td>
    </tr>
    <tr>
        <td><code>animatedCanvasBezierStyle<code></td>
        <td><code>Object</code></td>
        <td>
            <strong>可选</strong> <br>
            定义了直接在非动画贝塞尔曲线之上绘制的动画贝塞尔曲线的符号属性。参见上面的<a href="#符号">符号介绍</a>
       </td>
    </tr>
    <tr>
        <td><code>pathDisplayMode<code></td>
        <td><code>String</code></td>
        <td>
            <strong>可选</strong><br>
            有效值：<code>'selection'</code> 或者 <code>'all'</code><br>
            默认值为：<code>'all'</code>
       </td>
    </tr>
    <tr>
        <td><code>wrapAroundCanvas<code></td>
        <td><code>Boolean</code></td>
        <td>
            <strong>可选</strong><br>确保画布特征在超出+/- 180度经度时被绘制。<br>默认值为：<code>'true'</code>
       </td>
    </tr>
    <tr>
        <td><code>animationStarted<code></td>
        <td><code>Boolean</code></td>
        <td>
            <strong>可选</strong><br>构造过程中可以设置，但是当图层初始化完成后你需要使用<code>playAnimation()</code>和<code>stopAnimation()</code>方法控制动画。<br>默认值为：<code>'false'</code>
       </td>
    </tr>
    <tr>
        <td><code>animationDuration<code></td>
        <td><code></code></td>
        <td>
            查看下面的<code>setAnimationDuration()</code>方法介绍。
       </td>
    </tr>
    <tr>
        <td><code>animationEasingFamily<code></td>
        <td><code></code></td>
        <td>
            查看下面的<code>setAnimationEasing()</code>方法介绍。
       </td>
    </tr>
    <tr>
        <td><code>animationEasingTyp<code></td>
        <td><code></code></td>
        <td>
            查看下面的<code>setAnimationEasing()</code>方法介绍。
       </td>
    </tr>
</table>

##### `originAndDestinationFieldIds` 示例代码

（这也是图层源代码中的默认值。）

```javascript
// 这是默认值选项示例
// 开发人员很可能需要为这些选项对象提供其数据匹配值

originAndDestinationFieldIds: {
  originUniqueIdField: 'origin_id',
  originGeometry: {
    x: 'origin_lon',
    y: 'origin_lat'
  },
  destinationUniqueIdField: 'destination_id',
  destinationGeometry: {
    x: 'destination_lon',
    y: 'destination_lat'
  }
}
```

##### `style` 示例代码

（这也是图层源代码中的默认值。）

```javascript
style: function(geoJsonFeature) {
  // 使用 leaflet 路径样式选项

  // 因为 GeoJSON 特征元素属性有图层来修改，
  // 开发人员可以依靠"isOrigin"属性设置不同的符号来表达起止点CircleMarker标记的样式

  if (geoJsonFeature.properties.isOrigin) {
    return {
      renderer: canvasRenderer, // 推荐使用你自己的 L.canvas()
      radius: 5,
      weight: 1,
      color: 'rgb(195, 255, 62)',
      fillColor: 'rgba(195, 255, 62, 0.6)',
      fillOpacity: 0.6
    };
  } else {
    return {
      renderer: canvasRenderer,
      radius: 2.5,
      weight: 0.25,
      color: 'rgb(17, 142, 170)',
      fillColor: 'rgb(17, 142, 170)',
      fillOpacity: 0.7
    };
  }
}
```

### 方法概要

<table>
    <tr>
        <th>方法</th>
        <th>参数</th>
        <th>描述</th>
    </tr>
    <tr>
       <td><code>selectFeaturesForPathDisplay( selectionFeatures, selectionMode )</code></td>
       <td><code>selectionFeatures</code>:<code>Array</code> 由图层管理和展示的起点或终点特征元素数组；<br><code>selectionMode</code>:<code>String</code> 有效值为：<code>'SELECTION_NEW'/<code>、 <code>'SELECTION_ADD'</code> 或者 <code>'SELECTION_SUBTRACT'</code>。</td>
       <td>通知图层在地图上绘制相应的贝塞尔曲线。<br>举个例子，你可以在<code>click</code>和<code>mouseover</code>事件监听器中方便地使用它。</td>
    </tr>
    <tr>
       <td><code>selectFeaturesForPathDisplayById( uniqueOriginOrDestinationIdField, idValue, originBoolean, selectionMode )</code></td>
       <td></td>
       <td>如果唯一的起点或终点 ID 值已知，并且你不希望依赖于<code>click</code> 或 <code>mouseover</code>事件侦听器，这是一种方便的方法。</td>
    </tr>
    <tr>
       <td><code>clearAllPathSelections()</code></td>
       <td></td>
       <td>图层取消选择（并隐藏）所有贝塞尔曲线。</td>
    </tr>
    <tr>
       <td><code>playAnimation()</code></td>
       <td></td>
       <td>开始并显示贝塞尔曲线动画。</td>
    </tr>
    <tr>
       <td><code>stopAnimation()</code></td>
       <td></td>
       <td>停止并隐藏任何Bezier曲线动画</td>
    </tr>
    <tr>
       <td><code>setAnimationDuration( duration )</code></td>
       <td><code>douration</code>：<strong>可选</strong> 是以毫秒为单位的数字</td>
       <td>改变动画持续时间。</td>
    </tr>
    <tr>
       <td><code>setAnimationEasing( easingFamily, easingType )</code></td>
       <td><code>easingFamily</code>：<strong>字符串</strong> <br><code>easingType</code>：<strong>字符串</strong><br>有效值请看<code>getAnimationEasingOptions()</code>方法。</td>
       <td>通过<a href="https://github.com/tweenjs/tween.js">tweening.js 库</a> 改变动画的缓解功能</td>
    </tr>
    <tr>
       <td><code>getAnimationEasingOptions()</code></td>
       <td></td>
       <td>基于<a href="https://github.com/tweenjs/tween.js">tweening.js 库</a> 返回有效的<code>easingFamily</code>和<code>easingType</code>值。</td>
    </tr>
</table>

### 事件概要

| Event | Description |
| --- | --- |
| `click` | 继承自 [layer `click`](http://leafletjs.com/reference-1.0.3.html#interactive-layer-click) 并添加下面的属性到事件对象中：<br/><br/> `isOriginFeature`: `true` 如果起点已被点击，如果终点被点击为 `false` 。 <br/><br/> `sharedOriginFeatures`: `Array` 有共同起点的元素数组。 <br/><br/> `sharedDestinationFeatures`: `Array` 有共同终点的元素数组。 |
| `mouseover` | 继承自 [layer `mouseover`](http://leafletjs.com/reference-1.0.3.html#interactive-layer-mouseover) 并添加下面的属性到事件对象中：<br/><br/> `isOriginFeature`: `true` 如果鼠标经过起点， `false` 鼠标经过终点。 <br/><br/> `sharedOriginFeatures`: `Array` 有共同起点的元素数组。 <br/><br/> `sharedDestinationFeatures`: `Array` 有共同终点的元素数组。 |

## 许可
A copy of the license is available in the repository's [LICENSE](./LICENSE) file.
