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

粒子。它们非常受欢迎，可用于实现各种效果，如星星、烟、雨、灰尘、火和许多其他东西。
粒子的好处是您可以在屏幕上以合理的帧速率显示数十万个粒子。缺点是每个粒子都由一个始终面向相机的平面（两个三角形）组成。
创建粒子就像制作网格一样简单。我们需要一个BufferGeometry，一种可以处理粒子的材质 ( PointsMaterial )，而不是生成一个Mesh，我们需要创建一个Points。

### BufferGeometry

是面片、线或点几何体的有效表述。包括顶点位置，面片索引、法相量、颜色值、UV 坐标和自定义缓存属性值。使用 BufferGeometry 可以有效减少向 GPU 传输上述数据所需的开销。

BufferGeometry是一个没有任何形状的空几何体，我们可以通过BufferGeometry自定义任何几何形状，具体一点说就是**定义顶点数据**。

自定义buffergeometry几何体

```
particlesGeometry = new THREE.BufferGeometry();
```

通过JS类型化数组**Float32Array**创建一组xyz坐标数据用来表示几何体的顶点坐标和顶点颜色。

```
const positions = new Float32Array(count * 3);
const colors = new Float32Array(count * 3);
```

通过一定算法，循环向数组注入数据，表示坐标和颜色，每三个数据作为一个坐标或rgb，count是粒子数量

通过three.js的属性缓冲区对象 **BufferAttribute** 表示[threejs](https://so.csdn.net/so/search?q=threejs&spm=1001.2101.3001.7020)几何体顶点数据。

```
geometry.setAttribute("position",new THREE.BufferAttribute(positions, 3) );
geometry.setAttribute("color", new THREE.BufferAttribute(colors, 3));
```

再通过new THREE.PointsMaterial设置粒子材质

最后通过new THREE.Points(geometry, material); 创建粒子

## 光线投射

***光线投射([RayCaster](https://threejs.org/docs/index.html?q=Ra#api/zh/core/Raycaster))可以向特定方向投射光线，并测试哪些对象与其相交。光线投射用于进行鼠标拾取（在三维空间中计算出鼠标移过了什么物体）。***

用法示例：

1. 测试相机前方是否有一堵墙（障碍）
2. 光线是否击中目标
3. 当鼠标移动时测试是否有物体位于光标下方，以此模拟鼠标事件
4. 当物体朝向特定某处时提示信息

**创建光线投射**

```
const raycaster = new THREE.Raycaster()
```

```
Raycaster( origin : Vector3, direction : Vector3, near : Float, far : Float )
origin —— 光线投射的原点向量。
direction —— 向射线提供方向的方向向量，应当被标准化（单位向量化.normalize()）。
near —— 返回的所有结果比near远。near不能为负值，其默认值为0。
far —— 返回的所有结果都比far近。far不能小于near，其默认值为Infinity（正无穷）
```

**设置射线原点和方向向量**

```
// 射线原点
const rayOrigin = new THREE.Vector3(-3,0,0)
//射线方向
const rayDirection = new THREE.Vector3(10,0,0)
// 必须将射线方向三维向量转化为单位向量
// 也就是说，将该向量的方向设置为和原向量相同，但是其长度（length）为1
rayDirection.normalize()
raycaster.set(rayOrigin,rayDirection)
```

**投射射线**

使用Raycaster的`intersectObject()`方法来测试一个对象，`intersectObjects()`方法测试一组对象

```
const intersect = raycaster.intersectObject(object2);
const intersects = raycaster.intersectObjects([object1,object2,object3,]);
```

始终使用数组，即便你只是在测试一个对象，因为光线可以多次穿过同一对象

查看打印结果对象包含了什么信息
`distance` – 光线原点与碰撞点之间的距离
`face` – 几何体的哪个面被光线击中
`faceIndex` – 那被击中的面的索引
`object` – 什么物体与碰撞有关
`point` – 碰撞准确位置的矢量
`uv` – 该几何体中的UV坐标

### setFromCamera方法

这个方法可以通过相机来设置origin,direction两个值，来设置射线的起点和方向

```
setFromCamera: function ( coords, camera )
//coords: 鼠标的位置，是一个归一化的设备坐标，必须在-1到1之间
//camera：光线起源的位置
```

### 归一化坐标

- 归一化坐标优点：我们摄像机使用的是三维世界的世界坐标，通过归一化坐标比较好转化。
- 假设我们三维世界中归一化坐标在原点，那我们屏幕坐标在左上角，所以需要通过转化函数，把屏幕坐标转化为归一化坐标

简单来说就是原来的屏幕坐标原点为屏幕左上角，要转化为三维世界的原点位置，且坐标范围在[-1,1]

```
const sizes = {width: window.innerWidth,height: window.innerHeight,};
mouse = new THREE.Vector2();
window.addEventListener("mousemove", (_event) => {
   mouse.x = (_event.clientX / sizes.width) * 2 - 1;
   mouse.y = -(_event.clientY / sizes.height) * 2 + 1;
});
```

_event.client为鼠标当前位置，_event.clientX / sizes.width是占屏幕的百分比，*2-1可以使得坐标范围在[-1,1]

再通过**setFromCamera**方法来设置射线位置

```
raycaster.setFromCamera(mouse, camera);
```

## 中国地图

### 获取地图geoJson数据

对于地图的获取我们可以通过[阿里云可视化平台](https://link.juejin.cn/?target=http%3A%2F%2Fdatav.aliyun.com%2Fportal%2Fschool%2Fatlas%2Farea_selector%23%26lat%3D31.769817845138945%26lng%3D104.29901249999999%26zoom%3D4)获取,这里我们自己封装一个方法网络请求获取

```
const getGeoJson = async (adcode, isFull = true) => {
  const response = await fetch(
    `https://geo.datav.aliyun.com/areas_v3/bound/geojson?code=${adcode}${
      isFull ? "_full" : ""
    }`,
    {
      method: "GET",
    }
  );

  return response.json();
};
```

### 数据处理步骤

1.获取所绘制地图的最大和最小经纬度信息（地图的四边）

2.根据地图的四边计算地图的中心点坐标，目的是方便后期经纬度转墨卡托使用，以及将整个地图移动到场景中心

>这里解释下为什么要将地图移动到场景中心：我们绘制的地图的经纬度信息可能不是围绕场景中心的，这就导致地图可能距离中心很遥远，当我们相机设置的位置不是很合理的情况下，我们可能会发现绘制完成后根本看不到绘制的内容，这并不是我们想要的结果。

3.处理经纬度信息，将经纬度坐标转化为[墨卡托投影](https://link.juejin.cn?target=)坐标。众所周知，地球是一个球体，每一个经纬度坐标并不在同一平面上，而我们在绘制`3d`地图的时候是将他们放在同一个平面上的，因此`geoJson`得到的经纬度信息我们无法直接使用，为此我们需要使用墨卡托投影对经纬度信息进行处理，使得他们能够在同一平面展示

### 步骤的代码实现

1.定义一个变量`mapSideInfo`用于存储所绘制地图的最大、最小经纬度信息；`centerPos`用于存储中心点坐标信息

```
// 用于计算整个图形的中心位置
const mapSideInfo = {
  minlon: Infinity,
  maxlon: -Infinity,
  minlat: Infinity,
  maxlat: -Infinity,
};

// 中心坐标，用于到时候将图形绘制到坐标系原点计算使用
let centerPos = {};
```

## Shader

### Vertex Shader

片元着色器

````javascript
const vertex = `
  void main() {
    // gl_Position = projectionMatrix * viewMatrix * modelMatrix * 	vec4(position, 1.0);
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
  }
`;
````



### Fragment Shader

顶点着色器



### 三种修饰符 attribute/uniform/varying

attribute修饰符表示  这个数据 在每个顶点上都不同

uniform修饰符表示  这个数据 在每个顶点上都相同

varying修饰符是 顶点着色器 传递给 片元着色器 的数据

![image-20240429144326210](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240429144326210.png)

```
      const vertex = `
		varying vec2 vUv;
		void main() {
  			vUv = uv;
  			gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
		}
		`;
      const fragment = `
      	varying vec2 vUv;
		void main() {
  			gl_FragColor = vec4(1.0, vUv.x, vUv.y, 1.0);
		}
		`;
```

![image-20240429151602500](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240429151602500.png)

rgba(红，绿，蓝，透明度)

vec4(1.0, vUv.x, vUv.y, 1.0);  1.0表示全为红，vUv.x表示x轴方向绿色从0->1,vUv.y表示y轴方向蓝色从0->1

GLSL内置函数**step(edge,x)**，如果 `x<edge` 返回0.0，如果 `x>edge` 返回1.0。**实现突变**

````
float color = step(0.5, vUv.x);
gl_FragColor = vec4(vec3(color), 1.0);
````

![image-20240429161838743](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240429161838743.png)

GLSL内置函数**fract( )**，fract函数的意思是取小数部分。使得数值在 0.0-1.0 里循环重复，比如1.1、2.1取小数后都变回0.1。**实现重复**

````
gl_FragColor = vec4(vec3(fract(vUv.x * 10.0)), 1.0);
````



![image-20240429161908477](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240429161908477.png)

GLSL内置函数**length()**，获取向量的长度。

````
float dist = length(vUv);
vec3 color = vec3(dist);
gl_FragColor = vec4(color, 1.0);
````

vUv看作圆心出发的向量，长度为半径时值为1，（1，1，1，1.0）为白色。

![image-20240429162211765](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240429162211765.png)

内置函数mix(x,y,a)为线性插值，结果为 `x*(1-a)+y*a`，浮点数 a 的范围是0.0到1.0，根据其数值大小对 x、y 进行插值。

````
vec3 color1 = vec3(1.0, 1.0, 0.0);
vec3 color2 = vec3(0.0, 1.0, 1.0); 
float mixer = vUv.x;
vec3 color = mix(color1, color2, mixer);
gl_FragColor = vec4(color, 1.0);
````

![image-20240508143732749](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508143732749.png)

color1为黄色，color2为青色，mixer为vUv.x  所以从黄色沿着x轴渐变为青色

````
  vec3 color1 = vec3(1.0, 1.0, 0.0);
  vec3 color2 = vec3(0.0, 1.0, 1.0); 
  float mixer = fract(vUv.x*4.0);
  vec3 color = mix(color1, color2, mixer);
  gl_FragColor = vec4(color, 1.0);
````

![image-20240508143940912](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508143940912.png)

对角渐变，mixer=（vUv.x+vUv.y）/2

![image-20240508144501394](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508144501394.png)

两个对角向中心渐变

````
float mixer1 = vUv.x + vUv.y;
float mixer2 = 2.0 - (vUv.x + vUv.y);
float mixer = mixer1 * mixer2;;
````



![image-20240508145734610](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508145734610.png)

限制最大值后再相乘

借助内置函数clamp(x,min,max),将x限制在min-max之间

```
float mixer1 = vUv.x + vUv.y;
mixer1 = clamp(mixer1, 0.0, 1.0);
float mixer2 = 2.0 - (vUv.x + vUv.y);
mixer2 = clamp(mixer2, 0.0, 1.0);
float mixer = mixer1 * mixer2;
```

![image-20240508150126103](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508150126103.png)

### 黑白棋盘格

将多行黑白突变与多行白黑突变相减再取绝对值

```
vec3 mask1 = vec3(step(0.5, fract(vUv.x * 3.0)));
vec3 mask2 = vec3(step(0.5, fract(vUv.y * 3.0)));
vec3 color = abs(mask1 - mask2);
gl_FragColor = vec4(color, 1.0);
```

![image-20240508151014780](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508151014780.png)

彩色棋盘格

```
  vec3 color1 = vec3(1.0, 0.0, 0.0);
  vec3 color2 = vec3(0.0, 0.0, 0.0);
  vec3 mask1 = vec3(step(0.5, fract(vUv.x * 3.0)));
  vec3 mask2 = vec3(step(0.5, fract(vUv.y * 3.0)));
  vec3 mixer = abs(mask1 - mask2);
  vec3 color = mix(color1, color2, mixer);
  gl_FragColor = vec4(color, 1.0);
```

![image-20240508151448425](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508151448425.png)

abs+圆环

使用distance()距离函数，相当于各点到(0.5,0.5)的距离。越近越黑

```
float strength = distance(vUv, vec2(0.5));
vec3 color = vec3(strength);
gl_FragColor = vec4(color, 1.0);
```

![image-20240508153325406](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508153325406.png)

此时将strength加一个值，表示黑圆向外扩大，减去一个值，代表黑圆向内缩小

如果strength减去一个值，再取绝对值，圆中心就会变白

![image-20240508153746319](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508153746319.png)

再使用step进行突变

```
float strength = abs(distance(vUv, vec2(0.5))-0.25);
vec3 color = step(0.01,vec3(strength));
gl_FragColor = vec4(color, 1.0);
```

![image-20240508153931508](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508153931508.png)

```
float strength = abs(distance(vUv, vec2(0.5))-0.25);
vec3 color = 1.0-step(0.01,vec3(strength));
gl_FragColor = vec4(color, 1.0);
```

![image-20240508154335653](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508154335653.png)

对角线渐变用abs再次实现

```
float mixer = 1.0-abs((vUv.x + vUv.y - 1.0));
vec3 color = vec3(mixer);
gl_FragColor = vec4(color, 1.0);
```

![image-20240508155515360](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508155515360.png)

### 通过顶点着色器改变球体的位置

接下来介绍的 noise 噪声函数（Perlin Noise、Simplex Noise 等）可能有些人还没听说过，但其实用起来很简单，而且效果更强大。一言以蔽之借助 noise 函数能使相邻的点（一维、二维、三维的点都行）产生相近的数值，而不是 random 随机函数那种每个位置的数值都和附近无关的效果。

引入现成的谷歌glsl noise function

直接添加到main()之前.将球体的定位位置position通过噪声函数处理后，为了使球体的变换都按照原来的方向变化，乘以他的方向单位向量normal。将normal值传递到片元着色器，改变颜色

```
float cnoise(vec3 P){
  // ...
}

void main() {
  vec3 newPos = position;
  newPos += normal * cnoise(position);
  gl_Position = projectionMatrix * modelViewMatrix * vec4(newPos, 1.0);
  vNormal = normal;
}
```

```
varying vec3 vNormal;
void main() {
  gl_FragColor = vec4(vNormal, 1.0);
}
```

![image-20240508163959295](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240508163959295.png)

上面是使用cnoise函数，下面使用random函数来看效果

```
// 3D Randomness
float random(vec3 pos){
  return fract(sin(dot(pos, vec3(64.25375463, 23.27536534, 86.29678483))) * 59482.7542);
}

void main() {
  vec3 newPos = position;
  // newPos += normal * cnoise(position);
  newPos += normal * random(position);
  gl_Position = projectionMatrix * modelViewMatrix * vec4(newPos, 1.0);
}
```





noise改变position相邻范围

```
  vec3 newPos = position;
  newPos += normal * cnoise(position * (sin(uTime) ) * 4.0);
  gl_Position = projectionMatrix * modelViewMatrix * vec4(newPos, 1.0);
  vNormal = normal;
```

<video src="C:\Users\ouyang\AppData\Local\Packages\Microsoft.ScreenSketch_8wekyb3d8bbwe\TempState\Recordings\20240509-0140-55.4253238.mp4"></video>

用noise数值mix颜色

通过noise函数获取到颜色值后，通过step函数将数值变为0.0或者1.0

```
varying float vNoise;
void main() {
  vec3 newPos = position;
  float noise = cnoise(position * (sin(uTime)+1.0 ) * 4.0);
  noise = step(0.0, noise);
  newPos += normal * noise * 0.0;
  vNoise = noise;
  gl_Position = projectionMatrix * modelViewMatrix * vec4(newPos, 1.0);
}
```

```
varying float vNoise;
void main() {
  vec3 color1 = rgb(255.0, 0.0, 0.0);
  vec3 color2 = rgb(110., 161., 212.);
  vec3 color = mix(color1, color2, vNoise);
  gl_FragColor = vec4(color, 1.0);
}
```

![image-20240509102041125](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509102041125.png)

再把noise偏移顶点加回去,并且使用HSL颜色。HSL 即色相 Hue、饱和度 Saturation、亮度 Lightness 的简称。

将hsl颜色转化为rgb颜色      glsl hsl   或者glsl hs12rgb  函数

```
void main() {
  vec3 newPos = position;
  float noise = cnoise(position * (sin(uTime) +3.0) * 4.0);
  newPos += normal * noise;
  vNoise = noise;
  gl_Position = projectionMatrix * modelViewMatrix * vec4(newPos, 1.0);
}
```

```
void main() {
  vec3 color = hsl2rgb((0.1 + vNoise * 0.1), 1.0, 0.5);
  gl_FragColor = vec4(color, 1.0);
}
```

![image-20240509102003430](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509102003430.png)

### shader拧麻花

先创造一个长方体，再通过fract和step设置条纹

```
vec3 color1 = vec3(0.847, 0.204, 0.373);
vec3 color2 = vec3(1.0);
float mixer = step(0.5, fract(vUv.y * 3.0));
```

![image-20240509150226313](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509150226313.png)

旋转顶点

使用glsl rotate，让每个顶点都绕x轴旋转，即 axis 用 (1.0, 0.0, 0.0) 表示。

再使旋转的角度随着X的值变化

为了使旋转更加丝滑，需要增加长方体的细分数

```
const geometry = new THREE.BoxGeometry(3, 1, 1, 64, 64, 64);
```

```
uniform float uTime;
varying vec2 vUv;
const float PI = 3.1415925;
// vec3 rotate(...){ ... }
void main() {
  vUv = uv;
  vec3 axis = vec3(1.0, 0.0, 0.0);
  float angle = position.x;
  vec3 newPos = rotate(position, axis, angle);
  gl_Position = projectionMatrix * modelViewMatrix * vec4(newPos, 1.0);
}
```

![image-20240509150610189](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509150610189.png)

给angle加上时间

```
float angle = position.x*sin(uTime);
```

<video src="C:\Users\ouyang\AppData\Local\Packages\Microsoft.ScreenSketch_8wekyb3d8bbwe\TempState\Recordings\20240509-0747-01.4994296.mp4"></video>

### 最简单的粒子系统

想在 Three.js 里实现粒子系统，最简单的就是用现成的几何体如 SphereGeometry 搭配 `PointsMaterial` 材质，再丢给 `Points` 来替代 Mesh，即可在几何体顶点处放置粒子，默认粒子为方形。其中在 PointsMaterial 里可以统一设置粒子的颜色和大小。

```
const geometry = new THREE.SphereGeometry(10);
const material = new THREE.PointsMaterial({
  size: 0.4,
  color: 0xffffff,
});

const points = new THREE.Points(geometry, material);
scene.add(points);
```



![image-20240509160005080](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509160005080.png)

不过使用 SphereGeometry 有个很大的问题，粒子在球体两极密集、中间分散，空间上分布不均匀。

一种解决办法是用 `IcosahedronGeometry` 正二十面体，传入半径和细分数两个参数，细分数越大顶点越多，此时粒子分布很均匀。

```
const geometry = new THREE.IcosahedronGeometry(10, 6);
```

![image-20240509160114551](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509160114551.png)

材质换成 ShaderMaterial

为了更灵活的控制粒子效果，可以把材质换成 ShaderMaterial，和此前系列文章里的 shader 不同之处在于这里可通过 `gl_PointSize` 另外设置粒子大小，如果用一个固定数值的话粒子都一样大。

想要使靠近相机的粒子大、远离相机的粒子小，就需要对 mvPosition.z 值取倒数。经过 modelViewMatrix 后相机在原点处，3D物体顶点都在 z 轴负方向上，所以这里要加个负号，近大远小取倒数，再通过前面的数值调整大小即可。

```C#
gl_PointSize = 100.0 / -mvPosition.z;
```

![image-20240509161922088](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509161922088.png)

粒子系统看起来像由许多小 plane 组成，如果每个粒子有自己单独的 uv 坐标事情就好办了。

先直接用 uv 作为颜色看看，此时 uv 还是几何体上面的坐标而不是每个粒子单独的。

![image-20240509162041529](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509162041529.png)

幸运的是粒子系统里 `gl_PointCoord` 就是每个粒子上的(0,0)到(1,1)坐标，直接拿来替代 uv 就行，此时每个粒子上都是熟悉的青绿色。

```C#
gl_FragColor = vec4(gl_PointCoord, 0.0, 1.0);
```

![image-20240509162116218](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509162116218.png)

对 gl_PointCoord 减去0.5将坐标范围变化到(-0.5,-0.5)到(0.5,0.5)进行居中，接着通过 length 计算离粒子中心的距离，再通过 step 使得距离小于0.5半径的值为1.0，大于0.5的为0.0，然后作为颜色即可绘制出圆形，但此时粒子半径之外是黑色的而不是透明的，可以通过 discard 丢弃、不绘制对应片元/像素。

![image-20240509164110602](C:\Users\ouyang\AppData\Roaming\Typora\typora-user-images\image-20240509164110602.png)

除了用 Three.js 现成的几何体外，我们还能通过 `BufferGeometry` 来自定义几何体的 position 顶点坐标，这样想在哪放粒子就能在哪放。

在半径0-10、角度0-2xPI范围内随机出一个个顶点的 xy 坐标，将 z 统一设成0，依次放到数组里，再用 geometry.setAttribute 设置到顶点属性上，命名为 position，且通过 Float32BufferAttribute 表示该数组数据是三个为一组，组成 vec3，这样在顶点着色器里用 `attribute vec3 position` 就能声明和使用，只不过 ShaderMaterial 里 position 默认已经声明，所以直接用就行。

