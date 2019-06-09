---
title: " QtCharts模块在QtWideget中图表绘制(非QML)\t\t"
tags:
  - chart
  - qt
url: 724.html
id: 724
categories:
  - Qt
date: 2017-12-18 14:28:31
---

介绍
--

以前一直用QCustomPlot，现在Qt提供了QtCharts，看起来效果比，模块的帮助文档：[QtCharts](http://doc.qt.io/qt-5/qtcharts-index.html)，所有官方的范例：[Example](http://doc.qt.io/qt-5/qtcharts-examples.html)

*   以[QChartView](http://doc.qt.io/qt-5/qchartview.html)提供界面显示，继承自[QGraphicsView](http://doc.qt.io/qt-5/qgraphicsview.html)，setChart方法可以在一个view中添加一个chart
*   以[QChart](http://doc.qt.io/qt-5/qchart.html)作为图表，提供颜色风格，动画效果风格，坐标轴控制，图例显示位置，以及QtCharts提供的一系列图表类型元素的增删改，同时[QPolarChart](http://doc.qt.io/qt-5/qpolarchart.html)提供极坐标图
*   [QAbstractSeries](http://doc.qt.io/qt-5/qabstractseries.html)作为一系列图形类型的父类，以此接口实现了[QAbstractBarSeries](http://doc.qt.io/qt-5/qabstractbarseries.html)柱状图的接口, [QAreaSeries](http://doc.qt.io/qt-5/qareaseries.html)面积图, [QBoxPlotSeries](http://doc.qt.io/qt-5/qboxplotseries.html)箱形图, [QCandlestickSeries](http://doc.qt.io/qt-5/qcandlestickseries.html)(K线图), [QPieSeries](http://doc.qt.io/qt-5/qpieseries.html)饼图和 [QXYSeries](http://doc.qt.io/qt-5/qxyseries.html)(散点图/子类有线形图和曲线图)，均提供了append等函数用于添加每一项数据
*   QXXXXSet提供了复杂图形的每一项数据的添加比如[QBoxPlotSeries](http://doc.qt.io/qt-5/qboxplotseries.html)的append函数不能添加int等基础类型[QBoxSet](http://doc.qt.io/qt-5/qboxset.html)，范例：[BarChart Example](http://doc.qt.io/qt-5/qtcharts-barchart-example.html)
*   QXXXXAxis提供了一系列坐标轴类，可以使用QChart::[setAxisX](http://doc.qt.io/qt-5/qchart.html#setAxisX)/[setAxisY](http://doc.qt.io/qt-5/qchart.html#setAxisY)设置，当然也可以使用QChart::[createDefaultAxes](http://doc.qt.io/qt-5/qchart.html#createDefaultAxes)使用默认坐标轴类型，范例：[DateTimeAxis Example](http://doc.qt.io/qt-5/qtcharts-datetimeaxis-example.html)、[Logarithmic Axis Example](http://doc.qt.io/qt-5/qtcharts-logvalueaxis-example.html)
*   QLegend提供图例，范例：[Legend Example](http://doc.qt.io/qt-5/qtcharts-legend-example.html)
*   QLegendMarker图例标记 ，QLegend提供的是图例框，里面的每一项应该用QLegendMarker，范例：[LegendMarkers Example](http://doc.qt.io/qt-5/qtcharts-legendmarkers-example.html)
*   QXXXXMapper映射器，可以从[QAbstractItemModel](http://doc.qt.io/qt-5/qabstractitemmodel.html)中的数据映射到图表，范例：[BarModelMapper Example](http://doc.qt.io/qt-5/qtcharts-barmodelmapper-example.html)

QChart
------

提供了三个枚举类型

enum

**[AnimationOption](http://doc.qt.io/qt-5/qchart.html#AnimationOption-enum)** { NoAnimation, GridAxisAnimations, SeriesAnimations, AllAnimations }

enum

**[ChartTheme](http://doc.qt.io/qt-5/qchart.html#ChartTheme-enum)** { ChartThemeLight, ChartThemeBlueCerulean, ChartThemeDark, ChartThemeBrownSand, ..., ChartThemeQt }

enum

**[ChartType](http://doc.qt.io/qt-5/qchart.html#ChartType-enum)** { ChartTypeUndefined, ChartTypeCartesian, ChartTypePolar }

分别用于定义动画效果、背景风格、以及图标类型是极坐标还是笛卡尔，对于风格方面请见范例[Chart Themes Example](http://doc.qt.io/qt-5/qtcharts-chartthemes-example.html)

> Animation不会自己播放，需要在图标刷新时有用，一个是坐标轴和图标网格线一个是series，比如柱状图逐渐升起的过程，最简单的刷新方法：改窗口尺寸

除此以外通过接口可以操作title标题、axis坐标轴，并通过**[addSeries](http://doc.qt.io/qt-5/qchart.html#addSeries)**添加图

其他
--

*   建议使用Mapper映射到model中，通过修改model可以动态调整表格
*   QtCharts的new出的对象都会在add、set以后由上一级管理，不需要主动delete