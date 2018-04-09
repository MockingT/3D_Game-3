# 游戏对象与图形基础
### 一. 操作与总结： ###  
- 参考 Fantasy Skybox FREE 构建自己的游戏场景  
在创建完Terrian对象之后，将下载好的Skyboxes文件导入到Assets当中：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d4.png)  
之后为整个场景添加背景（天空），在Main Camera中添加一个Skybox属性，选择想要的背景：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d2.png)  
Terrian的属性中可以涂刷地形等：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d3.png)  
之后粗略地建造了下地形，效果如下图：
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d1.png)  
   
- 总结游戏对象的使用：  
添加背景可以选择在主摄像机中添加  
地形一般需要创建Terrian对象，然后在上面修改效果  
在Assets Store中可以下载自己想要的资源  
树木等可以直接导入到Assets中（系统自带）  
### 二. 编程实践：牧师与魔鬼 动作分离版  ###  
对于ppt要求的理解是将动作抽象出来，所以本次文件目录如下：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d5.png)  
实现效果和上周相同：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d6.png)  
其中actionmanager.cs中包含了船的运动分解代码； Controlle.cs文件中删了枚举类型的使用，改用IQueryGameStatus接口来判断当前状态；，model.cs文件大致和上次类似。  
具体代码如下：  

