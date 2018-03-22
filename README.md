## 课堂录制协议

admin类型接口请求URL（只有管理员才有权限调用的接口）
https://console.tim.qq.com/v4/ilvb_edu/record?sdkappid=140001533&identifier=userid&usersig=usersigstr&contenttype=json

user类型接口请求URL（普通用户有权限调用的接口）
https://yun.tim.qq.com/v4/ilvb_edu/record?sdkappid=140001533&identifier=userid&usersig=usersigstr&contenttype=json

|参数名|描述|
|--|--|
|sdkappid|互动直播sdkappid|
|identifier|互动直播登录账号|
|usersig|互动直播登录账号的鉴权sig|
|contenttye|填写固定值contenttype=json|


### 1. 创建课堂(admin)

新东方后台创建ReplayGroup后，通知录制后台创建课堂的信息。

* request字段示例

```json
{
    "cmd":"open_record_svc",
    "subcmd":"create_class",
    "class_confid":1000,
    "class_roomid":1000,
    "class_starttime":1521685108,
    "class_groupid":"#TGS@6666",
    "record_userid":"recorder_001",
    "record_usersig":"******",
    "record_roomid":2000
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
class_confid|Int|必填|课堂id（作为索引，全局唯一）
class_roomid|Int|必填|老师客户端使用的roomid
class_starttime|Int|必填|课堂的开始时间
class_groupid|String|必填|存储白板消息的群组的id
record_userid|String|必填|分配给录制程序的用户id（云通信管理员帐号）
record_usersig|String|必填|登录record_userid的usersig
record_roomid|Int|必填|分配给录制程序的roomid

* response字段示例

```json
{
    "error_code":0,
    "error_msg":"",
    "taskid":"MTQwMDAwMTUzM18xMDAwXzE1MjE2ODUxMDg="
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
taskid|String|必填|用于查询课堂录制结果

taskid生成规则：
规则：base64(sdkappid_class_confid_record_roomid_class_starttime_curtime)
示例：base64(1400027849_1000_2000_1521685108_1521705435)
结果：MTQwMDAyNzg0OV8xMDAwXzIwMDBfMTUyMTY4NTEwOF8xNTIxNzA1NDM1

### 2. 结束课堂(admin)

当结束课堂时，新东方后台通知录制后台课堂结束事件。

* request字段示例

```json
{
    "cmd":"open_record_svc",
    "subcmd":"destroy_class",
    "class_confid":1000,
    "class_endtime":1521685108,
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
class_confid|Int|必填|课堂id（作为索引，全局唯一）
class_endtime|Int|必填|课堂的结束时间

* response字段示例

```json
{
    "error_code":0,
    "error_msg":""
}
```

### 3. 录制结果回调(admin)

课堂录制后台完成录制任务后，通知新东方服务器录制结果。

* request字段示例

```json
{
    "error_code":0,
    "error_msg":"",
    "class_confid":1000,
    "class_starttime":1521685108,
    "class_endtime":1521685108,
    "taskid":"MTQwMDAwMTUzM18xMDAwXzE1MjE2ODUxMDg=",
    "results":[
        {
            "appid": 1253488539,
            "channel_id": "8525_1a8c72af7a96ee1e5effd111d402e945",
            "duration": 3,
            "end_time": 1521686424,
            "event_type": 100,
            "file_format": "mp4",
            "file_id": "7447398155133859147",
            "file_size": 380132,
            "media_start_time": 1267997317,
            "record_file_id": "7447398155133859147",
            "sign": "d1c3442a26f9c9c41123a781146f0289",
            "start_time": 1521686422,
            "stream_id": "8525_1a8c72af7a96ee1e5effd111d402e945",
            "stream_param": "txSecret=c9440f14384ea57c9e06f5d89f3905ef&txTime=6d3d2f91&from=interactive&client_business_type=0&sdkappid=1400042982&sdkapptype=1&groupid=14506&userid=QW5kcm9pZDE1MTk4MDM3OTU4OTE1&ts=5ab31791&cliRecoId=0",
            "t": 1521687024,
            "task_id": "870366013738787153",
            "video_id": "200059203_45dda4a4a7c04989a1ba88785399fea2",
            "video_url": "http:\/\/1253488539.vod2.myqcloud.com\/2e50eecfvodgzp1253488539\/223b3ad37447398155133859147\/f0.mp4"
        }
    ]
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
error_code|Int|必填|录制结果（0 成功；非0 失败）
error_msg|String|必填|错误描述
class_confid|Int|必填|课堂id（作为索引，全局唯一）
class_starttime|Int|必填|课堂的开始时间
class_endtime|Int|必填|课堂的结束时间
taskid|String|必填|用于查询课堂录制结果
results|Array|必填|返回课堂录制的信息（格式同音视频回调内容一致）

* response字段示例

```json
{
    "error_code":0,
    "error_msg":""
}
```

### 4. 转发音视频服务录制回调数据(admin)

音视频后台服务器回调录制事件到新东方服务器后，新东方服务器需要转发一份数据拷贝到课堂录制服务器。

* request字段示例

```json
{
    "cmd":"open_record_svc",
    "subcmd":"video_callback",
    "content":{
        "appid": 1253488539,
        "channel_id": "8525_1a8c72af7a96ee1e5effd111d402e945",
        "duration": 3,
        "end_time": 1521686424,
        "event_type": 100,
        "file_format": "mp4",
        "file_id": "7447398155133859147",
        "file_size": 380132,
        "media_start_time": 1267997317,
        "record_file_id": "7447398155133859147",
        "sign": "d1c3442a26f9c9c41123a781146f0289",
        "start_time": 1521686422,
        "stream_id": "8525_1a8c72af7a96ee1e5effd111d402e945",
        "stream_param": "txSecret=c9440f14384ea57c9e06f5d89f3905ef&txTime=6d3d2f91&from=interactive&client_business_type=0&sdkappid=1400042982&sdkapptype=1&groupid=14506&userid=QW5kcm9pZDE1MTk4MDM3OTU4OTE1&ts=5ab31791&cliRecoId=0",
        "t": 1521687024,
        "task_id": "870366013738787153",
        "video_id": "200059203_45dda4a4a7c04989a1ba88785399fea2",
        "video_url": "http:\/\/1253488539.vod2.myqcloud.com\/2e50eecfvodgzp1253488539\/223b3ad37447398155133859147\/f0.mp4"
    }
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
sdkappid|Int|必填|互动直播sdkappid
content|Json|必填|音视频回调的内容

* response字段示例

```json
{
    "error_code":0,
    "error_msg":"",
}
```

### 5. 查询课堂录制结果(admin)

新东方后台服务器可以通过参数 `taskid` 查询课堂录制的结果。

* request字段示例

```json
{
    "cmd":"open_record_svc",
    "subcmd":"query_record_result",
    "taskid":"MTQwMDAwMTUzM18xMDAwXzE1MjE2ODUxMDg="
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
taskid|String|必填|用于查询课堂录制结果

* request字段示例

```json
{
    "error_code":0,
    "error_msg":"",
    "class_confid":1000,
    "class_starttime":1521685108,
    "class_endtime":1521685108,
    "taskid":"MTQwMDAwMTUzM18xMDAwXzE1MjE2ODUxMDg=",
    "results":[
        {
            "appid": 1253488539,
            "channel_id": "8525_1a8c72af7a96ee1e5effd111d402e945",
            "duration": 3,
            "end_time": 1521686424,
            "event_type": 100,
            "file_format": "mp4",
            "file_id": "7447398155133859147",
            "file_size": 380132,
            "media_start_time": 1267997317,
            "record_file_id": "7447398155133859147",
            "sign": "d1c3442a26f9c9c41123a781146f0289",
            "start_time": 1521686422,
            "stream_id": "8525_1a8c72af7a96ee1e5effd111d402e945",
            "stream_param": "txSecret=c9440f14384ea57c9e06f5d89f3905ef&txTime=6d3d2f91&from=interactive&client_business_type=0&sdkappid=1400042982&sdkapptype=1&groupid=14506&userid=QW5kcm9pZDE1MTk4MDM3OTU4OTE1&ts=5ab31791&cliRecoId=0",
            "t": 1521687024,
            "task_id": "870366013738787153",
            "video_id": "200059203_45dda4a4a7c04989a1ba88785399fea2",
            "video_url": "http:\/\/1253488539.vod2.myqcloud.com\/2e50eecfvodgzp1253488539\/223b3ad37447398155133859147\/f0.mp4"
        }
    ]
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
class_confid|Int|必填|课堂id（作为索引，全局唯一）
class_starttime|Int|必填|课堂的开始时间
class_endtime|Int|必填|课堂的结束时间
taskid|String|必填|用于查询课堂录制结果
results|Array|必填|返回课堂录制的信息（格式同音视频回调内容一致）

### 6. 互动用户上报时间线(user)

当课堂中老师或者学生需要操作白板或者打开摄像头互动（包括屏幕共享、播片）时，需要往录制后台上报本地的时间线信息。如果上报失败，则无法进行正常录制。

* request字段示例

```json
{
    "cmd":"open_record_svc",
    "subcmd":"report_time",
    "class_confid":1000,
    "userid":"teacher_001",
    "localtime":1267997317
}
```

字段  | 类型  | 选项 | 说明
:-----: | :-----: | :-----: | :-----: 
class_confid|Int|必填|课堂id（作为索引，全局唯一）
userid|String|必填|客户端用户id
localtime|Int|必填|客户端本地时间线

* response字段示例

```json
{
    "error_code":0,
    "error_msg":"",
}
```

* 时间线的计算方法请参考以下示例

```js
//web
var time = parseInt(+ new Date() / 1000, 10);
var localtime = time * Math.pow(2, 15) + (this.gSeq++);
```

```c++
//pc
struct timeval recvtv;
gettimeofday(&recvtv,NULL);
unsigned int localtime = (unsigned int)(recvtv.tv_sec*1000 + (uint64)recvtv.tv_usec/1000);
```

```objc
//ios
#import <mach/mach_time.h>

mach_timebase_info_data_t info = {0};
kern_return_t krv __attribute__((unused)) = mach_timebase_info(&info);
double r = 1.0 * info.numer / (info.denom * NSEC_PER_MSEC);
uint64_t time = (mach_absolute_time() * r);
uint32_t localtime = (uint32_t)time;
```

```java
//android
long currentTime = System.currentTimeMillis();
int localtime = (int)(currentTime & 0xFFFFFFFF);
```

