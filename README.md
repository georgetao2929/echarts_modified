# echarts_modified
modify funnel code

@[TOC](目录)

## Echarts漏斗图原效果

这是Echarts中实现的funnel图的效果：
![每一块都为梯形](https://img-blog.csdnimg.cn/20210525164842468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk1OTgwNw==,size_16,color_FFFFFF,t_70#pic_center)

可以看到，除了最后一个块是三角形，其他的块都是梯形。

## 目标效果
![每一个块都为矩形](https://img-blog.csdnimg.cn/20210525165307497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk1OTgwNw==,size_16,color_FFFFFF,t_70#pic_center)

我想要得到的效果是每一块都像这样是矩形的。

## 解决方案

 1. 首先想到是在官方文档里找答案。
 
 看看有没有相关的参数可以修改来完成这种效果。但是很可惜，只找到了一种方法让最下面的三角形也变成梯形，没能找到让梯形变成矩形的参数。
 关于让三角形变成梯形，只需要在funnel图的**series**里设置**minSize**参数就可以了，**默认为0%**，需要把它**设置成不为0%**。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210525170150395.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk1OTgwNw==,size_16,color_FFFFFF,t_70#pic_center)

 于是第一种方案宣告失败。
 
 2. 如此，只能通过修改源代码来实现了。
 
 找到echarts的目录，进入**lib-chart-funnel**，里面有四个.js文件，一个一个看看，最终确定要修改的东西应该是在**funnelLayout.js**中，锁定377开始的几行代码：
 

```javascript
var start = getLinePoints(idx, y);
var end = getLinePoints(nextIdx, y + height);
y += height + gap;
data.setItemLayout(idx, {
 points: start.concat(end.slice().reverse())
});
```
很明显，idx、y和nextIdx、y+height分别是两个点的横坐标、纵坐标，就是这两个点影响了梯形的形状。经过测试，只要更改**nextIdx为idx**就可以把梯形变成矩形。
修改后代码：

```javascript
var start = getLinePoints(idx, y);
var end = getLinePoints(idx, y + height);
y += height + gap;
data.setItemLayout(idx, {
 points: start.concat(end.slice().reverse())
});
```
**成功！**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210525172016768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk1OTgwNw==,size_16,color_FFFFFF,t_70#pic_center)



