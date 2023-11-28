# Three-learn

## three.js是什么？

官网：Javascript 3D library（JavaScript 3D 库）。Three.js是基于webGL的封装的一个易于使用且轻量级的3D库，**Three.js**对WebGL提供的接口进行了非常好的封装，简化了很多细节，大大降低了学习成本，极大地提高了性能，功能也非常强大，用户不需要详细地学习 **WebGL**，就能轻松创作出三维图形，是前端开发者研发3D绘图的主要工具。微信小游戏跳一跳也是在基于Three.js研发的，Threejs现在是独领风骚。

## 三大要素

### 1.场景scene

场景是一个三维空间，是存放所有物品的容器，可以把场景想象成一个空房间，房间里面可以放置要呈现的物体、相机、光源等。

创建场景：要构件一个场景很简单，只需要new一个场景对象出来即可：`var scene = new THREE.Scene()`

### 2.相机camera

在场景中需要添加一个相机，相机用来确定观察位置、方向、角度，相机看到的内容，就是我们最终在屏幕上看到的内容。在程序运行过程中，可以调整相机的位置、方向、角度。

在Three.js中有两种常用的相机：**透视投影相机（perspectiveCamera）和正交投影相机（OrthographicCamera ）**

#### **透视投影相机（perspectiveCamera）**

**特点**透视相机的效果是模拟人眼看到的效果，跟人眼看到的世界是一样的，近大远小。

**用途**大部分场景都适合使用透视投影相机，因为跟真实世界的观测效果一样；

```
var camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
//fov 视野：表示摄像机能看到的视野。推荐默认值50
//aspect 	指定渲染结果水平方向和竖直方向长度的比值，推荐默认值为窗口的宽高比
//near 	近端渲染距离：指定从距离摄像机多近的位置开始渲染
//far 远端距离：指定摄像机从它所在的位置最远能看到多远，
```

#### **正交投影相机（OrthographicCamera ）**

**特点**正交投影则远近都是一样的大小，三维空间中平行的线，投影到二维空间也一定是平行的。

**用途**一般是用在制图、建模等方面，方便观察模型之间的大小比例。

```
var camera = new THREE.OrthographicCamera( left,right, top,bottom, near, far )
//left	可被渲染空间的左端面
//right	可被渲染空间的右端面
//top	可被渲染空间的上端面
//bottom	可被渲染空间的下端面
//near	基于相机所在位置，可被渲染空间的近端面
//far	基于相机所在位置，可被渲染空间的远端面
```

### 3.渲染器（renderer）

渲染器的作用就是将相机拍摄出的画面在浏览器中呈现出来。渲染器决定了渲染的结果应该画在页面的什么元素上面，并且以怎样的方式来绘制。

Three.js中有很多种类的渲染器，例如webGLRenderer、canvasRenderer、SVGRenderer，通常使用的是WebGLRenderer渲染器。

创建WebGLRenderer渲染器：`var renderer = new THERR.WebGLRenderer();`

创建完渲染器后，需要调用render方法将之前创建好的场景和相机相结合从而渲染出来，即调用渲染器的render方法：`renderer.render(scene,camera)`

## 纹理textures

### 是什么纹理？

纹理就是覆盖几何体表面的图像。不同的纹理类型具有不同的效果。

### 纹理加载器TextureLoader

```
// 初始化一个纹理加载器，然后用.load()加载纹理贴图
const textureLoader = new THREE.TextureLoader()
const colorTexture = textureLoader.load('xxx.jpg')
// 之后使用纹理来创建材质
const material = new THREE.MeshBasicMaterial({ map:colorTexture  })
```

在load方法里，图片路径后面可以跟三个函数，分别代表加载完成时，加载进行时，加载失败时

```
const colorTexture = textureLoader.load(
    'xxx.jpg',
    ()=>{  //加载完成时将调用
         console.log('load');
     },
    ()=>{   //加载过程中进行调用(一般不会用到)
         console.log('progress');
     },
    ()=>{  //加载出错时调用
         console.log('error');
     }
)
```

### 加载管理器[LoadingManager](https://threejs.org/docs/index.html?q=loa#api/zh/loaders/managers/LoadingManager)

刚才我们只用了一种纹理加载器加载了一个纹理，但如果后面我们再加载一些其他的纹理比如手机纹理、墙面纹理等等许多纹理，这时可能你想要知道所有这些纹理的全局加载进度或者当全部纹理加载完成时有消息提示，因此可以使用LoadingManager使这些加载事件相互关联

```
//初始化加载管理器
const loadingManager = new THREE.LoadingManager()
loadingManager.onStart = () => {
    console.log('Started loading file');
}
loadingManager.onProgress = () =>{
    console.log('onProgress');
}
loadingManager.onLoad = () => {
    console.log('Loading complete!');
}
loadingManager.onError = () =>{
    console.log('onError');
}
// 使用加载管理器loadingManager来跟踪 TextureLoader 的加载进度流程
const textureLoader = new THREE.TextureLoader(loadingManager)
//用.load()加载纹理贴图
const colorTexture = textureLoader.load('/textures/door/color.jpg')
const checkerboard1024Texture = textureLoader.load('/textures/checkerboard-1024x1024.png')
const checkerboard8Texture = textureLoader.load('/textures/checkerboard-8x8.png')
const minecraftTexture = textureLoader.load('/textures/minecraft.png')
```

观察控制台输出情况，说明共有四个纹理贴图被加载

![image-20231128100123379](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20231128100123379.png)

### 纹理转换

```
// 纹理在表面上重复多少次
// repeat属性只是一个Vector2二维向量
colorTexture.repeat.x = 2
colorTexture.repeat.y = 3
```

可以看到贴图横向变为原先的二分之一，纵向变为原先的三分之一，但并未有重复

```
// 如果每个方向上的repeat属性都设置大于1
// 则相应的wrap参数也应设置包裹模式为THREE.RepeatWrapping或THREE.MirroredRepeatWrapping以实现所需的平铺影响
colorTexture.wrapS = THREE.RepeatWrapping
colorTexture.wrapT = THREE.RepeatWrapping
```

### 过滤和Mip映射

mip映射(mipmapping)是一种技术，它包括一次又一次地创建半个较小版本的纹理，直到得到1x1纹理。
所有这些纹理变化都会发送到GPU，GPU将自动选择最合适的纹理版本。所有的这些都已经由THREE.js和GPU处理，但是我们可以选择不同的过滤算法

#### 缩小滤镜（Minification Filters）

当纹理的像素小于渲染的像素时触发该滤镜，如下图。原本纹理贴图像素如上面的图片一样大，当但我们把相机往后移动，可以看到贴图被缩小挤压到一小块渲染区域里面，这时就会触发缩小滤镜，换句话说就是原纹理贴图对于几何体表面太大了，因此此时用的纹理贴图可能就是较小版本的纹理如64x64，32x32。
我们可以通过为纹理的`minFilter`属性设置下面任意一个值来更改纹理的缩小滤镜，这里我们选择NearestFilter

```
// NearestFilter返回与指定纹理坐标最接近的纹理元素的值
colorTexture.minFilter = THREE.NearestFilter
```

当纹理的`minFilter`属性设置为NearestFilter后，像素点更加锐利以及会闪烁

#### 放大滤镜（Magnification Filters）

与缩小滤镜相反。当纹理的像素大于渲染的像素时触发该滤镜。



## 灯光Lights

### 环境光[AmbientLight](https://threejs.org/docs/index.html?q=light#api/zh/lights/AmbientLight)

环境光会均匀的照亮场景中的所有物体。

### 平行光[DirectionalLight](https://threejs.org/docs/index.html?q=light#api/zh/lights/DirectionalLight)

平行光是沿着特定方向发射的光。这种光的表现像是无限远,从它发出的光线都是平行的。常常用平行光来模拟太阳光的效果。

### 半球光[HemisphereLight](https://threejs.org/docs/index.html?q=light#api/zh/lights/HemisphereLight)

光源直接放置于场景之上，光照颜色从天空光线颜色渐变到地面光线颜色。
***半球光不能投射阴影。***

```
HemisphereLight( skyColor : Integer, groundColor : Integer, intensity : Float )
skyColor - (可选参数) 天空中发出光线的颜色。 缺省值 0xffffff。
groundColor - (可选参数) 地面发出光线的颜色。 缺省值 0xffffff。
intensity - (可选参数) 光照强度。 缺省值 1。
```

### 点光源[PointLight](https://threejs.org/docs/index.html?q=light#api/zh/lights/PointLight)

从一个点向各个方向发射的光源。一个常见的例子是模拟一个灯泡发出的光。

```
PointLight( color : Integer, intensity : Float, distance : Number, decay : Float )
color - (可选参数)) 十六进制光照颜色。 缺省值 0xffffff (白色)。
intensity - (可选参数) 光照强度。 缺省值 1。
distance - 这个距离表示从光源到光照强度为0的位置。 当设置为0时，光永远不会消失(距离无穷大)。缺省值 0.
decay - 沿着光照距离的衰退量。缺省值 1。 在 physically correct 模式中，decay = 2。
```

### 平面光光源[RectAreaLight](https://threejs.org/docs/index.html?q=light#api/zh/lights/RectAreaLight)

平面光光源从一个矩形平面上均匀地发射光线。这种光源可以用来模拟像明亮的窗户或者条状灯光光源。

### 聚光灯[SpotLight](https://threejs.org/docs/index.html?q=light#api/zh/lights/SpotLight)

光线从一个点沿一个方向射出，就像手电筒，随着光线照射的变远，光线圆锥体的尺寸也逐渐增大。

```
SpotLight( color : Integer, intensity : Float, distance : Float, angle : Radians, penumbra : Float, decay : Float )
color - (可选参数) 十六进制光照颜色。 缺省值 0xffffff (白色)。
intensity - (可选参数) 光照强度。 缺省值 1。
distance - 从光源发出光的最大距离，其强度根据光源的距离线性衰减。
angle - 光线散射角度，最大为Math.PI/2。
penumbra - 聚光锥的半影衰减百分比。在0和1之间的值。默认为0(光影边缘很锐利)。
decay - 沿着光照距离的衰减量。
```

### 性能消耗问题

灯光可以造成许多性能消耗，因此需尽可能少的添加光源或使用轻量级光源。
消耗最小的：环境光AmbientLight和半球光HemisphereLight
消耗适中的：平行光DirectionalLight和点光源PointLight
消耗最高的：聚光灯SpotLight和平面光光源RectAreaLight

 

## 阴影Shadows

### 阴影是怎么工作的？

当你进行一次渲染时，Three.js将对每个支持阴影的光线进行渲染，那些渲染会像摄像机那样模拟光线所看到的内容，而在这些灯光渲染下，网格材质将被深度网格材质MeshDepthMaterial所替代。
灯光渲染将像纹理一样被存储起来，称为阴影贴图，之后它们会被用于每个支持接收阴影的材质并投射到几何体上。

### 激活阴影

1.想要激活并使用阴影，就得先在渲染器`renderer`的`.shadowMap.enabled`属性中设置开启，允许在场景中使用阴影贴图

```
renderer.shadowMap.enabled = true
```

2.检查每个对象，确定它是否可以使用`castshadow`投射阴影，以及是否可以使用`receiveshadow`接收阴影。

```
几何体.castShadow = true
平面.receiveShadow = true
灯光.castShadow = true
```

***只有平行光、点光源和聚光灯支持阴影***

### 优化阴影贴图

#### 优化渲染尺寸

可以查看每个灯光阴影属性中的阴影贴图

```
console.log(directionalLight.shadow);
```

默认的贴图尺寸时512*512，可以修改为2的N次幂，数值越高越清晰

```
directionalLight.shadow.mapSize.width = 1024
directionalLight.shadow.mapSize.height = 1024
```

#### 近与远

上面说到Three.js使用灯光摄像机进行阴影贴图渲染。这些相机具有相同的属性，像near和far
为了方便调试，我们可以往场景中添加摄像机辅助对象（摄像机助手），要做的就是把平行光用于渲染阴影的灯光摄像机directionalLight.shadow.camera给添加到摄像机助手中

```
const directionalLightCameraHelper = new THREE.CameraHelper(directionalLight.shadow.camera)
scene.add(directionalLightCameraHelper)
```

#### 振幅amplitude

通过观察上图，使用相机助手后我们可以发现灯光相机所看到的区域还是太大了，溢出了不少。因为我们正在使用的是平行光，它使用的是正交相机OrthographicCamera。
所以我们可以通过正交相机的top，right，bottom，left四个属性来控制摄像机视锥体的哪一边可以看多远距离。



灯光相机的可视范围越小，阴影越精确，当然如果设置得实在太小，阴影将会被裁剪掉

#### 模糊

我们可以通过`radius`属性控制阴影模糊程度，它不会改变灯光相机与物体的距离。

```
directionalLight.shadow.radius = 10
```

## 粒子

