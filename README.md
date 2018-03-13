# xing-weapp-component

> 小程序基础组件扩展

## xing组件简介

|组件名|目录|扩展组件|扩展内容|
|---|---|---|---|
|xing-scroll-view|/xing-components/x-scroll-view|[scroll-view](https://mp.weixin.qq.com/debug/wxadoc/dev/component/scroll-view.html)|增加scroll-view自身的下拉刷新功能等|
|xing-image|/xing-components/x-image|[image](https://mp.weixin.qq.com/debug/wxadoc/dev/component/image.html)|增加图片的placeholder占位图/broken破图|

## 使用方法

- 复制文件夹 `xing-components` 到项目中

- 在需要使用相关组件的页面的 `json` 文件中进行引用声明。此时需要提供每个自定义组件的标签名和对应的自定义组件文件路径：

```json
{
  "usingComponents": {
    "x-tag-name": "path/to/xing-components/x-tag-name"
  }
}
```

- 在页面的 `wxml` 中就可以像使用基础组件一样使用自定义组件。节点名即自定义组件的标签名，节点属性即传递给组件的属性值。

```xml
<view>
  <x-tag-name attr-name="attr-value"></x-tag-name>
</view>
```

- [自定义组件官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/framework/custom-component/)

## 组件文档

### xing-scroll-view

|属性名|类型|默认值|说明|
|---|---|---|---|
|pull-down-height|Number|60|下拉到多少高度时可刷新，单位为 `px` |
|pull-text|String|下拉可以刷新|下拉还不能刷新时显示的文字|
|release-text|String|松开立即刷新|下拉至可刷新时显示的文字|
|loading-text|String|正在刷新数据中|刷新时显示的文字，尾部会自动接 `...` |
|finish-text|String|刷新完成|刷新完成时显示的文字|
|loadmore-text|String|正在加载更多数据|上拉加载更多时显示的文字|
|nomore-text|String|已经全部加载完毕|上拉加载无数据后显示的文字|
|refreshing|Boolean|false|是否正在刷新|
|nomore|Boolean|false|是否没有更多数据（加载完毕）|
|bindpulldownrefresh|HandleEvent||当下拉刷新时触发|
|bindloadmore|HandleEvent||当滚动到底部时触发|
|bindscroll|HandleEvent||同[scroll-view](https://mp.weixin.qq.com/debug/wxadoc/dev/component/scroll-view.html)|

**注意：**

1. 触发 `pulldownrefresh` 事件时，组件的 `refreshing` 属性会被设为 `true` ，刷新完成后将其设为 `false` 以完成刷新
2. 当没有更多数据时，将组件的 `nomore` 属性设为 `true`
3. 每页内容总高度应大于组件的高度，否则在底部加载时会出现问题，因此在当前页确定为最后一页时（如内容不足一页）就应该将 `nomore` 设为 `true`

**示例**

```xml
<view class="header">header</view>
<x-scroll-view refreshing="{{refreshing}}" nomore="{{nomore}}" bindpulldownrefresh="_onPullDownRefresh" bindloadmore="_onLoadmore" bindscroll="_onScroll">
  <block wx:for="{{colors}}" wx:for-index="index" wx:key="index">
    <view class="view" style="background: {{item}}">{{index + 1}}. {{item}}</view>
  </block>
</x-scroll-view>
```

```javascript
Page({

  data: {
    colors: [],
  },

  _randomColor: function () {
    return `rgba(${Math.floor(Math.random() * 256)},${Math.floor(Math.random() * 256)},${Math.floor(Math.random() * 256)},${(Math.random() * 0.3 + 0.2).toFixed(1)})`;
  },

  _generateColors: function (length) {
    return new Array(length).fill(null).map(() => this._randomColor());
  },

  //下拉刷新监听函数
  _onPullDownRefresh: function () {
    setTimeout(() => {
      const colors = this._generateColors(20);
      this.setData({
        colors,
        refreshing: false,
      });
    }, 2000);
  },

  //加载更多监听函数
  _onLoadmore: function () {
    setTimeout(() => {
      if (this.data.colors.length == 80) {
        this.setData({ nomore: true })
      } else {
        const colors = this._generateColors(20);
        this.setData({ colors: [...this.data.colors, ...colors] });
      }
    }, 1000);
  },

  _onScroll: function (e) {
    console.log(e);
  },

  onLoad: function (options) {
    const colors = this._generateColors(20);
    this.setData({ colors });
  },
})
```

### xing-image