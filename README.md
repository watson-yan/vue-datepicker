# vue-datepicker - 基于Vue2.x日期选择组件
Datepicker是一款稍微复杂的组件，其中涉及的日历计算与显示需要比较清晰的逻辑。

#### 预览图
<img src="https://github.com/watson-yan/vue-datepicker/blob/master/dist/imgs/1.png" width="400" height="400"/>

<img src="https://github.com/watson-yan/vue-datepicker/blob/master/dist/imgs/2.png" width="400" height="400"/>

#### How to start
* 下载依赖项/install dependencies
> npm install

* 运行服务/run server
> npm run test

* 程序默认运行在3000端口: localhost:3000

#### Description
在全局注册或者局部注册完成后使用组件:

``` javascript
<datepicker v-on:picked="picked"></datepicker>
```

（该组件源码为components文件夹下面的datepicker.vue文件， 其余文件是搭建了一个简易的express框架）

* Props:

    moment - 传入的时间(距 1970 年 1 月 1 日之间的毫秒数)，默认为现在时间

* 触发选取时间更新的条件:
    
    1. 选取日期: 在日历界面点击日期

    2. 选取年份和月份: 仅在可滑动的年份和月份列表点击


#### License
MIT