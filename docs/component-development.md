#  Sprite自定义组件开发

----------  

<h2 id="cid_0">Sprite组件开发注意事项</h2>  

开发Sprite组件之前，务必先了解这几个注意事项，避免走弯路。

1.	由于默认情况下组件名和组件文件名一致，所以同一个应用中尽量保证模板组件名必须唯一，而且路径也必须唯一，比如buttom.component模板文件，对应的组件标签是button，那么在整个应用中就最好只有一个button组件的文件，如果有其他相同组件最好另起名字比如buttonFH。

2.	在模板内部构建模板布局的时候不能在created方法里面执行document.refresh()布局刷新操作，如非要执行可以使用定时，因为模板构建的时候整个界面都在布局中，如果执行了document.refresh() 可能会影响其他控件布局展示。

3.	在模板内部使用相对路径时，其路径是基于模板文件的相对路径。这样有利于模板资源的封装。

4.	在模板内部动态创建标签时如果想受到模板内部样式的影响 在创建控件的方法里面需要添加第二个参数this。

5.	模板外部的样式都会自动压入模板内部的第一个布局标签上面，比如button组件的样式会自动作用到button.component里面的最外层的box上，属性也是一样但除了id,style,class这三个固定属性。

6.	模板内部最外层布局标签在view层上等同于外部组件本身，但是属于不同的dom对象，如果在模板内部对外层布局标签做事件监听操作，同时外部组件有同样的事件，都会监听到。

7.	模板和外部uixml属于同一个js环境，如果在uixml里面传入一个变量到模板内部，在模板内部修改后，外部变量也会受到影响。

8.	模板和模板之间可以嵌套使用，比如在一个模板里面引入了另外一个模板。

9.	模板内部组件相对外部uixml是封闭的，如果想在uixml里面得到模板内部控件或者某个对象，需要模板开发者暴露方法。

10.	在封装模板组件的时候定义方法和事件的时候，避免使用sprite已有的方法和事件名称，比如refresh方法，sprite平台已经有这个方法，如果自己定义成相同的方法名，可能会引起冲突。

11.	由于模板使用基础控件进行封装，除自定义属性、样式、方法、事件外，模板组件具备基础组件的所有属性、样式、方法和事件。如模板组件最外层用box封装，那么模板组件本身就具备box的所有属性、样式、方法和事件。只不过模板组件封装后有特定的使用场景，有些属性、样式、方法和事件可能不太适合直接用，开发者需要在组件文档中做特殊说明。

12. Sprite组件机制中，css样式有2层，一层是外部组件的css样式，另外一层是组件内部根节点的css样式，由于模板组件具有封装的特点，所以外层样式的优先级高于内部根节点的样式。

13. 组件虽然可以帮助我们解决代码重复问题，但是不建议在一个页面里面大量使用，尽量保证一个页面里面不要超过20个封装的组件，否者在android上页面打开会有效率问题。比如在页面里面放30个button组件（在list控件里面无所谓），页面打开可能就会比较慢。简单的场景下需要重复使用某一个布局时，尽量用box进行布局，如果实在不行，可以考虑动态布局，需要用的时候在加载，不要一次性都写在页面里面。




<h2 id="cid_1">Sprite组件的引用</h2>

**引用方式一：**

**在uixml页面中引用：**

直接在xxx.uixml页面中&lt;script&gt;节点中写组件的路径或者相对路径：  

```javascript
 //直接写路径
 require("res:sprite_component/titlebar/titlebar.component");
//相对uixml页面路径（如：xxx.uixml在src根目录下）   ：
 require("sprite_component/titlebar/titlebar.component");
```

xxx.uixml中写：

```html
<titlebar />
```

**支持模板里面引用模板组件**

在xxx.component 的&lt;module&gt;节点里面写组件的路径或者相对路径：

**注：** 这里的相对路径是相对xx.component文件的相对路径。

```javascript
 //直接写路径
 require("res:sprite_component/titlebar/titlebar.component");
//相对xxx.component页面路径（如：xxx.componen在sprite_component目录下）   ：
 require("titlebar/titlebar.component");
```

xxx.component中写：

```html
<titlebar />
```


**引用方式二：**

在程序入口组件配置文件require.json中进行配置：

```
{
    "jsPaths": {
    },
    "componentPaths": {
        "titlebarUI": "res:sprite_component/titlebar/titlebar.component"
    },
    "cssPaths": {

    }
}
```

然后在xxx.uixml页面中&lt;script&gt;节点或者xxx.component页面中的&lt;module&gt;节点 里面直接引用标识key

```javascript
 require("titlebarUI");
```


**引用方式三：**

自定义标签名，如果开发者想引用不同路径下的相同标签名，可以为标签定义个别名，比如buttonFH/button.component 和buttonMY/button.component 都有button组件，想同时使用，需要定义一个别名，否者会有冲突。

对于require的第一个参数，可以是路径也可以是require.json中配置的标识key，需要注意第一个参数不能相同。

```javascript
 require("res:buttonFH/button.component","button_fh");
 require("res:buttonMY/button.component","button_my");
```

页面中对应写法：

```html
<button_fh />
<button_my />
```



<h2 id="cid_2">Sprite组件开发步骤</h2>  

下面我以封装一个button控件为例，来说明组件的开发步骤：

**第一步，新建模板文件mybutton.component**

由于官方组件里面已经有button组件了，所以重新换个名字mybutton。

在新建模板组件文件前，先创建一个模板组件目录，该目录用来存放组件文件、组件样式还有一些图片资源。

新建模板文件时，模板组件的名称必须和模板文件名保持一致，比如我们想做一个mybutton组件，那么文件名就必须是mybutton.component。另外整个应用全局组件名称不能相同。

模板文件框架内容如下：

```html
<page>
  <module>
    <![CDATA[
     <!--这里可以引用其他模板组件文件-->

    ]]>
  </module>
  <script>
    <![CDATA[

    //定义一个模板类
    var MYbutton = function () {
      //这里定义一些变量

    };

    MYbutton.prototype = {
      //模板组件创建的时候执行
      created: function () { },
      //属性变更回调函数
      attrChanged: function (attrName, attrValue,tagInit) { },
      //样式变更回调函数
      styleChanged: function (styleName, styleValue,tagInit) { },
      //监听横竖屏切换时候的回调
      orientationChanged: function (orientation, screenWidth, screenHeight) { }

    }

    module.exports = MYbutton;
    ]]>
  </script>
  <style>

  </style>
  <ui>
    <box>

    </box>
  </ui>
</page>
```

上述代码中是模板组件的基本结构，其中：

> module节点里面可以通过requrie("模板地址")来引用其他模板组件；
> 
> script节点用来写模板的js；
> 
> style节点用来写模板的样式；
> 
> ui节点用来写模板的布局


模板组件里面有几个固定回调函数：

created：模板组件创建的时候会执行，只要在uixml布局里面写模板标签，该函数就会执行。

attrChanged：一般用于外部对组件模板进行属性动态修改时，当模板组件的属性发生变化时进入该回调函数。如果属性直接写在组件标签上面，该函数不会执行。其中attrName:属性名称、attrValue:属性值、tagInit:是否是初始的时候执行，初始的时候为true，其他时候通过js调用都为false，可以通过该属性判断初始的时候要不要刷新布局。

styleChanged：一般用于外部对组件模板样式进行动态修改时，会进入该回调函数。如果样式直接写在组件标签上面，该函数不会执行。其中styleName:样式名称、styleValue:样式值、tagInit:是否是初始的时候执行，初始的时候为true，其他时候通过js调用都为false，可以通过该属性判断初始的时候要不要刷新布局。

orientationChanged：函数为页面横竖屏切换回调函数,该函数用于页面横竖屏时通知组件内部处理相关逻辑，回调函数参数：

> orientation：当前屏幕方向,字符串枚举类型,【portrait, landscape】,portrait:竖屏,landscape:横屏  
> 
> screenWidth：当前窗口绘制区域宽度； 
>  
> screenHeight：当前窗口绘制区域高度  


模板组件对外接口主要通过&lt;script&gt;节点中定义的module.exports对象来标识，这个千万不能少。

然后在home.js的入口处，引用该组件：

```javascript
require.config({
    jsPaths: {
    },
    componentPaths: {
        "mybuttonUI": "res:sprite_component/mybutton/mybutton.component"
    },
    cssPaths: {
     
    }
});
```

到时候，我们在页面里面引用的时候就可以写require("mybuttonUI");


**第二步：设计组件**

根据[组件开发设计六大原则](https://gitdocument.exmobi.cn/sprite-senior/component-principle.html)设计组件，定义好组件的属性，样式，方法和事件。

**属性：**

> builtInClass 使用内置样式
> 
> value：按钮文字（可以通过js修改）；
> 
> tip：小气泡数字（可以通过js修改）；
> 
> licon；按钮上的图片（可以通过js修改）；
> 
> ricon；按钮上的图片（可以通过js修改）；
> 
> readonly:只读（可以通过js修改）；
> 
> loading:加载等待，取值true和false （可以通过js修改）；

**样式：**

> color:按钮文字颜色（可以通过js修改）；
> 
> color-click:按钮文字点击颜色 默认白色；
> 
> background-color：按钮背景颜色 （可以通过js修改）；
> 
> background-color-click：按钮背景点击色；
> 
> border-color:边框颜色（可以通过js修改）；
> 
> border-color-click：按钮边框点击色；
> 
> tip-color：小气泡字体颜色 （可以通过js修改）；
> 
> tip-background-color：小气泡背景色 （可以通过js修改）；
> 
> font-size: 按钮字体大小（可以通过js修改）；
> 
> licon-width: 按钮左侧图片宽度（可以通过js修改）；
> 
> licon-height: 按钮左侧图片高度（可以通过js修改）；
> 
> ricon-width: 按钮右侧图片宽度（可以通过js修改）；
> 
> ricon-height: 按钮右侧图片高度（可以通过js修改）；

**方法：**

> click(): 模拟按钮点击方法，可以js模拟点击按钮效果；

**事件：**

> hange事件：监听button属性修改；




**第三步：组件样式布局**

模板组件内部必须要有一个根节点，一般是box标签，然后按照设计进行界面布局，模板组件的样式可以写在&lt;style&gt;节点里面，比如我们要做一个button组件，可以先做好布局如下：

```html
<box id="btn" class="button">
            <image id="btn_image" class="button-image" style="display:none" />
            <box id="btn_content" class="row-flex-center">
                <image id="loadingimage" style="width:15;height:15;display:none" class="button-image" src="loading.gif" />
                <text id="text" class="button-text"></text>
            </box>
            <box id="tipbox" class="tip" style="display:none">
                <text id="tiptext" class="tip-text">9</text>
            </box>
            <image id="btn_rimage" class="button-image" style="display:none" />
</box>
```

样式可以提取到css文件中进行引用，这里写的是样式文件的相对路径，该相对路径基于xx.component文件。

<img src="image/component_1.png"/>

```html
 <style>     
        @import url("mybutton.layout.css");
        @import url("mybutton.color.css");
</style>
```

mybutton.layout.css 文件内容：

```css
.flex1{
	flex:1;
}

.margin4{
	margin:4;
}

.radius4{
	border-radius:4;
}

.row-flex-center{
	flex-direction:row;
	justify-content:center;	
	align-items:center;
}

.column-flex-center{
	flex-direction:column;
	justify-content:center;
	align-items:center;
}
.button{
	justify-content:center;
	flex-wrap:wrap;
	flex-direction:row;	
	height:40;
	padding:0 8 0 8;
}

.button-image{
	
	width:24;
	height:24;
	margin:0 4 0 4;
	align-self: center;
}

.button-rimage{
	
	width:24;
	height:24;
	margin:0 4 0 4;
	align-self: center;
}

.button-text{
	text-align:center;
	text-overflow:ellipsis;
	singleline:true;
	line-space:0;
	padding: 0;
	font-size:16;
	
}

.round{
	width:40;
	height:40;
	border-radius:20;	
}

/**********  tip样式 开始 ************/
.tip{	
	flex-direction:row;
	align-items:center;
	height:20;
	
	justify-content:center;
	border-radius:10;
	margin:0 0 0 8;
	align-self: center;

}
.tip-outline{	
	flex-direction:row;
	align-items:center;
	height:20;
	
	justify-content:center;
	border-radius:10;
	margin:0 0 0 8;
	align-self: center;
}

.tip-text{

	text-align:center;	
	line-space:0dp;
	font-size:12;
	margin:0 6.5 0 6.5;
}
/**********  tip样式 结束 ************/
```

mybutton.color.css 文件内容：

```
.button{
	background-color:#549FF7;
	border-color:#549FF7;	
}

.tip{
	background-color:rgba(0, 0, 0, 0.15);
}

.tip-text{	
	color:#ffffff;
}

.tip-outline{
	background-color:red;
}

.button-text{
	color:#ffffff;
}
.button-click{
	background-color:#295b9d;
	border-color:#295b9d;	
}

.submit{
  background-color: #88d038;
  border-color:#88d038;
 /* 基于按钮自定义样式*/

  border-color-click:#669d2a;
  background-color-click:#669d2a;
}

.submit-click{
	border-color:#669d2a;
	background-color:#669d2a;
}
.cancel{
	background-color:#dd524d;
	border-color:#dd524d;
	/*自定义样式*/
	border-color-click:#a63e3a;
	background-color-click:#a63e3a;	
}
.cancel-click{
	border-color:#a63e3a;
	background-color:#a63e3a;
}

.disable{		
	background-color:#d9d9d9;
}
...

.outline{
	border-width:1dp;	
	background-color:transparent;
	/*基于按钮自定义样式*/
	color:#549FF7;
	color-click:#ffffff;
	tip-background-color:red;

}
.outline-text{	
	color:#549FF7;	
}

.outline-submit-text{
	color:#669d2a;	
}
.outline-cancel-text{
	color:#dd524d;	
}

.outline-disable{
	border-color:#d9d9d9;
	border-width:1dp;
	background-color:transparent;
    /*自定义样式*/
	color:#d9d9d9;	
}
.outline-disable-text{
	color:#d9d9d9;
}
```

如果马上要在页面看效果可以直接在uixml页面写&lt;mybutton /&gt;，

**第四步：组件js逻辑完善**

组件核心js部分如下：

```html
<!-- 
    样式（普通 、内置样式包括：sumbit cancel outline 注意：混合使用时outline必需在最后）；
    box公共样式;
    
    
    color:按钮文字颜色（可以通过js修改）；
    color-click:按钮文字点击颜色 默认白色；
    background-color：按钮背景颜色 （可以通过js修改）；
    background-color-click：按钮背景点击色；
    border-color:边框颜色（可以通过js修改）；
    border-color-click：按钮边框点击色；
    tip-color：小气泡字体颜色 （可以通过js修改）；
    tip-background-color：小气泡背景色 （可以通过js修改）；
    font-size: 按钮字体大小（可以通过js修改）；
    licon-width: 按钮左侧图片宽度（可以通过js修改）
    licon-height: 按钮左侧图片高度（可以通过js修改）
    ricon-width: 按钮右侧图片宽度（可以通过js修改）
    ricon-height: 按钮右侧图片高度（可以通过js修改）

    属性：
    builtInClass 使用内置样式
    value：按钮文字（可以通过js修改）；
    tip：小气泡数字（可以通过js修改）；
    licon；按钮上的图片（可以通过js修改）；
    ricon；按钮上的图片（可以通过js修改）；
    readonly:只读（可以通过js修改）；
    loading:加载等待，取值true和false （可以通过js修改）；

    方法：
    box公共方法；
    click(): 模拟按钮点击方法，可以js模拟点击按钮效果；

    事件：
    box公共事件；
    change事件：监听button属性修改；
 -->
<page>
    <module>
        <![CDATA[
    
    ]]>
    </module>
    <script>
        <![CDATA[
            var document = require("Document");
            var window = require("Window");
            var time = require("Time");
            var console = require("Console");
            var ui = require("UI");

            function aniCallBack() {

            }

            var Button = function() {

                this.box = null;
                this.btn = null;
                this.text = null;
                this.tiptext = null;
                this.tipbox = null;
                this.btn_image = null;
                this.btn_rimage = null;
                this.fontText = null;
                this.loadingimage = null;
                this.btn_content = null;
                this.readonlydom = null;

                this.backgroundColor = "#549FF7";
                this.backgroundClickColor = "#295b9d";
                this.borderColor = "#549FF7";
                this.borderClickColor = "#295b9d";
                this.color = "#ffffff";
                this.colorClick = "#ffffff";
                this.tipColor = "#ffffff";
                this.tipbackgroundColor = "red";
                this.fontSize = "16";
                this.liconWidth = "24";
                this.liconHeight = "24";
                this.riconWidth = "24";
                this.riconHeight = "24";

                this.readonly = "false";
                this.value = "";
                this.tip = "0";
                this.licon = "";
                this.ricon = "";

                this.loading = "false";
                // this.taginit = true;
                this.classStyle = "";
                this.pathLocation = "";

            };



            Button.prototype = {

                created: function() {

                    //这里是得到组件所在的uixml页面的绝对路径目录
                    this.pathLocation = document.getPathLocation();
                    this.readonlydom = this.getElement("readonly");
                    this.box = this.getElement("ctrlbox");
                    this.btn = this.getElement("btn");
                    this.text = this.getElement("text");
                    this.tiptext = this.getElement("tiptext");
                    this.tipbox = this.getElement("tipbox");
                    this.btn_image = this.getElement("btn_image");
                    this.btn_rimage = this.getElement("btn_rimage");
                    this.loadingimage = this.getElement("loadingimage");

                    this.btn_content = this.getElement("btn_content");
                    if (this.getClassStyle() != null) {
                        this.classStyle = this.getClassStyle();
                    }

                    this.init();
                    var copythis = this;
                    this.box.on("touchDown", function(e, param) {

                        if (copythis.readonly == "false") {
                            var jsonData = {};
                            var aniAry = new Array();
                            var jsonAni1 = {};
                            jsonAni1.delay = 0;
                            jsonAni1.duration = 50;
                            jsonAni1.curve = "linear";
                            jsonAni1.props = {};
                            jsonAni1.props.backgroundColor = copythis.backgroundClickColor;
                            aniAry.push(jsonAni1);
                            jsonData.animators = aniAry;
                            copythis.box.startAnimator(jsonData, function() {


                            });
                            copythis.text.setStyle("color", copythis.colorClick);
                            copythis.box.setStyle("border-color", copythis.borderClickColor);
                            copythis.box.setStyle("opacity", "1");
                            copythis.box.releaseAnimator();
                        }

                    });

                    this.box.on("touchUp", function(e) {
                        if (copythis.readonly == "false") {

                            var jsonData = {};
                            var aniAry = new Array();
                            var jsonAni1 = {};
                            jsonAni1.delay = 0;
                            jsonAni1.duration = 50;
                            jsonAni1.curve = "linear";
                            jsonAni1.props = {};
                            jsonAni1.props.backgroundColor = copythis.backgroundColor;
                            aniAry.push(jsonAni1);
                            jsonData.animators = aniAry;
                            copythis.box.startAnimator(jsonData, function() {
                                //copythis.box.releaseAnimator();

                            });
                            copythis.text.setStyle("color", copythis.color);
                            copythis.box.setStyle("opacity", "1");
                            copythis.box.setStyle("border-color", copythis.borderColor);
                            //这里释放动画，以便后续可以对该控件进行刷新
                            copythis.box.releaseAnimator();
                        }

                    });
                    this.box.on("touchCancel", function(e) {
                        if (copythis.readonly == "false") {
                            var jsonData = {};
                            var aniAry = new Array();
                            var jsonAni1 = {};
                            jsonAni1.delay = 0;
                            jsonAni1.duration = 50;
                            jsonAni1.curve = "linear";
                            jsonAni1.props = {};
                            jsonAni1.props.backgroundColor = copythis.backgroundColor;
                            aniAry.push(jsonAni1);
                            jsonData.animators = aniAry;
                            copythis.box.startAnimator(jsonData, aniCallBack);
                            copythis.text.setStyle("color", copythis.color);
                            copythis.box.setStyle("opacity", "1");
                            copythis.box.setStyle("border-color", copythis.borderColor);

                            //这里释放动画，以便后续可以对该控件进行刷新
                            copythis.box.releaseAnimator();

                        }

                    });



                },
                init: function() {

                    var builtInClass = this.getAttr("builtInClass");

                    if (builtInClass != null) {

                        if (builtInClass.indexOf("outline") > -1 && builtInClass.indexOf("submit") < 0 && builtInClass.indexOf("cancel") < 0) {
                            this.setClassStyle(this.classStyle + " outline", this);
                            this.classStyle = this.classStyle + " outline";
                        }


                        if (builtInClass.indexOf("submit") > -1) {

                            this.setClassStyle(this.classStyle + " submit", this);

                            this.classStyle = this.classStyle + " submit";


                        } else if (builtInClass.indexOf("submit") > -1 && builtInClass.indexOf("outline") > -1) {

                            this.setClassStyle(this.classStyle + " submit outline outline-submit-text", this);

                            this.classStyle = this.classStyle + "  submit outline outline-submit-text";
                        }

                        if (builtInClass.indexOf("cancel") > -1) {

                            this.setClassStyle(this.classStyle + " cancel", this);
                            this.classStyle = this.classStyle + "  cancel";
                        } else if (builtInClass.indexOf("cancel") > -1 && builtInClass.indexOf("outline") > -1) {
                            this.setClassStyle(this.classStyle + " cancel outline outline-cancel-text", this);
                            this.classStyle = this.classStyle + "   cancel outline outline-cancel-text";
                        }
                    }


                    var getParent = this.getParent();
                    if (getParent != null) {
                        if (getParent.getStyle("flex-direction") != "row") {
                            this.btn_content.setStyle("flex", "1");
                            this.text.setStyle("flex", "1");
                        }
                    }
                },
                click: function() {
                    var copythis = this;
                    this.fire("touchDown");
                    time.setTimeout(function() {
                        copythis.fire("touchUp");

                        copythis.fire("click");

                    }, 200);


                },
                //属性变更回调函数
                attrChanged: function(attrName, attrValue, taginit) {
                    var changetag = false;
                    if (taginit == false) {

                        if (this.getAttr(attrName) != this[attrName] && this.getAttr(attrName) != null) {
                            //this.fire("change");
                            changetag = true;
                        }
                    }


                    if (attrName == "value") {
                        if (attrValue != null) {
                            this.value = attrValue;
                        }

                        this.text.setText(this.value);
                        if (taginit == false) {
                            this.refresh();
                        }
                    }

                    if (attrName == "readonly") {

                        var borderColorStyle = this.getStyle("border-color");

                        if (attrValue != null) {
                            this.readonly = attrValue;
                        }
                        if (this.readonly == "true") {

                            this.readonlydom.setStyle("visibility", "visible");
                            if (borderColorStyle != null) {
                                this.box.setStyle("border-color", "#ececec");
                            }

                        } else {
                            this.readonlydom.setStyle("visibility", "hidden");
                            if (borderColorStyle != null) {
                                this.box.setStyle("border-color", this.borderColor);
                            }


                        }
                    }

                    if (attrName == "tip") {
                        if (attrValue != null) {
                            this.tip = attrValue;
                            this.tiptext.setText(attrValue);
                            this.tipbox.setStyle("display", "block");
                        } else {

                            this.tipbox.setStyle("display", "none");

                        }
                        if (taginit == false) {
                            this.refresh();
                        }
                    }


                    if (attrName == "licon") {

                        if (attrValue != null) {
                            if (attrValue.indexOf("res:") > -1 || attrValue.indexOf("http://") > -1 || attrValue.indexOf("https://") > -1) {
                                this.licon = attrValue;
                            } else {
                                this.licon = this.pathLocation + "/" + attrValue;
                            }

                            this.btn_image.setAttr("src", this.licon);
                            this.btn_image.setStyle("display", "block");
                        } else {

                            this.btn_image.setStyle("display", "none");

                        }

                        if (taginit == false) {
                            this.refresh();
                        }
                    }

                    if (attrName == "ricon") {

                        if (attrValue != null) {
                            //通过js进来的图片地址，都认为是基于uixml页面的相对路径，符合开发者思维
                            if (attrValue.indexOf("res:") > -1 || attrValue.indexOf("http://") > -1 || attrValue.indexOf("https://") > -1) {
                                this.ricon = attrValue;
                            } else {
                                this.ricon = this.pathLocation + "/" + attrValue;
                            }
                            this.btn_rimage.setAttr("src", this.ricon);
                            this.btn_rimage.setStyle("display", "block");
                        } else {
                            this.btn_rimage.setStyle("display", "none");
                        }

                        if (taginit == false) {
                            this.refresh();
                        }
                    }

                    if (attrName == "loading") {
                        if (attrValue != null) {
                            this.loading = attrValue;
                        }
                        if (this.loading == "true") {
                            this.loadingimage.setStyle("display", "block");
                        } else {
                            this.loadingimage.setStyle("display", "none");
                        }

                        if (taginit == false) {
                            this.refresh();
                        }
                    }

                    if (changetag == true) {
                        this.fire("change");
                    }
                },
                //样式变更回调函数
                styleChanged: function(styleName, styleValue, taginit) {
                    // console.log("taginit++" + taginit);
                    var copythis = this;
                    if (styleName == "color") {
                        if (styleValue != null) {
                            this.color = styleValue;
                        }

                        // console.log("this.color:"+this.color);
                        this.text.setStyle("color", this.color);
                    }

                    if (styleName == "width") {

                        if (styleValue != null) {
                            this.btn_content.setStyle("flex", "1");
                            this.text.setStyle("flex", "1");
                        }
                    }


                    if (styleName == "flex") {
                        if (styleValue != null) {
                            this.btn_content.setStyle("flex", "1");
                            this.text.setStyle("flex", "1");
                        }
                    }




                    if (styleName == "color-click") {
                        if (styleValue != null) {
                            this.colorClick = styleValue;
                        }
                    }


                    if (styleName == "background-color") {
                        if (styleValue != null) {
                            this.backgroundColor = styleValue;

                        }

                        if (taginit == false) {

                            time.setTimeout(function() {
                                //console.log("修改了颜色");
                                //通过js修改颜色会受动画延迟影响，这里做个延迟
                                copythis.box.setStyle("background-color", copythis.backgroundColor);
                            }, 250);


                        } else {
                            this.box.setStyle("background-color", this.backgroundColor);
                        }

                    }
                    if (styleName == "background-color-click") {
                        if (styleValue != null) {
                            this.backgroundClickColor = styleValue;
                        }
                    }

                    if (styleName == "border-color-click") {
                        if (styleValue != null) {
                            this.borderClickColor = styleValue;
                        }
                    }
                    if (styleName == "border-color") {
                        if (styleValue != null) {
                            this.borderColor = styleValue;
                        }

                        this.box.setStyle("border-color", this.borderColor);
                    }


                    if (styleName == "tip-background-color") {
                        if (styleValue != null) {
                            this.tipbackgroundColor = styleValue;
                        }
                        this.tipbox.setStyle("background-color", this.tipbackgroundColor);

                    }
                    if (styleName == "tip-color") {
                        if (styleValue != null) {
                            this.tipColor = styleValue;
                        }
                        this.tiptext.setStyle("color", this.tipColor);

                    }

                    if (styleName == "font-size") {
                        if (styleValue != null) {
                            this.fontSize = styleValue;
                        }
                        this.text.setStyle("font-size", this.fontSize);
                        if (taginit == false) {

                            this.refresh();
                        }
                    }

                    if (styleName == "licon-width") {

                        if (styleValue != null) {
                            this.liconWidth = styleValue;
                        }
                        this.btn_image.setStyle("width", this.liconWidth);
                        if (taginit == false) {

                            this.refresh();
                        }
                    }

                    if (styleName == "licon-height") {

                        if (styleValue != null) {
                            this.liconHeight = styleValue;
                        }
                        this.btn_image.setStyle("height", this.liconHeight);
                        if (taginit == false) {

                            this.refresh();
                        }
                    }

                    if (styleName == "ricon-height") {

                        if (styleValue != null) {
                            this.riconHeight = styleValue;
                        }
                        this.btn_rimage.setStyle("height", this.riconHeight);
                        if (taginit == false) {

                            this.refresh();
                        }
                    }

                    if (styleName == "ricon-width") {

                        if (styleValue != null) {
                            this.riconWidth = styleValue;
                        }
                        this.btn_rimage.setStyle("height", this.riconWidth);

                        if (taginit == false) {

                            this.refresh();
                        }
                    }



                }
            }
            module.exports = Button;

        ]]>
    </script>
    <style>
        @import url("button.layout.css");
        @import url("button.color.css");
        .readonly {


            background-color: #ffffff;
            opacity: 0.7;
            flex-direction: row;
            align-items: center;
            padding: 0 0 0 0;
            margin: 0;
            position: absolute;
            left: 0;
            top: 0;
            bottom: 0;
            right: 0;
            visibility: hidden;
        }
    </style>
    <ui>
        <box class="ctrlbox" id="ctrlbox">

            <box id="btn" class="button">

                <image id="btn_image" class="button-image" style="display:none" />
                <box id="btn_content" class="row-flex-center">
                    <image id="loadingimage" style="width:15;height:15;display:none" class="button-image" src="loading.gif" />
                    <text id="text" class="button-text"></text>
                </box>
                <box id="tipbox" class="tip" style="display:none">
                    <text id="tiptext" class="tip-text">9</text>
                </box>
                <image id="btn_rimage" class="button-image" style="display:none" />
            </box>
            <box id="readonly" class="readonly" />
        </box>
    </ui>
</page>
```

上述代码中的this指的就是当前组件，通过this.getElement("xx")可以得到组件内部的布局元素，this也表示当前组件类，可以得到自定义的组件类里面的变量和方法。

注意代码中有很多var copythis = this的写法，是为了在某些监听事件里面用，因为进入事件里面，那么this就表示的是当前监听事件的控件对象了。

在代码中还自定义了一个init方法，其目的是为了处理组件上面的属性和样式，由于styleChanged和attrChange，只能通过组件js赋值才会生效，所以自己在init里面通过this.setStyle和this.setAttr人为的触发一下styleChanged和attrChange，让里面的逻辑可以执行一次。否者可能要写两遍相同的逻辑。

另外还需要注意的是，由于在处理组件初始布局的时候，不能做刷新操作，但是styleChanged和attrChange里面在某些时候必须要刷新布局，所以定义了一个this.taginit用来标识是第一次初始化组件还是后来通过外部js调用的。

代码中还自定义了一个click方法，这个方法是给外部模拟点击按钮用的。其实组件里面自定义的方法外部都可以调用，只不过有些方法是给内部用的，比如init方法，有些是给外部用的，这个需要开发者在后期组件使用文档中进行说明，哪些方法是可以外部调用。

在组件里面如果要和外部进行通信，最好使用事件里面的fire来触发外部的监听事件，fire可以进行传参，关于fire的用法可以参考 [https://gitdocument.exmobi.cn/sprite-api/ggff.html](https://gitdocument.exmobi.cn/sprite-api/ggff.html)。

