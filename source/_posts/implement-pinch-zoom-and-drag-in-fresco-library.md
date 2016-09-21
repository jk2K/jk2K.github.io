title: 让 fresco 支持双指缩放与拖动
date: 2015-12-16 17:37:02
tags:
- android
---
## 需求
fresco 的 SimpleDraweeView 不支持缩放与拖动，想尽可能简单的实现这个功能

## 代码
{% gist 67502e1744e081dcfef5 ZoomableDraweeView.java %}

## 说明
在 [PinchToZoomScaleDetector.java](https://gist.github.com/nbarraille/eb5d0da20bc813969b08) 的基础上实现了拖动操作, 不是很难, 在这个过程中最大的收获就是了解了`ScaleGestureDetector`和`GestureDetector`该如何使用, 好的, 尽情使用吧


## 参考链接
* [Implementing pinch zoom and drag using Android&#039;s build in gesture listener and scale listener](http://stackoverflow.com/questions/19418878/implementing-pinch-zoom-and-drag-using-androids-build-in-gesture-listener-and-s/19545542#19545542)
* [ZoomableDraweeView-sample](https://github.com/kunny/ZoomableDraweeView-sample)
* [PinchToZoomScaleDetector.java](https://gist.github.com/nbarraille/eb5d0da20bc813969b08)
* [fresco docs](http://frescolib.org/)
