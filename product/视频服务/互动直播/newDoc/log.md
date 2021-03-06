
## 日志位置

日志在开发控制台输出的同时，也会写入终端的存储中。开发者可以在这些地方找到日志：

### Android

SDK|目录
:--:|:--:
iLiveSDK|tencent/imsdklogs/包名/ilivesdk_YYYYMMDD.log
IMSDK|tencent/imsdklogs/包名/imsdk_YYYYMMDD.log
AVSDK|tencent/imsdklogs/包名/QAVSDK_YYYYMMDD.log



### ios

SDK|目录
:--:|:--:
iLiveSDK|Library/Caches/imsdk_YYYMMDD.log
IMSDK|Library/Caches/imsdk_YYYMMDD.log
AVSDK|Library/Caches/QAVSDK_YYYMMDD.log



## 日志收集接口

### 适用场景 

* 任何需要从手机上收集互动直播日志的场景


<img src="https://zhaoyang21cn.github.io/ilivesdk_help/readme_img/log_report.png" width = "360" height = "480" alt="图片名称" align=center />

### 使用方法

1. 集成iLive sdk
      
	**1.4.0以上**
	 	
2. 在需要用户上报日志的时候，调用日志上报接口。iLive sdk会直接把日志传到腾讯云后台。     

	
**android接口**	

```java
/**
 * 上报日志
 *
 * @param desc 描述
 * @param data 0表示当天 1-昨天 2-前天 类推
 * @param callBack 回调
 * 
 */
ILiveSDK.getInstance().uploadLog(String desc, int data ILiveCallBack callback);
```
**IOS接口**         

```java
/**
 日志上报
 
 @param logDesc      日志描述
 @param dayOffset    日期，0-当天，1-昨天，2-前天，以此类推
 @param uploadResult 上报回调
 */
(void)uploadLog:(NSString *)logDesc logDayOffset:(int)dayOffset uploadResult:(ILiveLogUploadResultBlock)uploadResult;
```

4. 开发者可以在[日志查询平台](http://vip.avc.qcloud.com/SdkLog/home)查询用户和下载用户上传的日志。


![](https://zhaoyang21cn.github.io/ilivesdk_help/readme_img/iLiveSDK_Log.png)


5. 当需要进一步分析问题的时候，可以提供日志id给腾讯云技术支持人员。

### 常见问题

* 都会上报哪些日志？  
     
    **iLiveSDK流程日志，IMSDK消息系统日志，AVSDK日志 后期会加上Crash信息日志**
    
* 如何指定上报哪天的日志？     

	**0表示当天 1-昨天 2-前天 类推**
	
* 上报过程中如果网络中断了会怎么样？      

	**需要重新传送**
	
* 上报的日志是否有删除策略？     

    **超过15天， 日志超过1000条自动清理** 
    
* 错误码 
		        
	0    ： 日志上报成功

	8101 ： 参数错误

	8102 ： 文件不存在

	8103 ： 压缩失败

	8104 ： 获取签名失败

	8105 ： 协议解析失败

	8106 ： 上传失败

	8107 ： 上报结果失败






## iLive SDK 关键路径的LOG

开发者在遇到问题时，可以根据这些日志，判断哪个流程执行出错，有助于定位问题。<br/><br/>
*在iLiveSDK 1.0.3以后版本过滤关键字Key_Procedure 会搜索到创建房间或加入房间的关键路径*

### Android

#### 创建房间流程正确LOG如下

![创建房间](https://mc.qcloudimg.com/static/img/587519130c2cb55ea0788def61b2e70d/rightProcess.png)

* 具体包括以下几个步骤


```C
1. 初始化步骤
ILiveSDK: Key_Procedure｜initSdk->init appid:1400001692, accountType:884    

2. 设置渲染层       
ILVBRoom: Key_Procedure|ILVB-Room|init root view    
AVVideoGroup: Key_Procedure|ILVB-AVVideoGroup|init sub views
    
3. iLive登录   
ILVBLogin: Key_Procedure｜ILVB-iLiveLogin strart |id:will     
ILVBLogin: Key_Procedure｜ILVB-iLiveLogin|login success    

4. 创建房间   
ILVBRoom: Key_Procedure|ILVB-Room|start create room:6357 enter with im:true|video:true   

5. 直播聊天室创建完毕
ILVBRoom: Key_Procedure|createRoom->im room ok:6357     

6. AV房间创建完毕
ILVBRoom: Key_Procedure|ILVB-Room|enter av room complete result: 0      

7. 打开摄像头
ILVBRoom: Key_Procedure|ILVB-Room|strart enableCamera     

8. server回调用户上线
ILVBRoom: Key_Procedure|ILVB-Room|onEndpointsUpdateInfo myself id has camera will     

9. 渲染
AVRootView: Key_Procedure|ILVB-AVRootView|renderVideoView->enter index:0|0,0,1080,1845       

10. 摄像头上报成功回调    
ILVBRoom: Key_Procedure|ILVB-Room|enable camera id:0/true     

```

#### 加入房间流程正确LOG如下

![加入房间](https://mc.qcloudimg.com/static/img/7932e829882bbd68e9c0a05af24a2e68/joinRoomProcess.png)

* 具体包括以下几个步骤


```C
1. 初始化 

ILiveSDK: Key_Procedure｜initSdk->init appid:1400001692, accountType:884   
  
2. 设置渲染层
ILVBRoom: Key_Procedure|ILVB-Room|init root view      
AVVideoGroup: Key_Procedure|ILVB-AVVideoGroup|init sub views   

3. iLive登录
ILVBLogin: Key_Procedure｜ILVB-iLiveLogin strart |id:will      
ILVBLogin: Key_Procedure｜ILVB-iLiveLogin|login success       

4. 加入房间  
ILVBRoom: Key_Procedure|joinRoom->id: 6352 isIMsupport: true       

5. 直播聊天室加入成功    
ILVBRoom: Key_Procedure|joinLiveRoom joinIMChatRoom callback succ    

6. AV房间加入成功       
ILVBRoom: Key_Procedure|ILVB-Room|enter av room complete result: 0        

7. 获取server 成员上线回调 
ILVBRoom: Key_Procedure|ILVB-Endpoint | requestRemoteVideo id [willguo]     

8. 渲染界面
AVRootView: Key_Procedure|ILVB-AVRootView|renderVideoView->enter index:0| 0,0,1080,1845      

```


### ios

#### 创建房间流程正确LOG如下

![创建房间](http://mc.qcloudimg.com/static/img/f1ee4994ede1dd89199361973a1cfbee/image.jpg)

* 具体包括以下几个步骤

```C
1. 初始化SDK
ILiveSDK:Key_Procedure|initSdk|succ

2. 登录SDK（托管模式，如果是独立模式，无tlsLogin的log）
ILiveLogin:Key_Procedure|tlsLogin|start:id:ken1
ILiveLogin:Key_Procedure|tlsLogin|succ
ILiveLogin:Key_Procedure|iLiveLogin|start:id:ken1
ILiveLogin:Key_Procedure|iLiveLogin|succ

3. 创建渲染根视图和主播渲染视图
ILiveOpenGL:Key_Procedure|createOpenGLView|succ
ILiveOpenGL:Key_Procedure|addRenderView|succ:frame:{{0, 0}, {375, 667}},key:ken1

4. 创建聊天群组
ILiveRoom:Key_Procedure|createIMGroup|start:groupId:9878
ILiveRoom:Key_Procedure|createIMGroup|succ

5. 创建直播房间
ILiveRoom:Key_Procedure|enterAVRoom|start:roomId:9878
ILiveRoom:Key_Procedure|enterAVRoom|succ

6. 打开摄像头并收到server事件（此处是摄像头开启事件）回调
ILiveRoom:Key_Procedure|enableCamera|start:enable:YES
ILiveRoom:Key_Procedure|OnEndpointsUpdateInfo|evenId:3,id:ken1
ILiveRoom:Key_Procedure|enableCamera|succ:enable:YES

7. 收到视频帧（间隔打印）
ILiveGLBase:Key_Procedure|videoFrame|id:ken1,index:1
ILiveGLBase:Key_Procedure|videoFrame|id:ken1,index:11
ILiveGLBase:Key_Procedure|videoFrame|id:ken1,index:21

8. 退出直播房间
ILiveRoom:Key_Procedure|exitAVRoom|start
ILiveRoom:Key_Procedure|exitAVRoom|succ

9 退出聊天群组（群主解散群组）
ILiveRoom:Key_Procedure|quitIMGroup|start:groupId:9878
ILiveRoom:Key_Procedure|quitIMGroup|succ:code:10009
ILiveRoom:Key_Procedure|deleteIMGroup|start:groupId:9878
ILiveRoom:Key_Procedure|deleteIMGroup|succ
```

#### 加入房间流程正确LOG如下

![加入房间](http://mc.qcloudimg.com/static/img/54b586d5fa373c5f1c359d624bf4557a/image.png)

* 具体包括以下几个步骤

```C
1. 初始化SDK
ILiveSDK:Key_Procedure|initSdk|succ

2. 登录SDK（托管模式，如果是独立模式，无tlsLogin的log）
ILiveLogin:Key_Procedure|tlsLogin|start:id:ken2
ILiveLogin:Key_Procedure|tlsLogin|succ
ILiveLogin:Key_Procedure|iLiveLogin|start:id:ken2
ILiveLogin:Key_Procedure|iLiveLogin|succ

3. 创建渲染根视图和主播渲染视图
ILiveOpenGL:Key_Procedure|createOpenGLView|succ
ILiveOpenGL:Key_Procedure|addRenderView|succ:frame:{{0, 0}, {414, 736}},key:ken1

4. 加入聊天群组
ILiveRoom:Key_Procedure|joinIMGroup|start:groupId:9878
ILiveRoom:Key_Procedure|joinIMGroup|succ

5. 进入直播房间
ILiveRoom:Key_Procedure|enterAVRoom|start:roomId:9878
ILiveRoom:Key_Procedure|enterAVRoom|succ

6. server事件（此处是摄像头开启事件）回调
ILiveRoom:Key_Procedure|OnEndpointsUpdateInfo|evenId:3,id:ken1

7. 收到主播视频帧（间隔打印）
ILiveGLBase:Key_Procedure|videoFrame|id:ken1,index:1
ILiveGLBase:Key_Procedure|videoFrame|id:ken1,index:11
ILiveGLBase:Key_Procedure|videoFrame|id:ken1,index:21

8. 退出直播房间
ILiveRoom:Key_Procedure|exitAVRoom|start
ILiveRoom:Key_Procedure|exitAVRoom|succ

9 退出聊天群组
ILiveRoom:Key_Procedure|quitIMGroup|start:groupId:9878
ILiveRoom:Key_Procedure|quitIMGroup|succ
```


