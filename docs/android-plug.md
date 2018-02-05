#  Android原生插件开发

----------  

<h2 id="cid_0">概述</h2>  


Android原生插件开发分为UI插件和功能插件，其中功能插件又分为单例插件和多例插件。  

单例插件继承于FHSingletonComponent。  

多例插件继承于FHMultitonComponent。  

UI插件继承于FHUIComponent，其中UI插件分为两个部分，一个是视图控件，继承于FHView，主要用于界面展示和用户交互部分；另外一个是JS对象，继承于FHUIComponent，主要用于js中的处理。  


<h2 id="cid_1">环境搭建</h2>   


1：开发环境：Android studio 2.3.1及以上  

2：EDN论坛下载Sprite[  Android原生开发工程](https://www.exmobi.cn/downloadRedirect.jsp?type=sprite_plugin_android)，该工程与EDN打包工程相同，包含Sprite基础SDK aar包，基础SDK使用的第三方库等  

3：运行该工程，若一切正常则可进入如下图所示Sprite界面  

<img src="image/android_0.png"  width="250"/>


<h2 id="cid_2">插件开发说明</h2>   

**基础类说明** 

 Sprite已经提供了完善的插件开发及加载相关机制，开发者只需要继承插件基础类，重载关键方法，即可快速实现插件开发。  

其中FHUIComponent，FHMultitonComponent，FHSingletomComponent均继承于FHComponent，组件开发时类相关方法说明如下。

**FHUIView 的方法 ：**  

> **init () : void**   初始化函数  
> 
> **registerEvent () : void**	注册触摸事件   
> 
> **getSysView () : View**    获取系统View  
> 
> **addSubView (FHDomObject child) : void** 	插入子节点   
> 
> 参数:   
> child -- 待添加子节点dom对象
> 
> 
> **removeChild (FHDomObject child) : void** 	去除子节点 
> 
> 参数:    
> child -- 待删除子节点dom对象  
> 
> 
> **updateAttribute () : void ** 	更新属性 
> 
> 
> **updateStyle () : void ** 	更新样式   
> 
> 
> **textChanged (boolean isInit) : void **	文本修改后处理  
> 
> 参数:  
> isInit -- 是否在初始化时调用。若设为 false, 表示通过 js 调用，如果样式影响了布局，需要通知布局刷新。  
> 
> 
> **attributeChanged (String attrName, String attrValue, boolean isInit) : void** 	属性修改后处理  
> 
> 参数:   
> attrName -- 属性名  
> attrValue -- 属性值  
> isInit -- 是否在初始化时调用。若设为 false, 表示通过 js 调用，如果样式影响了布局，需要通知布局刷新。   
> 
> **cssChanged (String styleName, String styleValue, boolean isInit) : boolean** 	样式修改后处理  
> 
> 参数:   
> styleName -- 属性名  
> styleValue -- 属性值  
> isInit -- 是否在初始化时调用。若设为 false, 表示通过 js 调用，如果样式影响了布局，需要通知布局刷新。    
> 
> **setBackgroundColor (int bgColor) : void**	设置背景颜色 
> 
> **updateBorder () : boolean**	更新边框  
> 
> 返回值: 是否更新  
> 
> **createChildView () : void**  	遍历创建所有子节点   
> 
> **updateChildView () : void**	遍历更新所有子节点   
> 
> **updateChildFrame () : void**	遍历刷新子节点布局   
> 
> **update() : void**	 更新界面，包括样式、属性和文本   
> 
> **updateFinish () : void**	更新样式和属性结束后调用  
> 
> **updateFrame () : void**	刷新布局  
> 
> **preferenceChanged () : void** 	通知刷新CSS Node  
> 
> **postInvalidate () : void**	通知系统调用onDraw方法 
> 
> **measure (YogaNodeAPI yogaNodeAPI, float width, YogaMeasureMode widthMeasureMode, float height, YogaMeasureMode heightMeasureMode) : void**	    控件测量函数   
> 
> 参数:   
> yogaNodeAPI -- CSS Node API  
> width -- 宽度  
> widthMeasureMode -- 宽度模式  
> height -- 高度  
> heightMeasureMode -- 高度模式    
> 
> **setFrame (Size size, Rect rect) : void**	 设置控件Frame  
> 
> 参数:   
> size -- 控件尺寸  
> rect -- 控件矩形区域  
> 
> 
> **parseStylePath (String oldPath) : String**	解析样式路径 
> 
> **parseAttrPath (String oldPath) : String**	解析属性路径 
> 
> **onResume () : void**	 处理Activity onResume事件(view可见) 
> 
> **onChildResume () : void**	 子节点处理Activity onResume事件(view可见) 
> 
> **onPause () : void**	处理Activity onPause 事件(view不可见或者被遮挡) 
> 
> **onChildPause () : void**	子节点处理Activity onPause 事件(view不可见或者被遮挡)   
> 
> **destroy () : void**	  销毁对象 


 **FHComponent 的方法 ：**

> **on (String[] params) : void**	注册事件
> 
> **fire (String[] params) : void**	触发事件  
> 
> **off(String[] params) : void**	移除事件 
> 
> **getOn(String[] params) : int[]**  	获取已注册事件函数列表

**FHUIComponent 的方法 ：**  

> **on (String[] params) : void**	注册事件  
> 
> **fire (String[] params) : void**	触发事件  
> 
> **off(String[] params) : void**	移除事件  
> 
> **getOn(String[] params) : int[]	**  获取已注册事件函数列表  
> 
> **getFrame (String[] params) : JSONObject** 	获取控件Frame  
> 
> **setFrame (String[] params) : void**	设置控件Frame  
> 
> **getCenter (String[] params) : JSONObject**	 获取中心点坐标  
> 
> **getAbsoluteFrame (String[] params) : JSONObject**  	获取控件绝对布局Frame  
> 
> **getParent (String[] params) : JavaScriptObject**  	获取父节点  
> 
> **getNext (String[] params) : JavaScriptObject**	获取相邻的后一个兄弟节点  
> 
> **getPrevious (String[] params) : JavaScriptObject**	获取相邻的前一个兄弟节点  
> 
> **remove (String[] params) : void**	去除当前节点  
> 
> **clone (String[] params) : JavaScriptObject** 	克隆一个节点  
> 
> **setText (String[] params) : void**	设置文本  
> 
> **getText (String[] params) : ParamObject**	获取文本  
> 
> **getAttr (String[] params) : String**	获取节点某个属性  
> 
> **getAttrs (String[] params) : JSONObject**	获取节点所有属性  
> 
> **removeAttr (String[] params) : void**	删除某个属性  
> 
> **hasAttr (String[] params) : boolean**	是否包含某个属性  
> 
> **setAttr (String[] params) : void**	设置某个属性  
> 
> **getId (String[] params) : String**	获取节点的ID  
> 
> **getTag (String[] params) : String**	获取节点的TAG  
> 
> **getStyle (String[] params) : String **	获取节点某个样式  
> 
> **clearStyle (String[] params) : void	** 清除节点所有样式  
> 
> **setStyle (String[] params) : void**	设置节点某个样式  
> 
> **getClassStyle (String[] params) : String**	获取节点的class样式  
> 
> **setClassStyle (String[] params) : void**	设置节点的class样式  
> 
> **getElement (String[] params) : JavaScriptObject**	获取某个节点  
> 
> **getElements (String[] params) : JavaScriptObject[]**	 获取符合rule值的所有节点  
> 
> **getChildren (String[] params) : JavaScriptObject[]**	获取所有子节点  
> 
> **getFirstChild (String[] params) : JavaScriptObject**	获取第一个子节点  
> 
> **getLastChild (String[] params) : JavaScriptObject**	获取最后一个子节点  
> 
> **appendChild (String[] params) : void**	在末尾添加一个子节点  
> 
> **replaceChild (String[] params) : void**	替换某个节点  
> 
> **insertBefore (String[] params) : void** 	在某个节点前插入一个节点  
> 
> **insertAfter (String[] params) : void**	在某个节点后插入一个节点  
> 
> **clear (String[] params) : void** 	清除所有子节点  
> 
> **startAnimation (String[] params) : void**	启动组件动画  
> 
> **startAnimator (String[] params) : void** 	启动属性动画  
> 
> **releaseAnimator (String[] params) : void**	暂停属性动画  
> 
> **startKeyFrameAnimator (String[] params) : void**	启动帧动画  
> 
> **getInnerHTML (String[] params) : String** 	获取HTML


**IFHActivityResultListener** 

> **handleActivityResult(int requestCode, int resultCode, Intent data):void	** 页面Activity回调函数  
> 
> 参数：  
> requestCode：确认返回的数据是从哪个Activity返回的，这个requestCode和startActivityForResult中的requestCode相对应
> resultCode：由上层Activity通过其setResult()方法返回
> data：一个Intent对象，带有返回的数据  

<h2 id="cid_3">UI插件开发</h2>   

开发过程说明：

#### 1. 建立一个壳工程。
新建一个 android 工程的时候，android studio 会自动创建一个 app 模块。我们需要新建一个 application 类，继承系统的 application 类，在它的 onCreate 方法中添加  `FHSDKEngine.initSpriteApplication(this);` 初始化 sprite application。

![application](image/application.PNG)

创建 application 之后，需要在 AndroidManifest.xml 中引用它`android:name=".MyApplication"`。

![android manifest](image/android_manifest.PNG)

接着我们需要初始化 sprite sdk。

![main activity](image/main_activity.PNG)

#### 2. 新建一个 SDK 模块。
sdk 模块是从 sdk-release.aar 创建得到。

![create sdk1](image/create_sdk1.PNG)

![create sdk2](image/create_sdk2.PNG)

#### 3. 新建一个组件模块。
button 模块是一个 android library 模块。

![create button1](image/create_button1.PNG)

![create button2](image/create_button2.PNG)

首先在 button 模块的 build.gradle 文件中添加必要的依赖，这样就可以使用 sprite sdk 中的 api 了。

![button build](image/button_build.PNG)

接着我们创建 view 和 component 了。

![button view](image/button_view.PNG)

![button component](image/button_component.PNG)

写完 view 和 component 之后，我们需要回到 MainActivity 中注册 view `FHSDKEngine.registerView("button", "com.fiberhome.button.UIButton");`和注册 component `FHSDKEngine.registerComponentByClassName("button", "com.fiberhome.button.ButtonComponent");`。

![main activity with register](image/main_activity_with_register.PNG)

此外，还需要在 app 模块中的 build.gradle 文件中引用 button 组件。

![app build](image/app_build.PNG)

UI插件开发：

1：以开发一个最简单的button为示例。

2：新建一个java类FHUIButton继承于FHView,如下代码所示：

```java
public class FHUIButton extends FHUIView
{

   public Button button;

   /**
    * 构造函数
    *
    * @param domObject
    */
   public FHUIButton(FHDomObject domObject)
   {
      super(domObject);
      // 设置插件的MeasureFunction有效
      this.domObject.getCssNode().setMeasureFunction(this);
      // 建立一个Android原生Button控件
      this.button = new Button(this.domObject.pageInstance.activity);
      // 将该Button组件与FHUIView的sysView属性进行绑定
      this.sysView = this.button;
   }

    /**
     * 在这个函数进行一些初始化的工作
     */
   @Override
   public void init()
   {
      super.init();
   }

   /**
    * 调用此函数来刷新布局
    */
   @Override
   public void updateFrame()
   {
      super.updateFrame();
   }

   /**
    * 调用此函数更新样式
    */
   @Override
   public void updateStyle()
   {
      super.updateStyle();
   }

   /**
    * 属性发生改变时调用此函数
    * 
    * @param attrName
    *            属性名称
    * @param attrValue
    *            属性值
    * @param isInit
    *            是否在初始化时调用 false: true非初始化调用,即通过JS修改,如果样式影响了布局,需要通知布局刷新
    */
   @Override
   public void attributeChanged(String attrName, String attrValue, boolean isInit)
   {
      super.attributeChanged(attrName, attrValue, isInit);
      if ("value".equals(attrName))
      {
         String textValue = FHUIButton.this.domObject.getAttribute("value");
         this.button.setText(textValue);
      }
   }

   /**
    * 样式修改后处理
    *
    * @param styleName
    *            样式名称
    * @param styleValue
    *            样式值
    * @param isInit
    *            是否在初始化时调用 false:非初始化调用,即通过JS修改,如果样式影响了布局,需要通知布局刷新
    */
   @Override
   public boolean cssChanged(String styleName, String styleValue, boolean isInit)
   {
      boolean isHandle = super.cssChanged(styleName, styleValue, isInit);
      if (isHandle)
      {
         return true;
      }
      FHCssTable styles = this.domObject.getStyles();
      if ("button-color".equals(styleName))
      {
         int fontColor = styles.getColor("button-color", Color.WHITE);
         this.button.setTextColor(fontColor);
         return true;

      }
      return false;
   }

   /**
    * 控件测量函数
    *
    */
   @Override
   public long measure(YogaNodeAPI yogaNodeAPI, float width, YogaMeasureMode widthMode, float height,
         YogaMeasureMode heightMode)
   {
        //更新属性
      updateAttribute();
        //更新样式
      updateStyle();

        //此为css设置的宽度
      float cssWidth = width;
      if (YogaConstants.isUndefined(width))
      {
         cssWidth = this.domObject.pageInstance.layoutSize.width;
      }

        //此为css设置的高度
      float cssHeight = height;
      if (YogaConstants.isUndefined(height))
      {
         cssHeight = 100;
      }

        //也可以通过outWidth和outHeight来指定控件的宽高
      float outWidth = 0;
      float outHeight = 0;
      this.button.setMaxWidth((int) cssWidth);
      this.button.measure((int) cssWidth, (int) cssHeight);
      if (widthMode == YogaMeasureMode.UNDEFINED)
      {
         outWidth = this.button.getMeasuredWidth();
      }
      else
      {
         outWidth = cssWidth;
      }

      if (heightMode == YogaMeasureMode.UNDEFINED)
      {
         outHeight = this.button.getMeasuredHeight();
      }
      else
      {
         outHeight = cssHeight;
      }
      return YogaMeasureOutput.make(outWidth, outHeight);

   }

   /**
    * 设置按钮内容
    */
   public void setButtonTitle()
   {
      this.button.setText("buttonTitle");
   }

   /**
    * 获取按钮内容
    * 
    * @return 返回按钮内容
    * 
    */
   public String getButtonTitle()
   {
      String title = this.button.getText().toString();
      return title;
   }
}


```

3：下面建立js类FHUIButtonComponent，新建一个java类FHUIButtonComponent，继承于com.fiberhome.sprite.sdk.component.ui.FHUIComponent,如代码所示：  

```
public class FHUIButtonComponent extends FHUIComponent
{
   public FHUIButtonComponent(FHJScriptInstance scriptInstance, FHDomObject domObj)
   {
      super(scriptInstance, domObj);
   }

   /**
    * js调用此方法设置按钮内容
    */
   @JavaScriptMethod(jsFunctionName = "setButtonTitle")
   public void setButtonTitle()
   {
      FHUIButton fhUiButton = (FHUIButton) this.domObject.view;
      fhUiButton.setButtonTitle();
   }

   /**
    * js调用此方法获取按钮内容
    * @return 按钮内容
     */
   @JavaScriptMethod(jsFunctionName = "getButtonTitle")
   public String getButtonTitle()
   {

      FHUIButton fhUiButton = (FHUIButton) this.domObject.view;
      return fhUiButton.getButtonTitle();
   }
}

```  

4：接着使用FHSDKEngine通过反射对插件进行注册，此操作可以在MainActivity的onCreate方法中进行。 

```java

FHSDKEngine.registerComponentByClassName("fh_button", "com.example.appplugin.fhbutton.FHUIButtonComponent");
try
{
   FHSDKEngine.registerView("fh_button", Class.forName("com.example.appplugin.fhbutton.FHUIButton"));
}
catch (ClassNotFoundException e)
{
}


```

5：编写测试用例测试代码: 

```xml

<fh_button id="close" value="关闭页面" />
<fh_button style="background-color:red;button-color:blue;width:100;height:100" value="css样式" />


```


<h2 id="cid_4">多例功能插件开发</h2>   


1：以FHAjaxDemoComponent开发为示例 

2：新建一个java类继承于FHMultitonComponent，如代码所示：  

```java
public class FHAjaxDemoComponent extends FHMultitonComponent
{
   private String url;
   public FHAjaxDemoComponent(FHJScriptInstance scriptInstance)
   {
      super(scriptInstance);
   }

   @JavaScriptMethod(jsFunctionName = "send")
   public String send(String[] params)
   {
      JSONObject json = getParamJson(params, 0);
      url = FHJsonUtil.getString(json, "url");
      if (url != null && url.length() > 0)
      {
         FHHttpRequest request = new FHHttpRequest(FHHttpRequest.Command_Ajax, url);
         request.scriptInstance = getScriptInstance();
         request.parseRequestOptions(json);
         int callFunId = getParamInt(params, 1, -1);
         int requestProgressFunId = getParamInt(params, 2, -1);
         int responseProgressFunId = getParamInt(params, 3, -1);
         request.setCallFunction_id(callFunId);
         request.setRequestProgressFunction_id(requestProgressFunId);
         request.setResponseProgressFunction_id(responseProgressFunId);
         return FHConnectManager.getInstance().excuteRequest(request);
      }
      return "";
   }
}

```

3：接着使用FHSDKEngine通过反射对插件进行注册，此操作可以在MainActivity的onCreate方法中进行。 

```java
FHSDKEngine.registerComponentByClassName("fhajax", "com.example.appplugin.fhajax.FHAjaxDemoComponent");

```

4：编写测试用例测试代码： 

```java

var Ajax = require("fhajax"); 
var ajax1 = new Ajax(); 
var ajax2 = new Ajax(); 
window.on("loaded", function() { 
    var close = document.getElement("close"); 
    close.on("click", function(e) { 
        closePage(); 
    }); 
 
    var test = document.getElement("test"); 
test.on("click", function(e) { 
	var json1 = {};
    json1.url = "http://int.dpool.sina.com.cn";
        var json2 = {};
    json2.url = "http://www.baidu.com";
        var result1 = ajax1.send(json1); 
        var result2 = ajax2.send(json2); 
    }); 
}); 
 

```

<h2 id="cid_5">单例功能插件开发</h2>   


1：以FHAsynCallbackDemoComponent开发为示例。  


2：新建一个java类继承于FHSingletonComponent,如代码所示：  


```java

public class FHAsynCallbackDemoComponent extends FHSingletonComponent implements IFHActivityResultListener
{
   public static int CAPTURE_IMAGE_REQUEST_CODE;

   private Uri fileUri;
   private String path;
   private String type;

   public FHAsynCallbackDemoComponent(FHJScriptInstance scriptInstance)
   {
      super(scriptInstance);
   }

   public final static String CameraCallBackFun = "fhCamera";

   /**
    * 启动拍照界面
    * 
    * @param args
    *            args[0]: jsonData：传入设置参数，json格式，定义如下：
    *            type：拍照模式，字符串枚举型，必选参数，【photo，video】，photo：拍照模式；video：摄像模式
    *            path：拍照/摄像生成文件路径（包含文件名），支持res: file:，字符串类型，可选项，若不设置则存储于系统路径中
    * 
    *            args[1]: callBackId：回调函数，回调参数为json格式，定义如下：
    *            code：拍照/摄像结果，数字【0,-1】 0：拍照/摄像成功；-1：用户取消； path：生成文件完整全路径，字符串类型；
    * 
    */
   @JavaScriptMethod(jsFunctionName = "start")
   public void start(String[] args)
   {
      if (args.length <= 0)
      {
         return;
      }
      JSONObject jsonData = getParamJson(args, 0);
      int callBackId = getParamInt(args, 1);

      if (callBackId != Integer.MIN_VALUE)
      {
         this.setFunction(CameraCallBackFun, callBackId);
      }

      type = FHJsonUtil.getString(jsonData, "type");
      path = FHJsonUtil.getString(jsonData, "path");

      if (type == null || !type.equals("camera"))
      {
         return;
      }

      // 相对路径转换成绝对路径
      if (path != null && path != "")
      {
         path = FHFileUtil.getFilePath(path, this.scriptInstance.pageInstance.mCurrentDirPath);
      }
      else
      {
         String storageState = Environment.getExternalStorageState();
         if (Environment.MEDIA_REMOVED.equals(storageState))
         {
            Toast.makeText(FHPageManager.getInstance().getActivePage().activity, "SD卡不存在", Toast.LENGTH_SHORT)
                  .show();
         }
         else
         {
            path = Environment.getExternalStorageDirectory().getPath();
         }
      }
      // 如果需要调用当前activity的onActivityResult方法，则需要通过实现IFHActivityResultListener接口注册onActivityResult业务码，并在handleActivityResult方法中进行处理
      CAPTURE_IMAGE_REQUEST_CODE = FHPageManager.getInstance()
            .registerActivityForResultResquestCodeOutside(FHAsynCallbackDemoComponent.this, 0);
      FHPageInstance activePage = FHPageManager.getInstance().getActivePage();
      Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
      // 创建一个文件来存储图片
      fileUri = Uri.fromFile(getOutputMediaFile(path));
      intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);
      path = FHFileUtil.getRealFilePath(activePage.activity, fileUri);
      activePage.activity.startActivityForResult(intent, CAPTURE_IMAGE_REQUEST_CODE);
   }

   /** 创建一个文件来存储图片或视频 */
   private static File getOutputMediaFile(String path)
   {
      File file = new File(path);
      if (path.endsWith(File.separator))
      {
         FHLog.d("创建单个文件" + path + "失败，目标文件不能为目录！");
         return null;
      }
      // 判断目标文件所在的目录是否存在
      if (!file.getParentFile().exists())
      {
         // 如果目标文件所在的目录不存在，则创建父目录
         FHLog.d("目标文件所在目录不存在，准备创建它！");
         if (!file.getParentFile().mkdirs())
         {
            FHLog.d("创建目标文件所在目录失败！");
            return null;
         }
      }
      return file;
   }

   /**
    * 处理拍照结果返回的结果
    * 
    * @param requestCode
    *            请求码
    * @param resultCode
    *            结果码
    * @param data
    *            照片的数据，如已经指定照片的uri则此值为null
    */
   @Override
   public void handleActivityResult(int requestCode, int resultCode, Intent data)
   {
      JSONObject jsonObject = FHJsonUtil.string2JsonObject("");
      // 处理camera拍照返回的结果
      if (requestCode == CAPTURE_IMAGE_REQUEST_CODE)
      {
         if (resultCode == Activity.RESULT_OK)
         {
            FHJsonUtil.putInt(jsonObject, "code", 0);
            FHJsonUtil.putString(jsonObject, "path", path);
            callBack(CameraCallBackFun, jsonObject);
         }
         else if (resultCode == Activity.RESULT_CANCELED)
         {
            // 用户取消拍照处理
            FHJsonUtil.putInt(jsonObject, "code", -1);
            FHJsonUtil.putString(jsonObject, "path", "");
            callBack(CameraCallBackFun, jsonObject);
         }
         else
         {
            // 拍照失败结果通知用户
            FHJsonUtil.putInt(jsonObject, "code", -2);
            FHJsonUtil.putString(jsonObject, "path", "");
            callBack(CameraCallBackFun, jsonObject);
         }
      }

   }
}

```


3：接着使用FHSDKEngine通过反射对插件进行注册，此操作可以在MainActivity的onCreate方法中进行 

```java
FHSDKEngine.registerComponentByClassName("fh_camera","com.example.appplugin.fhcamera.FHAsynCallbackDemoComponent");

```

4：主要的测试用例：

```java

var fhcamera = require("fh_camera"); 
window.on("loaded", function() { 
    var close = document.getElement("close"); 
    close.on("click", function(e) { 
        closePage(); 
    }); 
 
    var test = document.getElement("test"); 
    test.on("click", function(e) { 
      var json = {}; 
      json.type = "camera"; 
      json.path = "res:image/1.jpg"; 
      fhcamera.start(json,callback); 
    }); 
}); 
 
function callback(json) 
{ 
  alert(json.code + " " + json.path); 
} 

```

<h2 id="cid_6">上传EDN</h2>     

1：插件开发及自测完成后，生成aar文件，放置于文件夹中  

2：该文件夹中放置插件描述文件component.xml，属性定义如下表：

**name** 	uixml使用中require的字段
如：FHAsynCallbackDemoComponent插件，使用时用require("fhcamera")。对于UI插件name就为uixml中ui节点的tagName，以FHUIButtomComponent为示例，在uixml中就可以写&lt;fh-button value="123"/&gt;   

**jsClass**	jsComponent对应的类名称   

**class**	UI插件：视图控件对应的类名  
功能插件：同jsClass  

示例：

```xml
<component name="fhajax" class="FHAjaxDemoComponent" jsClass="FHAjaxDemoComponent"  />
```


3：将该文件夹压缩为zip格式文件，提交至EDN插件上传


<img src="image/android_1.png" />