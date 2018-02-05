#  第三方程序交互

----------  

<h2 id="cid_0">第三方调用Sprite</h2>  


**Android**

第三方原生应用调Sprite传参，示例：

```java
Intent it = new Intent();
it.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
//基座包名，若EDN更换包名则取用实际打包包名称
it.setClassName("com.fiberhome.sprite.client","com.fiberhome.sprite.client.MainActivity");   
//需要传递参数 key-value对，value值不需要做base64编码
it.putExtra("name","admin");
it.putExtra("password","admin111");
it.putExtra("token","thirdapp");
startActivity(it);
```

Sprite接收参数：

通过app.json中homeJs配置应用启动入口，通过App类launch事件区分应用启动类型走不同流程跳转。
关于程序入口的讲解查看[https://gitdocument.exmobi.cn/sprite-api/ptgnjj.html](https://gitdocument.exmobi.cn/sprite-api/ptgnjj.html)

launch启动参数为Json对象，定义如下：

> type：启动类型，字符串枚举型，[normal,app]
> 
> - normal ：桌面启动
> 
> - app：第三方程序调用
> 
> data：传递参数，Android为Json对象

示例：

```javascript
//应用homeJs配置中启动
var app = require("App");
var window = require("Window");
app.on("launch",function(e,jsonData){
    var type = jsonData.type;
     if(type =="normal"){
        //正常桌面启动 无传递参数
        var data = {};
        data.url = "res:page/index.uixml";
        data.openAnimation="none";
        window.open(data);
     }else if(type =="app"){
        //其他应用调用启动 获取传参 Android传参为Json对象 key，value均为字符串类型
        var data = jsonData.data;
        //获取第三方应用传递name值
        var name = data.name;
        //获取第三方应用传递password值
        var password = data.password;
        //执行后续处理流程
     } 
 });
```

**IOS**  

第三方原生应用调Sprite传参：

 Sprite iOS基座支持urlscheme为sprite（根据EDN打包设置urlschme配置）的不同类型启动请求，格式如下：

> 应用的urlscheme://+自定义参数

其中urlscheme的值：默认sprite（根据EDN打包设置urlschme配置）

传递键值对：自定义，以 = 分割key和value，以&分割多个对

示例：

```
sprite://name=admin&password=admin111&token=thirdapp
```

Sprite接收参数:

通过app.json中homeJs配置应用启动入口，通过App类launch事件区分应用启动类型走不同流程跳转。
launch启动参数为Json对象，定义如下： 

> type：启动类型，字符串枚举型，[normal,app]
> 
> - normal ：桌面启动
> 
> - app：第三方程序调用
> 
> data：传递参数，iOS为字符串类型


```javascript
//应用homeJs配置中启动
var app = require("App");
var window = require("Window");
app.on("launch",function(e,jsonData){
    var type = jsonData.type;
     if(type =="normal"){
        //正常桌面启动 无传递参数
        var data = {};
        data.url = "res:page/index.uixml";
        data.openAnimation="none";
        window.open(data);
     }else if(type =="app"){
        //其他应用调用启动 获取传参 传参为字符串类型 如：sprite://name=admin&password=admin111&token=thirdapp
        var data = jsonData.data;
        //这里和android不一样，ios获取的是字符串，开发者自行解析data字符串执行后续处理流程
     } 
 });

```


<h2 id="cid_1">Sprite调用第三方</h2> 

根据不同应用提供的调用规则，[通过Native原生程序相关操作类openApp方法调用](https://gitdocument.exmobi.cn/sprite-api/native.html)。

**Android**  

示例：

```javascript
var native = require("Native");
var data = {};
//程序包名
data.appId = "com.fiberhome.gaea.client";
//程序入口Activity
data.activity = "com.fiberhome.gaea.client.WelcomActivity"
//程序传递参数
var params = {};
params.name="zhangsan";
params.password = "123456";
data.params = params;
native.openApp(data,callFunction);

//回调函数
function callFunction(jsonData){
    var code = jsonData.code;
    if(status == 0){
       //启动第三方应用成功
        
    }else if(status == -1){
       //启动第三方应用失败
       
    }
}

```

**IOS** 

示例：

```javascript
var native = require("Native ");
var data = {};
//urlscheme 启动高德地图 
data.appId = "iosamap://navi?sourceApplication=%@&backScheme=%@&lat=%f&lon=%f&dev=0&style=2";
app.openApp(data,callFunction);

//回调函数
function callFunction(jsonData){
    var code = jsonData.code;
    if(status == 0){
       //启动第三方应用成功
        
    }else if(status == -1){
       //启动第三方应用失败
       
    }
}
```

