

































[TOC]

# 一、精华

  vertical-align用于设置一个元素的垂直对齐方式，但是它只针对于行内元素或者行内块元素有效。图片、表单都属于行内块元素，默认的 vertical-align 是基线对齐。bug：图片底侧会有一个空白缝隙，原因是行内块元素会和文字的基线对齐。
主要解决方法有两种：
1. 给图片添加 vertical-align:middle | top| bottom 等。 （提倡使用的）
2. 把图片转换为块级元素 display: block;



1、**在传统盒子模型下，设置内外边距需要确认元素是否有设置宽高，否则将不会撑大盒子**

2、**有定位的盒子，不能用margin：auto居中。同时，不能对其祖先元素用overflow：hidden，因为他会隐藏多余部分 。**

3、行内元素的上下内外边距都不起作用，只有左右内外边距有作用（给父盒子添加text-align：center）aQW3W

​      行内元素为了照顾兼容性，尽量只设置左右内外边距，不要设置上下内外边距。但是转换为块级和行内块元素就可以了

4、无序列表有内外边距

5、表单元素轮廓线（outline：none），文本域拖拽（resize:none），文本域标签代码如果不在一行，输入时焦点将会发生改变

**6、相对定位的盒子会压住标准流的盒子**

**7、行内元素、行内块元素之间有间隙**

8、浮动的盒子环绕文字，不会压住文字。定位的元素会压住文字（p,h）

9、溢出会影响下面的盒子，所以需要强制一行显示white-space：nowrap

**10、transform的移动及旋转都是相对于初始位置来说的，它不会影响其他的元素，并对行内元素不起作用。缩放放大盒子的同时也会放大阴影及使文字变模糊，解决办法就是放大宽高**

11、过渡和动画的本质都是为了使元素属性的变化，动态的显示出来，变化可见。不同详见下

12、如果想要立体感，就需要使用透视perspective。注意**transform-style: preserve-3d**应该什么时候使用

13、**3D空间中，元素距离我们眼睛的距离越来越短，视觉上看到的东西会放大（元素实际大小不变）。但如果把元素放在一个没有3D效果的盒子内，视觉上的效果将会消失**

**14、flex布局中不能用浮动，display**

**15、3D空间中，如果一个盒子不动，另一个盒子沿着X旋转90度，将看不见这个盒子**

​        2D如果有移动 或者其他样式，必须先写移动.3D中不一定，需要看需求

​        **注意：默认的旋转点除非设置，否则不会改变，旋转点一直在图片上。只是在3d的旋转中，轴方向会跟着旋转角度一起改变。所            以使用时必须注意是否先旋转后位移。如果移动是沿着原轴方向移动，则先移动，不然则反之**

​     父级盒子内的两个兄弟元素，可以利用旋转和位移形成立方体的中心点，这样父级盒子旋转点就是立方体的中心点

16、**[css overflow失效的原因](https://www.cnblogs.com/sogeisetsu/p/11663966.html)**对绝对定位的内容溢出隐藏，必须给最近的父级元素设置相对定位

（1）

块级元素：在没有设置宽高和内容的情况下，只存在元素结构，元素不会在界面显示（添加背景颜色，元素也没有显示在页面上）

行内元素：在没有内容的情况下，只存在元素结构，元素不会在界面显示（添加背景颜色，元素也没有显示在页面上）

span

a：需浮动。也可以理解为，a是行内元素，要给a设置宽高，就需要将a转换块级或行内块，而浮动元素具有行内块属性

行内块元素：img，input，（默认和文字的基线对齐，会出现图片在盒子内没有垂直居中，**通过给图片添加 vertical-align：middle 解决，或者display：block**）

span跟img一样，默认与文字基线对齐，不只是行内块的属性，行内元素也具有与基线对齐的属性，所以转换成块级元素可解决

**注意：行内块元素一般都要加上 vertical-align：middle**

继承：line，font开头的属性；当子集是块元素，如果不设置宽度，那么它的宽度就是父级的宽，高度如果不设置，那么高度就是它的子集中内容的高度。

（2）脱离标准流的盒子需要注意：脱离文档流的元素不设置宽度时，元素宽度由内容撑开，而不继承父亲宽度（流式布局中的固定定位需要设置最大最小宽度）；通过给父元素（float）或定位本身元素添加overflow：hidden清除浮动

# 二、定位

定位的盒子不能通过margin移动，应该通过left、right、top，buttom与transform：translateX（-50%）移动

固定定位：必须写在版心盒子的前面，否则出不来效果；不占位置要给宽度。relative属于文档流，文档流中元素不设置宽度时会继承父亲宽度。fixed和absolute都脱离了文档流，脱离文档流的元素不设置宽度时，元素宽度由内容撑开，而不继承父亲宽度

![image-20221103182546624](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221103182546624.png)



**在移动端布局固定定位一定要设置最小宽度和最大宽度**；

```
.search-wrap {
    position: fixed;
    overflow: hidden;
    width: 100%;
    height: 44px;
    min-width: 320px;
    max-width: 640px;
}
```

固定定位相对于整个屏幕，居中需要用left：50%。translate：transform（-50%）（如果屏幕宽度确定，不是百分比或者rem,VW,可以用margin-left：版心宽度一般）

**注意：定位的盒子不能用margin：0 auto居中**

# 三、HTML新特性

新特性都有兼容性问题，基本是 IE9+ 以上版本的浏览器才支持，如果不考虑兼容性问题，可以大量使用这
些新特性。

## 1、语义化标签

![image-20221122190734430](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122190734430.png)

## 2、多媒体标签

### 2.1 video

![image-20221122191157052](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191157052.png)

![image-20221122191236344](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191236344.png)

![image-20221122191257091](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191257091.png)

### 2.2 audio

![image-20221122191349539](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191349539.png)

![image-20221122191423359](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191423359.png)

![image-20221122191503097](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191503097.png)

### 2.3 HTML5 新增的 input 类型

![image-20221122191728162](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191728162.png)

![image-20221122191841092](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122191841092.png)

# 四、css3新特性

新特性都有兼容性问题，基本是 IE9+ 以上版本的浏览器才支持，如果不考虑兼容性问题，可以大量使用这
些新特性。

## 4.1 新增选择器

属性选择器、结构伪类选择器、伪元素选择器

## 4.2 属性选择器

![image-20221122192034102](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122192034102.png)

## 4.3 结构伪类选择器

![image-20221122192408917](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122192408917.png)



![image-20221122192436071](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122192436071.png)

## 4.4 伪元素标签选择器

![image-20221122192730929](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122192730929.png)

## 4.5 盒子模型

![image-20221122192904724](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122192904724.png)

## 4.6 过渡与动画

### 4.6.1 过渡

在不使用 Flash 动画或JavaScript 的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。过渡动画： 是从一个状态 渐渐的过渡到另外一个状态

**我们现在经常和 :hover 一起 搭配使用。**

![image-20221122193235850](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122193235850.png)

### 4.6.2 动画

### 4.7 浏览器私有前缀

# 五、seo优化

SEO（Search Engine Optimization）汉译为搜索引擎优化，是一种利用搜索引擎的规则提高网站在有关搜索引擎内自然排名的方式

页面必须有三个标签用来符合 SEO 优化

![image-20221125140527153](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125140527153.png)

## 1、三大标签SEO优化

（1）title 网站标题
       title 具有不可替代性，是我们内页的第一个重要标签，是搜索引擎了解网页的入口和对网页主题归属的最佳判断点。
       建议：网站名（产品名）- 网站的介绍 （尽量不要超过30个汉字）

```
    <title>品优购商城-综合网购首选-正品低价、品质保障、配送及时、轻松购物！ </title>
```

2. description 网站说明
   简要说明我们网站主要是做什么的。
   我们提倡，description 作为网站的总体业务和主题概括，多采用“我们是…”、“我们提供…”、“×××网
   作为…”、“电话：010…”之类语句。

```
<meta name="description" content="京东JD.COM-专业的综合网上购物商城,销售家电、数码通讯、电脑、家居百货、服装服饰、母婴、图书、食品等数万个品牌优质商品.便捷、诚信的服务，为您提供愉悦的网上购物体验!" />
```

3. keywords 关键字
   keywords 是页面关键词，是搜索引擎的关注点之一。
   keywords 最好限制为 6～8 个关键词，关键词之间用英文逗号隔开，采用 关键词1,关键词2 的形式。

```
<meta name= " keywords" content="网上购物,网上商城,手机,笔记本,电脑,MP3,CD,VCD,DV,相机,数码,配件,手表,存储卡,京东" />
```

## 2、LOGO SEO 优化

1. logo 里面首先放一个 h1 标签，目的是为了提权，告诉搜索引擎，这个地方很重要。
2. h1 里面再放一个链接，可以返回首页的，把 logo 的背景图片给链接即可。
3. 为了搜索引擎收录我们，我们链接里面要放文字（网站名称），但是文字不要显示出来。
    方法1：text-indent 移到盒子外面（text-indent: -9999px) ，然后 overflow:hidden ，淘宝的做法。
    方法2：直接给 font-size: 0; 就看不到文字了，京东的做法。
4. 最后给链接一个 title 属性，这样鼠标放到 logo 上就可以看到提示文字了。

```
  <div class="logo">
            <h1>
                <a href="index.html" title="品优购商城">品优购商城</a>
            </h1>
   </div>
```



# 六、过渡与动画

## 1、过渡

触发才会有变化，一般和伪类hover结合使用，盒子触发前是什么状态，触发后是什么状态，在加上transition过渡

应用:

（1）触发时出现遮罩层

触发前盒子display：none隐藏，触发后，盒子display：block显示，在加上过渡

（2）触发时从主盒子下方旋转出现另一个侧盒子

触发前侧盒子transform：rotate（180deg），旋转到主盒子的下方，并且overflow：hidden隐藏。触发后，侧盒子transform：rotate（0deg），在加上过渡

注意：谁过渡给谁加

## 2、动画

### 2.1 动画的基本使用

制作动画分为两步：

1.先定义动画

```
@keyframes 动画名称 {
   0%{
        width:100px;
   }  
   100%{
        width:200px;
   }
}

```

2.再使用（调用）动画

```
/* 动画名称 */
animation-name: move;
/* 持续时间 */
animation-duration: 2s;
```

### 2.2 动画序列

![image-20221125175023931](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125175023931.png)

1. 可以做多个状态的变化 keyframe 关键帧
2. **里面的百分比要是整数** 
3. 里面的百分比就是 总的时间的划 25% * 10  =  2.5s

### 2.3 常用属性

| **属性**                  | **描述**                                                     |
| ------------------------- | ------------------------------------------------------------ |
| @keyframes                | 规定动画。                                                   |
| animation                 | 所有动画属性的简写属性，除了animation-play-state属性。       |
| animation-name            | 规定@keyframes动画的名称。（必须的）                         |
| animation-duration        | 规定动画完成一个周期所花费的秒或毫秒，默认是0。（必须的）    |
| animation-timing-function | 规定动画的速度曲线，默认是“ease”。                           |
| animation-delay           | 规定动画何时开始，默认是0。                                  |
| animation-iteration-count | 规定动画被播放的次数，默认是1，还有infinite                  |
| animation-direction       | 规定动画是否在下一周期逆向播放，默认是“normal“,alternate逆播放 |
| animation-play-state      | 规定动画是否正在运行或暂停。默认是"running",还有"paused"。   |
| animation-fill-mode       | 规定动画结束后状态，保持forwards回到起始backwards            |

### 2.4 简写属性

![image-20221125181252114](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125181252114.png)

### 2.5 速度曲线细节

![image-20221125184839234](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125184839234.png)

应用：

（1）文字一个一个的显示

盒子宽度由0到1，文字溢出隐藏，速度曲线用step，走一步的宽度就是一个文字的大小，文字就会出现，从而达到目的

**注意：需要强制一行显示（white-space：nowrap），因为溢出会影响下面的盒子**

(2)奔跑的熊大

### 2.6 animation和transition异同

异：

（1）动画是自动变化，不需要触发

（2）动画可以多次变化，transition只能变化一次

同：

（1）过渡和动画的本质都是为了使元素属性的变化，动态的显示出来，变化可见

（2）transition变化的属性写在元素里面，而transition指定变化的属性。动画将变化的属性写入关键帧，再由动画将关键帧应用（引入属性变化）。它们之间的简写前四个都是一样的

# 七、2D转换

 二维坐标系

![image-20221125145508048](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125145508048.png)

## 1、2D转换之移动

**1.** **语法**

```
transform: translate(x,y); 或者分开写
transform: translateX(n);
transform: translateY(n);
```

**2.** **重点**

定义 2D 转换中的移动，沿着 X 和 Y 轴移动元素

**translate最大的优点：不会影响到其他元素的位置**

translate中的百分比单位是相对于自身元素的 translate:(50%,50%);

**每一次的移动都是相对初始位置来说的，不是根据上一次的移动**

**对行内标签没有效果**

## 2、2D转换之旋转rotate

**1.** **语法**

```
transform:rotate(度数)
```

**2.** **重点**

rotate里面跟度数， 单位是 deg 比如 rotate(45deg)

角度为正时，顺时针，负时，为逆时针

默认旋转的中心点是元素的中心点

**这里的度数参考系是盒子的原始位置 transform不改变盒子的原始位置**

## 3、 2D转换中心点 transform-origin

我们可以设置元素转换的中心点

**1.** **语法**

```
transform-origin: x y;
```

**2.** **重点**

注意后面的参数 x 和 y 用空格隔开

x y 默认转换的中心点是元素的中心点 (50% 50%)

还可以给x y 设置 像素 或者 方位名词 （top bottom left right center）

## 4、2D 转换之缩放scale

缩放，顾名思义，可以放大和缩小。 只要给元素添加上了这个属性就能控制它放大还是缩小。

**1.** **语法**

```
transform:scale(x,y);
```

**2.** **注意**

注意其中的x和y用逗号分隔

transform:scale(1,1) ：宽和高都放大一倍，相对于没有放大

transform:scale(2,2) ：宽和高都放大了2倍

transform:scale(2) ：只写一个参数，第二个参数则和第一个参数一样，相当于 scale(2,2)

transform:scale(0.5,0.5)：缩小

sacle缩放最大的优势：可以设置转换中心点缩放，默认以中心点缩放的，而且不影响其他盒子

**放大会让里面的字体变模糊，如果不想的话，就用宽高放大盒子**

   transform: scale(5);  我们不要用scale 因为他会让 阴影变大

# 八、3D转换

1、三维坐标系

![image-20221125192558161](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125192558161.png)

## 1、 3D移动 translate3d

![image-20221125193721046](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125193721046.png)

translform:translateZ(100px)：仅仅是在Z轴上移动。必须有透视，才能能看到translateZ 引起的变化了 

translateZ：近大远小

translateZ：往外是正值

translateZ：往里是负值 

## 2、**透视** **perspective**

![image-20221125193829864](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221125193829864.png)

**注意：透视是正值（值越大，离屏幕越远，看到的东西越小），而Z由里向外（由负到正，值越大，离眼睛越近，看到的东西越清楚）**

眼睛和物体之间的距离越小，看到的东西越大



## 3、translateZ和transform：scale区别

用salce是改变了原来大小的，translateZ是通过3d距离，感官变大，实际盒子没有变大

## **4、** **3D旋转 rotate3d** 

3D旋转指可以让元素在三维平面内沿着 x轴，y轴，z轴或者自定义轴进行旋转

transform:rotateX(45deg)：沿着x轴正方向旋转 45度

transform:rotateY(45deg) ：沿着y轴正方向旋转 45deg

transform:rotateZ(45deg) ：沿着Z轴正方向旋转 45deg

transform:rotate3d(x,y,z,deg)： 沿着自定义轴旋转 deg为角度（了解即可）

简写：xyz是表示旋转轴的矢量，是标示你是否希望沿着该轴旋转，最后一个标示旋转的角度。值为1时，表示希望沿着那个轴旋转

**注意：每次旋转之后，三轴旋转中心点不变，但三轴的方向变了**

先旋转，坐标轴也跟着转，在新坐标轴上移动距离；如果先移动则是在原来坐标轴上移动，再旋转出现新的坐标轴也不动了

## 5、如何确定值的正负

左手的手拇指指向 x，y,z（由里向外）轴的正方向

其余手指的弯曲方向就是该元素沿着x,y,z轴**正值**旋转的方向

## 6、3D呈现 transfrom-style

控制子元素是否开启三维立体环境。。

transform-style: flat 子元素不开启3d立体空间 默认的

transform-style: preserve-3d; 子元素开启立体空间

代码写给父级，但是影响的是子盒子

这个属性很重要，后面必用

注意：

（1）当perspective用在祖先元素上，要想控制子元素开启三维立体环境，必须给其亲父级元素添加transform-style: preserve-3d

（2）当perspective用在父级元素，则已经控制其子元素开启三位立体环境，不用给父级元素添加transform-style: preserve-3d

总结：看子元素是否使用3D和perspective所处位置，决定是否给父级元素添加transform-style: preserve-3d

**使用场景：**兄弟元素已通过旋转和位移确定了父盒子的内容，父盒子发生旋转和移动（transform-style: preserve-3d）

**父级盒子内有多个兄弟元素，并且兄弟元素具有三维的运动，同时父级也具有三维的运动，所以perspective添在爷级元素，这时必须给父级开启3D立体空间transform-style: preserve-3d才能让子元素起作用**

## 7、两面盒子翻转

结构：

```
  <body>
    <div class="try">
      <div class="father">
        <div class="front">黑马程序员</div>
        <div class="back">pink老师这里等你</div>
      </div>
    </div>
  </body>
```

**注意：要让父级盒子嵌套在一个盒子中，否则翻转时会出现抖动（变缩放变旋转）**

**因为沿着Y旋转时，元素距离我们眼睛的距离越来越短，视觉上看到的东西会放大（元素实际大小不变）。但如果把元素放在一个没有3D效果的盒子内，视觉上的效果将会消失**

样式：

（1）父级相对定位并且**开启3D空间transform-style: preserve-3d**，兄弟元素绝对定位，其中一个兄弟沿着Y旋转180deg，同时另一个兄弟沿着Z移动1px（也可以给两个兄弟元素添加 backface-visibility: hidden;）在3D空间形成不重叠的两面（如果不这样，而是用z-index，就会出现颜色不变的情况，因为z-index一直在干扰）

**祖先元素perspective**

（2）触发时，父级沿着Y旋转180deg。同时给父级添加**过渡**，使变化动态展示出来

# 九、其他技巧

## 1、盒子插入图片

1、div>img

![image-20221117174516360](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221117174516360.png)

使用场景：整个盒子除了一张图片，没有其他内容

```
img{
width：100%
//行内块元素，宽高等比例缩放，与父级盒子覆盖
}
```

2、div --- background



（1）![image-20221117174630398](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221117174630398.png)

（2）![image-20221117174928671](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221117174928671.png)

使用场景：（1）盒内有多个相似布局（2）盒内除了多张图片，没有其他内容

3、给元素添加before，after伪类，在通过background导入图片

使用场景：![image-20221117175008620](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221117175008620.png)

```
.search-btn {
    position: absolute;
    top: 0;
    left: 0;
    width: 40px;
    height: 44px;
}

.search-btn::before {
    content: "";
    display: block;
    width: 20px;
    height: 18px;
    background: url(../images/s-btn.png) no-repeat;
    background-size: 20px 18px;
    margin: 14px 0 0 15px;
}
```

**图片和文字一行：**

（1）盒子内容+盒子背景图片，通过background-position、text-intent、内外边距调整图片与文字的距离

（2）伪元素+定位，通过top等调整图片与文字的距离

**图片与文字两行：**

（1）伪元素+block，通过内外边距调整与文字的距离

## 2、伪元素在布局中的应用

<img src="C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110195622179.png" alt="image-20221110195622179" style="zoom:67%;" />

（1）伪元素与兄弟分行显示

![image-20221110195904165](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110195904165.png)

```
.user {
    width: 44px;
    height: 44px;
    /* background-color: purple; */
    font-size: 12px;
    text-align: center;
    color: #2eaae0;
}
//转换为块级元素才能设置宽高，父盒子的text-align：center只能调整文字元素的位置，调整伪元素的位置则通过外边距
.user::before {
    content: "";
    display: block;
    width: 23px;
    height: 23px;
    background: url(../images/sprite.png) no-repeat -59px -194px;
    background-size: 104px auto;
    margin: 4px auto -2px;
}

```

（2）伪元素与兄弟同一行显示

![image-20221110195644243](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110195644243.png)

```
.search {
    position: relative;
    height: 26px;
    line-height: 24px;
    border: 1px solid #ccc;
    flex: 1;
    font-size: 12px;
    color: #666;
    margin: 7px 10px;
    padding-left: 25px;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, .2);
}
//绝对定位不占有原位置，通过left等属性调整位置
.search::before {
    content: "";
    position: absolute;
    //也可以转换为行内块元素，但是需要使用vertical-align：middle
    top: 5px;
    left: 5px;
    width: 15px;
    height: 15px;
    background: url(../images/sprite.png) no-repeat -59px -279px;
    background-size: 104px auto;
}
```

## 3、为盒子添加小竖线

1、给盒子添加边框

2、添加after伪元素

![image-20221103171314217](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221103171314217.png)

## 4、padding不撑开盒子的情况

（1）css3盒子模型

（2）宽高是继承的，而不是自己设置的，只要写了，就会撑大盒子对应的宽高

## 5、外边距合并塌陷

上外边距合并出现的条件:  1.父元素的上边距与子元素的上边距之间没有border。 2.父元素的上边距与子元素的上边距之间没有非空内容。 3.父元素的上边距与子元素的上边距之间没有padding。

![image-20221123164434618](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221123164434618.png)

## 6、清除浮动

清除浮动之后，父级就会根据浮动的子盒子自动检测高度。父级有了高度，就不会影响下面的标准流了

![image-20221122185901668](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122185901668.png)

![image-20221122190032564](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122190032564.png)

![image-20221122190208864](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122190208864.png)

![image-20221122190302400](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221122190302400.png)

## 7、图片与底部缝隙

![image-20221123160243986](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221123160243986.png)

图片默认底层有空白缝隙**,通过给图片添加 vertical-align：middle 解决**

1.图片块状化。( vertical-align )只适用于 inline``inline-block 元素，也就是说对于 block 元素并没有基线对齐的说辞。

```
 img{display:block;}
```

2.图片底线对齐

```
img{vertical-align:bottom;}
```

3.行高足够小，使基线上移。

```
 .box{line-height:0} line-height的实际应用
```

## 8、两边固定，中间自适应


（1）弹性布局（圣杯布局）

设置两边子元素宽度，中间元素设置flex：1（子元素在剩余空间所占份数）

```
   section {
            display: flex;
            width: 60%;
            height: 150px;
            background-color: pink;
            margin: 0 auto;
        }
        
        section div:nth-child(1) {
            width: 100px;
            height: 150px;
            background-color: red;
        }
        
        section div:nth-child(2) {
            flex: 1;
            background-color: green;
        }
        
        section div:nth-child(3) {
            width: 100px;
            height: 150px;
            background-color: blue;
        }     
```

（2）定位

两边元素设置宽高，中间元素利用外边距向左右移动两边元素的宽

![image-20221110190112980](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110190112980.png)

```
.search-wrap {
    position: fixed;
    overflow: hidden;
    width: 100%;
    height: 44px;
    min-width: 320px;
    max-width: 640px;
}

.search-btn {
    position: absolute;
    top: 0;
    left: 0;
    width: 40px;
    height: 44px;
}

.search-login {
    position: absolute;
    right: 0;
    top: 0;
    width: 40px;
    height: 44px;
    color: #fff;
    line-height: 44px;
}

.search {
    position: relative;
    height: 30px;
    background-color: #fff;
    margin: 0 50px;
    border-radius: 15px;
    margin-top: 7px;
}
```

## 9、伪元素在布局中的应用

<img src="C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110195622179.png" alt="image-20221110195622179" style="zoom:67%;" />

（1）伪元素与兄弟分行显示

![image-20221110195904165](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110195904165.png)

```
.user {
    width: 44px;
    height: 44px;
    /* background-color: purple; */
    font-size: 12px;
    text-align: center;
    color: #2eaae0;
}
//转换为块级元素才能设置宽高，父盒子的text-align：center只能调整文字元素的位置，调整伪元素的位置则通过外边距
.user::before {
    content: "";
    display: block;
    width: 23px;
    height: 23px;
    background: url(../images/sprite.png) no-repeat -59px -194px;
    background-size: 104px auto;
    margin: 4px auto -2px;
}

```

（2）伪元素与兄弟同一行显示

![image-20221110195644243](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221110195644243.png)

```
.search {
    position: relative;
    height: 26px;
    line-height: 24px;
    border: 1px solid #ccc;
    flex: 1;
    font-size: 12px;
    color: #666;
    margin: 7px 10px;
    padding-left: 25px;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, .2);
}
//绝对定位不占有原位置，通过left等属性调整位置
.search::before {
    content: "";
    position: absolute;
    //也可以转换为行内块元素，但是需要使用vertical-align：middle
    top: 5px;
    left: 5px;
    width: 15px;
    height: 15px;
    background: url(../images/sprite.png) no-repeat -59px -279px;
    background-size: 104px auto;
}
```

## 10、调整元素位置

（1）父盒子的text-align：center只能调整文字元素的位置

（2）有定位的元素：通过top等属性调整位置，并且如果盒子没有宽度，则和transform：translateX一起调整位置，有宽度就可以和外边距一起；没有定位的元素通过外边距，内边距调整位置

## 11、子元素的文字居中

上下都有边框，又是c3盒模型，盒子做了自减，所以实际内容区域变小，原本26px的行高，在24px的内容里装不下，字体下边空白行间距溢出，所以看见字体偏下。传统盒模型就没有这个影响。解决办法：line-height：高-边框px

## 12、盒子居中

（1）块级盒子居中

```
margin：0 auto
```

注意：margin值不能用在行内或者行内块去设置居中（auto），另外，没有设置宽度的块级元素也不可以设置。场景：div>img内的图片居中，需要转换为块级元素才能，

（2）定位盒子居中

```
left：50%
margin-left：自身一半
//如果自身宽度不确定。则通过transform: translateX(-50%);
```

注意：定位的盒子无法通过margin：0 auto居中

（3）文字居中

```；1.5
text-align:center
line-heignt:盒子的高//注意：当盒子模型是css3时，需要减去内边距以及边框
line-heignt：1.5//是字体的1.5倍，这样字体的上边距以及下边距一样高，就垂直居中了
```

（4）行内块、行内元素水平居中居中

给父级元素添加text-align：center

**注意：**垂直居中设置line-height只适用于单行文本的情况！！！

## 13、给行内元素设置padding和[margin](https://so.csdn.net/so/search?q=margin&spm=1001.2101.3001.7020)是否有效

- 行内元素的margin-left、margin-right和padding-left、padding-right属性设置都是有效的
- 行内元素的margin-top、margin-bottom和padding-top、padding-bottom属性设置是无效的，但是必须注意的是，对于padding-top和padding-bottom的设置，从显示效果上来看是增加的，但其实设置是无效的，因为它们没有撑大盒子，并不会对周围的元素产生影响。
- 特别要注意，img是一个特例，它是行内块元素，对于img设置padding和margin都是有效的。

## 14、背景色渐变

![image-20221117142642297](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221117142642297.png)

注意：现在正规写法是不用私有前缀，写成to right也行！

不用私有前缀，写成to right也行！

如果加私有前缀就直接写方向，不能加 to

且如果方向改成角度，是否加私有前缀也会影响渐变朝向。

## 15、小三角

上边框与右边框，旋转90度

![image-20221117174152259](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221117174152259.png)

```
.more::after {
    content: "";
    position: absolute;
    top: 9px;
    right: 9px;
    width: 7px;
    height: 7px;
    border-top: 2px solid #fff;
    border-right: 2px solid #fff;
    transform: rotate(45deg);
}
```

交集选择器前面老师确实没说。它需要同时满足多个选择器条件  加逗号是并集，什么都不加紧挨着写是交集

交集(h3.class) 并集（.class , h3）后代（.class h3)）

## 16、ps工具

cutterman:切片工具

使用：通过ps---窗口---扩展功能打开插件

![image-20221119001230846](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221119001230846.png)

问题：（1）右侧信息没显示出来的，选择浏览器顶部第三个开发

![image-20221119002156844](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221119002156844.png)

摹客/蓝湖：（1）不用在测量宽高，字体大小，边距（点击图片到目标图片自动测量）等。（2）右边信息框的顶部可以切换单位，显示css代码，下载切图（点击图片可以再次切换为信息栏）

信息栏：![image-20221119002857995](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221119002857995.png)

## 17、元素的显示与隐藏

（1）display

![image-20221123203529760](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221123203529760.png)

（2）visibility

![image-20221123203610230](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221123203610230.png)

（3）overflow

![image-20221123203429145](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221123203429145.png)

## 18、文字溢出省略号显示

单行文本溢出

```
*1. 先强制一行内显示文本*/
white-space: nowrap; （ 默认 normal 自动换行）
/*2. 超出的部分隐藏*/
overflow: hidden;
/*3. 文字用省略号替代超出的部分*/
text-overflow: ellipsis;
```

多行文本溢出

```
overflow: hidden;
text-overflow: ellipsis;
/* 弹性伸缩盒子模型显示 */
display: -webkit-box;
/* 限制在一个块元素显示的文本的行数 */
-webkit-line-clamp: 2;
/* 设置或检索伸缩盒对象的子元素的排列方式 */
-webkit-box-orient: vertical;
```

有兼容性问题，一般在webkit浏览器或者移动端使用

## 19、margin负值运用

解决盒子边框变粗的问题；鼠标经过元素显示边框

![image-20221123212350996](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20221123212350996.png)

## 20、移动盒子位置的方法

（1）定位（2）外边距（3）2D transform 移动

注意：浮动是布局。并不能够移动盒子的位置。它根本移动位置还得靠外边距