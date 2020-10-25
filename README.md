# imdb
![](README_files/1.png)
#### 介绍
ssm阶段的电影排行查看系统前端。

#### 软件架构
uni-app + DCloud组件库


#### 安装教程

1.  先将其导入HbuiderX中，并识别项目为uni-app。
2.  修改manifest.json里的appid（示例微信小程序）：
```json
/* 小程序特有相关 */
    "mp-weixin" : {
        "appid" : "",
        "setting" : {
            "urlCheck" : false
        },
        "usingComponents" : true,
        "permission" : {}
    }
```
3.  修改App.vue里的全局变量：
```js
globalData:{
	//后端业务请求路径基址
	baseUrl:'http://',
	//图片访问、存储路径
	imgUrl:'http://'
}
```
4.  安装Dcloud官方组件库

#### 使用说明

1.  在HbuilderX下点击运行——>运行到小程序或模拟器——>运行至微信模拟器。

