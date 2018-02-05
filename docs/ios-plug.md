# IOS原生插件开发

----------

<h2 id="cid_0">概述</h2>  


IOS原生插件开发分为UI插件和功能插件，其中功能插件又分为单例插件和多例插件。  

单例插件继承于FHSingletonComponent。  

多例插件继承于FHMultitonComponent。  

UI插件继承于FHUIComponent，其中UI插件分为两个部分，一个是视图控件，继承于FHView，主要用于界面展示和用户交互部分；另外一个是JS对象，继承于FHUIComponent，主要用于js中的处理。  

<h2 id="cid_1">关于javascriptcore</h2>  

IOS采用系统javascriptcore作为js引擎，具有安装包体积小，原生桥接方便等特点

参考文档1： iOS7新JavaScriptCore框架入门介绍 http://www.cnblogs.com/ider/p/introduction-to-ios7-javascriptcore-framework.html

参考文档2 ：JavaScriptCore and iOS 7  https://www.bignerdranch.com/blog/javascriptcore-and-ios-7/


<h2 id="cid_2">环境搭建</h2>   


1：开发环境：Xcode8.3及以上；Mac系统：建议10.12及以上  

2：EDN论坛下载Sprite[  IOS原生开发工程](https://www.exmobi.cn/downloadRedirect.jsp?type=sprite_plugin_ios)，该工程与EDN打包工程相同，包含Sprite基础SDK Framework，基础SDK使用的第三方库，外置组件相关； 

3：运行该工程，若一切正常则可进入如下图所示Sprite界面  

<img src="image/ios_0.png"  width="250"/>
 

<h2 id="cid_3">插件开发</h2>   

**关于component.xml文件** 

 在工程中有一个component.xml,原生组件主要通过此文件反射进行组件扩展，每一个component节点对应一个原生组件，所以component.xml可以包括多个组件(UI组件，单例组件，多例组件)如图所示   

<img src="image/ios_1.png" />


> **component.name**      对于功能组件,name的配置就是uixml使用中require的字段，
>                         例如 FHAsynCallbackDemoComponent组件,使用时用require("fhcamera"); 
>                         对于UI组件name就为uixml中ui节点的tagName,以FHUIButtomComponent为例子,
>                         在uixml中就可以写<fhbutton value="123"/> 
> 
> **component.class**     对于UI组件，class配置就是视图控件对应的类名，对于功能组件，同jsClass一样，都是js类名 
> 
> **component.jsClass**   获取系统View  


**基础类说明** 

 Sprite已经提供了完善的插件开发及加载相关机制，开发者只需要继承插件基础类，重载关键方法，即可快速实现插件开发。  

其中FHUIComponent，FHMultitonComponent，FHSingletomComponent均继承于FHComponent，组件开发时类相关方法说明如下。

**FHUIView 的方法 ：**  

> **-(instancetype)initWithPage:(FHPageInstance*)pageInstance**   初始化函数  
> 
> **-(void)willResignActive**	即将切换到后台   
> 
> **-(void)didEnterBackground**    进入后台  
> 
> **-(void)willEnterForeground** 	即将切换到前台   
> 
> **-(void)didBecomeActive** 	进入前台 
> 
> **-(void)viewWillAppear** 	    ViewController viewWillAppear事件
> 
> **-(void)viewWillDisappear** 	ViewController viewWillDisappear事件   
> 
> **-(void)onPageDestroy**	    页面即将销毁时调用,此后会调用destroy方法  
> 
> **-(void)destroy** 	页面销毁时调用     
> 
> **+(void)onEngineInitEvent:(NSDictionary*)dic** 	引擎初始化通知，主要用于百度地图等初始化时调用  
> 
> **+(BOOL)application:(UIApplication *)application openURL:(NSURL *)url**
> **sourceApplication:(NSString *)sourceApplication annotation:(id)annotation**	处理回调（浏览器打开程序、第三方app回调等） 
> 
> **+(void)application:(UIApplication *)application** 
> **didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken**	Push获取deviceToken  
> 
> **+(void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo**
> **appInstance:(FHAppInstance*)appInstance**  	Ios8-9获取Push   
> 
> **+(void)userNotificationCenter:(UNUserNotificationCenter*)center didReceiveNotificationResponse:(UNNotificationResponse*)**
> **response appInstance:(FHAppInstance*)appInstance**	ios10 程序在后台运行时收到的Push消息   
> 
> **+(void)userNotificationCenter:(UNUserNotificationCenter*)center willPresentNotification:(UNNotification*)notification**
> **appInstance:(FHAppInstance*)appInstance**	ios10 程序在前台运行时收到的Push消息   
> 
> **-(void)remoteControlReceivedWithEvent:(UIEvent*)receivedEvent**	 远程事件通知回调   
> 
> **-(BOOL)isOnEvent:(NSString*)eventName**	判断是否监听了某一事件  
> 
> **-(JSValue*)fire:(NSString*)eventName target:(id)target thisObj:(id)thisObj paramArray:(NSArray*)paramArray**	组件fire事件  
> 
> **-(NSArray*)getOnEvents:(NSString*)eventName** 	获取监听事件的functions数组(JSValue) 
> 

<h2 id="cid_4">UI插件开发</h2> 


开发过程说明：

#### 1. 建立工程。
基于官方提供的插件工程包，包含了sprite核心框架framework，在此工程基础上进行开发。

#### 2. 下面以UI组件(fhbutton)开发为例子
新建一个OC类FHUIButton继承于FHView,如图所示 

<img src="image/ios_2.png" />

<img src="image/ios_3.png" />

#### 相关函数说明

>**-(void)loadComplete**	页面加载结束时会调用此函数，但是此时页面还没有Push到navigationcontroller中
>
>**-(void)openPageAnimationFinished**	页面Push到navigationcontroller中,并且开窗动画完毕以后的回调
>
>**-(void)viewDidAppear**	ViewController viewDidAppear事件
>
>**-(void)viewWillAppear**	ViewController viewWillAppear事件
>
>**-(void)viewDidDisappear**	ViewController viewDidDisappear事件
>
>**-(void)viewWillDisappear**	ViewController viewWillDisappear事件
>
>**-(void)didBecomeActive**	进入前台回调
>
>**-(void)willEnterForeground**	即将进入前台
>
>**-(void)willResignActive**	即将进入后台
>
>**-(void)resizeCompleted**	横竖屏切换完成
>
>**-(void)onPagePause**	页面处于非激活时调用
>
>**-(void)onPageResume**	页面处于激活时调用
>
>**-(void)onPageDestroy**	页面即将销毁时调用,此后会调用destroy方法
>
>**-(void)destroy** 	页面销毁时调用
>
>**-(void)setLayoutFrame:(CGRect)rect** 	当组件大小改变时会调用
>
>**-(BOOL)cssChanged:(NSString *)cssKey cssValue:(NSString *)cssValue isInit:(BOOL)isInit**
>	当Css改变时会调用，cssKey:css的名称,cssValue:css的值,isInit为true的时候表示是xml中设置的样式，为false时表示是通过js方式设置的样式，注>   意当cssValue为nil的时候，表示清除了控件的样式
>
>**-(void)attributeChanged:(NSString *)name value:(NSString *)value isInit:(BOOL)isInit**	当属性改变时会调用，name:属性的名称,
>   value:属性的值,isInit为true的时候表示是xml中设置的属性，为false时表示是通过js方式设置的属性，
>   注意当value为nil的时候，表示清除了控件的属性
>
>**-(instancetype)initWithDom:(FHDomObject *)dom parent:(FHUIComponent *)parentDom page:(FHPageInstance *)pageInstance**初始化方法
>
>**-(CGSize)measureSize:(CGSize)size rootViewSize:(CGSize)rootViewSize**	控件测量函数，默认返回值是
>   CGSize(FHCSSUndefined,FHCSSUndefined) size为css的宽高，如果宽高都没写,size为(FHCSSUndefined,FHCSSUndefined) 
>   如果只写了宽度width:100 则size(100,FHCSSUndefined) 如果只写了宽度height:100 则size(FHCSSUndefined,100) 
>   如果只写了宽度width:100;height50 则size(100,50) rootViewSize 为页面个宽度高度 
>   这里返回的值不一定是控件的真实宽高，真实的宽高还要根据css的设置还决定，毕竟css的优先级最高比如(width,height,flex), 
>   例如css写了width:100; 这里的函数返回CGSize(500,100),最终实际宽度还是按照css的100来设置的
>
>**-(void)remoteControlReceivedWithEvent:(UIEvent*)receivedEvent**	远程事件通知回调 
>


```java
// 
//  FHUIButton.m 
//  Sprite 
// 
//  Created by wjr on 16/12/20. 
//  Copyright © 2016年 Fiberhome. All rights reserved. 
// 
 
#import "FHUIButton.h" 
 
@implementation FHUIButton 
 
-(instancetype)initWithDom:(FHDomObject *)dom parent:(FHUIComponent *)parentDom page:(FHPageInstance *)pageInstance 
{ 
    //根据虚拟Dom初始化UI视图组件，dom中包括属性，css样式，父子节点等信息 
    self = [super initWithDom:dom parent:parentDom page:pageInstance]; 
    //初始化控件(UIButton),这里可以做一些初始化操作 
    _button = [[UIButton alloc] initWithFrame:CGRectMake(0, 0, 0, 0)]; 
    _button.backgroundColor = [FHCssUtil parseColor:@"#88D038" defaultValue:COLOR_TRANSPARENT]; 
    //把UIButton视图赋值给sysView,sysView是这个组件的根视图，外部可能拿这个根视图做一些操作，这步很重要 
    self.sysView = _button; 
    return self; 
} 
-(void)attributeChanged:(NSString *)name value:(NSString *)value isInit:(BOOL)isInit 
{ 
    //当属性发生改变时调用，name为属性名，value为属性值,isInit代表是否为初始化，isInit为TRUE代表uixml中写的属性 
    //isInit为FALSE代表js代码设置属性 
    //这里以属性value的值作为button的Title 
    if ([name isEqualToString:@"value"]) 
    { 
        //根据属性value的值设置button的title 
        [_button setTitle:value forState:UIControlStateNormal]; 
    } 
} 
-(BOOL)cssChanged:(NSString *)cssKey cssValue:(NSString *)cssValue isInit:(BOOL)isInit 
{ 
    //当Css发生改变时调用，cssKey为样式名称，cssValue为样式值,isInit代表是否为初始化，isInit为TRUE代表uixml中写的样式 
    //isInit为FALSE代表js代码设置样式 
     
    //先判断下是否是通用属性(如backgroung-color,width,margin等等) 
    BOOL res = [super cssChanged:cssKey cssValue:cssValue isInit:isInit]; 
    if (res) 
        return res; 
     
    //如果不是，则需要自己处理 
    if ([cssKey isEqualToString:@"button-color"]) 
    { 
        //这里以自定义的button-color做为button的title颜色 
        UIColor* color = [FHCssUtil parseColor:cssValue defaultValue:[UIColor redColor]]; 
        [_button setTitleColor:color forState:UIControlStateNormal]; 
        return TRUE; 
    } 
    return FALSE; 
} 
 
-(void)setLayoutFrame:(CGRect)rect 
{ 
    //当宽度和高度改变时，会调用此函数 
} 
 
-(void)onPageDestroy 
{ 
    [super onPageDestroy]; 
    //当页面即将关闭时，会调用此函数，在此函数中做一些预销毁的操作，如停止动画，停止定时器等等 
} 
-(void)destroy 
{ 
    [super destroy]; 
    //当页面销毁时调用，这里需要处理销毁动作 
} 
 
-(void)loadComplete 
{ 
    //加载完毕时，会调用此函数 
    [super loadComplete]; 
     
    //fire自定义事件,ary可以为nil 
    NSArray* ary = [[NSArray alloc] initWithObjects:@"参数1",@"参数2", nil]; 
    [self fireEvent:@"fhbuttonloaded" withArray:ary]; 
     
    //注册点击事件 
    [_button addTarget:self action:@selector(click:) forControlEvents:UIControlEventTouchUpInside]; 
} 
 
-(void)openPageAnimationFinished 
{ 
    //页面开窗动画结束回调 
} 
 
-(void)viewDidAppear 
{ 
    //ViewController viewDidAppear事件 
} 
 
-(void)viewWillAppear 
{ 
    //ViewController viewWillAppear事件 
} 
 
-(void)viewDidDisappear 
{ 
    //ViewController viewDidDisappear事件 
} 
-(void)viewWillDisappear 
{ 
    //ViewController viewWillDisappear事件 
} 
 
-(void)didBecomeActive 
{ 
    //进入前台回调 
} 
-(void)willEnterForeground 
{ 
    //即将进入前台 
} 
-(void)willResignActive 
{ 
    //即将进入后台 
} 
-(void)didEnterBackground 
{ 
    //进入后台 
} 
-(void)resizeCompleted 
{ 
    //横竖屏切换完成 
} 
-(void)onPagePause 
{ 
    //页面处于非激活时调用 
} 
-(void)onPageResume 
{ 
    //页面处于激活时调用 
} 
-(void)remoteControlReceivedWithEvent:(UIEvent*)receivedEvent 
{ 
    //远程事件通知回调 
} 
-(void)click:(id)sender 
{ 
    //fire click事件 
    [self fireEvent:@"click" withArray:nil]; 
} 
 
-(CGSize)measureSize:(CGSize)size rootViewSize:(CGSize)rootViewSize 
{ 
    //控件测量函数，默认反正值是CGSize(FHCSSUndefined,FHCSSUndefined) 
    //size为css的宽高，如果宽高都没写,size为(FHCSSUndefined,FHCSSUndefined) 如果只写了宽度width:100 则size(100,FHCSSUndefined) 
    //如果只写了宽度height:100 则size(FHCSSUndefined,100) 如果只写了宽度width:100;height50 则size(100,50) 
    //rootViewSize 为页面个宽度高度 
    //这里返回的值不一定是控件的真实宽高，真实的宽高还要根据css的设置还决定，毕竟css的优先级最高比如(width,height,flex), 
    //例如css写了width:100; 这里的函数返回CGSize(500,100),最终实际宽度还是按照css的100来设置的 
    return CGSizeMake(160, 30); 
} 
-(void)setButtonTitle:(NSString*)title 
{ 
    [_button setTitle:title forState:UIControlStateNormal]; 
 
} 
-(NSString*)getButtonTitle 
{ 
    return _button.titleLabel.text; 
} 
@end 


```

####  3.下面建立js类FHUIButtonComponent，新建一个oc类FHUIButtonComponent，继承于FHUIComponent,如图所示 

<img src="image/ios_5.png" />

<img src="image/ios_6.png" />

#### 4.配置工程，找到工程中的component.xml,增加<component name="fhbutton" class="FHUIButton" jsClass="FHUIButtomComponent" /> 
让程序反射扩展UI组件

#### 5.UIXML中使用插件

```java    

<fhbutton id="close" value="关闭页面" /> 
<fhbutton style="background-color:red;button-color:blue;width:100;height:100" value="css样式" />

```

#### 6.手机效果

   <img src="image/ios_7.png"  width="250"/>


<h2 id="cid_5">多例组件开发</h2> 

#### 1.以FHAjaxDemoComponent开发为例子

#### 2.新建一个oc类继承于FHMultitonComponent，如图所示 

<img src="image/ios_8.png"/>

<img src="image/ios_9.png"/>

代码示例：


```java  
// 
//  FHAjaxComponent.m 
//  Sprite 
// 
//  Created by wjr on 2016/12/26. 
//  Copyright © 2016年 Fiberhome. All rights reserved. 
// 
 
#import "FHAjaxDemoComponent.h" 
#import <AFNetworking/AFNetworking.h> 
#import <SpriteFramework/FHJsonUtil.h> 
#import <SpriteFramework/FHBackGourndEventHandler.h> 
@implementation FHAjaxDemoComponent 
{ 
    NSString* _url; 
} 
 
-(void)send:(JSValue*)callback 
{ 
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager]; 
    //不设置会报-1016或者会有编码问题 
    manager.responseSerializer = [AFHTTPResponseSerializer serializer]; 
 
    //发送请求 
    [manager GET:_url parameters:nil progress:^(NSProgress * _Nonnull downloadProgress) { 
         
    } 
         success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) { 
              
             NSData* data = responseObject; 
             NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]; 
             NSDictionary* dic = [FHJsonUtil stringToJson:content]; 
              
             NSString* text = [NSString stringWithFormat:@"%@ %@ %@ %@",dic[@"country"],dic[@"province"],dic[@"city"],dic[@"district"]]; 
              
             FHPerformBlockOnMainThread(^{ 
                 //对于javascriptcode NSDictionary对应json 
                 NSDictionary* dic = [[NSDictionary alloc] initWithObjectsAndKeys:@(0),@"code",text,@"text", nil]; 
                 NSArray* ary = [[NSArray alloc] initWithObjects:dic, nil]; 
                 //调用JS异步回调，只能在主线程中调用 
                 [callback callWithArguments:ary]; 
                 ary = nil; 
                 dic = nil; 
             }); 
         } 
         failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull   error) { 
              
             FHPerformBlockOnMainThread(^{ 
                 //对于javascriptcode NSDictionary对应json 
                 NSDictionary* dic = [[NSDictionary alloc] initWithObjectsAndKeys:@(-1),@"code",[error localizedDescription],@"text", nil]; 
                 NSArray* ary = [[NSArray alloc] initWithObjects:dic, nil]; 
                 //调用JS异步回调，只能在主线程中调用 
                 [callback callWithArguments:ary]; 
                 ary = nil; 
                 dic = nil; 
             }); 
              
         }]; 
} 
 
-(void)setUrl:(NSString *)url 
{ 
    //js设置url的事件 
    _url = url; 
} 
-(NSString*)url 
{ 
     //js获取url的事件 
    return _url; 
} 
 
 
@end 


```

#### 3.配置工程，找到工程中的component.xml,
增加如下代码，让程序反射扩展UI组件

```java
<component name="fhajax" class="FHAjaxDemoComponent" jsClass="FHAjaxDemoComponent"/> 

```


#### 4.UIXML前端界面中使用:

```js
var Ajax = require("fhajax"); 
var ajax1 = new Ajax(); 
var ajax2 = new Ajax(); 
ajax1.url = "http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=218.94.117.236"; 
ajax2.url = "http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=60.161.76.34"; 
 
window.on("loaded", function() { 
    var close = document.getElement("close"); 
    close.on("click", function(e) { 
        closePage(); 
    }); 
 
    var test = document.getElement("test"); 
    test.on("click", function(e) { 
        ajax1.send(callback); 
        ajax2.send(callback); 
    }); 
}); 
 
function callback(json) 
{ 
    alert(json.code + " " + json.text); 
} 

```


<h2 id="cid_6">单例组件开发</h2> 

#### 1.以FHAsynCallbackDemoComponent开发为例子

#### 2.新建一个oc类继承于FHSingletonComponent,如图所示

<img src="image/ios_10.png"/>

#### 3.代码示例

 ```java
// 
//  FHCameraComponent.m 
//  Sprite 
// 
//  Created by dyc on 16/11/8. 
//  Copyright © 2016年 Fiberhome. All rights reserved. 
// 
 
#import "FHAsynCallbackDemoComponent.h" 
#import <MobileCoreServices/UTCoreTypes.h> 
#import <UIKit/UIImage.h> 
#import <SpriteFramework/FHFileUtil.h> 
#import <SpriteFramework/FHDeviceUtil.h> 
#import <SpriteFramework/UIImage+Sprite.h> 
#import <SpriteFramework/FHPageInstance.h> 
 
 
@interface FHAsynCallbackDemoComponent() 
{ 
    JSValue* callbackCamera; //回调方法 
    NSDictionary* jsonData;//camera参数设置 
} 
 
@end 
 
@implementation FHAsynCallbackDemoComponent 
 
#pragma mark - 多态方法（覆盖父类中方法的） 
-(instancetype)initWithPage:(FHPageInstance*)pageInstance; 
{ 
    self = [super initWithPage: pageInstance]; 
    if(self) 
    { 
        callbackCamera = nil; 
        jsonData = nil; 
    } 
    return self; 
} 
 
//多态方法（覆盖父类中方法的） 
//如果需要，可在此处处理资源释放等操作 
-(void)destroy 
{ 
     
} 
 
#pragma mark - 接口方法 
-(void)start:(JSValue*)json value:(JSValue*)callbackFunc 
{ 
    if(![FHDeviceUtil isDeviceSupportCamera]) 
        return; 
 
    if(nil == json || !json.isObject || nil == callbackFunc || !callbackFunc.isObject) 
        return; 
 
    NSDictionary* info = [json toDictionary]; 
     
    jsonData = [[NSDictionary alloc] initWithDictionary: info]; 
    callbackCamera = callbackFunc; 
     
    NSString* jsonType = jsonData[@"type"]; 
    if (jsonType == nil) 
        return; 
     
    if (![jsonType isEqualToString:@"camera"]) 
        return; 
 
    //处理参数并启动相机 
    UIImagePickerControllerSourceType type = UIImagePickerControllerSourceTypeCamera; 
 
    if(![UIImagePickerController isSourceTypeAvailable: type]) 
    { 
        return ; 
    } 
 
    UIImagePickerController* picker  =  [[UIImagePickerController alloc] init]; 
    picker.sourceType = type; 
    picker.delegate    = self; 
    //摄像头拍照片 
    picker.mediaTypes = [NSArray arrayWithObject:(NSString*)kUTTypeImage]; 
         
    if(nil != picker) 
    { 
        if([[[UIDevice currentDevice] systemVersion] floatValue]>=8.0) { 
             
            picker.modalPresentationStyle=UIModalPresentationOverCurrentContext; 
             
        } 
        //通过页面所在的viewcontroller来弹出拍照界面 
        [self.pageInstance.viewController presentViewController: picker animated:YES completion:^{ 
             
        }]; 
    } 
} 
 
#pragma mark - 内部方法 
-(void)doCallback:(NSDictionary*)data 
{ 
    if (callbackCamera && callbackCamera.isObject) 
    { 
        NSArray* ary = [[NSArray alloc] initWithObjects: data, nil]; 
        [callbackCamera callWithArguments: ary]; 
    } 
} 
 
#pragma mark - UIImagePickerControllerDelegate 
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info 
{ 
    [picker dismissViewControllerAnimated:YES completion:^{ 
       
        NSString * path = nil; 
        id value = [jsonData objectForKey: @"path"]; 
        if(value) 
        { 
            path = [[NSString alloc] initWithFormat: @"%@", value]; 
        } 
        //转换文件路径 
        //传来的路径往往是res:开头（相对路径） 
        NSString* pathDest = [FHFileUtil getFilePath: path]; 
 
        NSLog(@"info . %@", info); 
         
        BOOL bRet = NO; 
        //photo 
        { 
            UIImage* image = [info valueForKey:UIImagePickerControllerOriginalImage]; 
 
            //读取图片数据 
            //非png图片 
            NSData* data = UIImageJPEGRepresentation(image, 1); 
             
            //将图片写入目录 
            bRet = [FHFileUtil writeNSDataFile: pathDest content: data isAppend: NO]; 
        } 
         
        //回调参数 
        NSMutableDictionary* dic = [[NSMutableDictionary alloc] init]; 
        [dic setObject: [NSNumber numberWithInt: bRet ? 0 : -1] forKey: @"code"]; 
        if(bRet) 
        { 
            [dic setObject: pathDest forKey: @"path"]; 
        } 
        //回调 
        [self doCallback: dic]; 
 
    }]; 
} 
 
- (void)imagePickerControllerDidCancel:(UIImagePickerController *)picker 
{ 
    [picker dismissViewControllerAnimated:YES completion:^{ 
        //回调参数 
        NSMutableDictionary* dic = [[NSMutableDictionary alloc] init]; 
        [dic setObject: [NSNumber numberWithInt: -1] forKey: @"code"]; 
         
        //回调 
        [self doCallback: dic]; 
    }]; 
} 
 
@end 

```

#### 4.配置工程

找到工程中的component.xml,增加如下代码，让程序反射扩展UI组件

``` js
<component name="fhcamera" class="FHAsynCallbackDemoComponent" jsClass="FHAsynCallbackDemoComponent"/>

```

#### 5.UIXML界面中使用

```js
 
var fhcamera = require("fhcamera"); 
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


<h2 id="cid_7">上传EDN</h2> 

1：插件开发及自测完成后，把源码或者生成.a文件，图片资源、framework等放置到文件夹中；

2：该文件夹中放置插件描述文件component.xml，属性定义如下表：

>**name** uixml使用中require的字段 如：FHAsynCallbackDemoComponent插件，使用时用require("fhcamera")。
对于UI插件name就为uixml中ui节点的tagName，以FHUIButtomComponent为示例，在uixml中就可以写

```jsp
<fh-button value="123"/>
```

>**jsClass** jsComponent对应的类名称
class UI插件：视图控件对应的类名
功能插件：同jsClass

<img src="image/android_1.png"/>

