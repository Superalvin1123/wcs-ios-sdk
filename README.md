## 工程介绍
1. [工程源码](https://github.com/Wangsu-Cloud-Storage/wcs-js-sdk/tree/master/src)
2. [demo&例子](https://github.com/Wangsu-Cloud-Storage/wcs-js-sdk/tree/master/test/demo1)

## 开发准备
* 账号要求：已开通网宿云存储，并获取上传密钥，上传域名等
* 系统要求：H5以上
* Wcs-JavaScript-SDK没有包含`token`计算逻辑，为了安全，建议客户自己搭建token服务器计算token。可参考java-sdk等服务端SDK。

## 安装说明
1. 直接引用
```
<script type="text/javascript" src="/dist/wcs.min.js"></script>
通过sctipt标签引入该文件，会在全局生成名为 wcs 的对象
```

2. 通过npm安装
```
npm install wcs-js-sdk
```

## 初始化说明
1. sdk通过一个`uploadObj`对象用来控制上传行为，可触发上传、回调、停止等操作。
2. sdk会判断文件大小同块大小`BLOCK_SIZE`来决定使用直接上传还是分片上传。文件大小>BLOCK_SIZE：使用分片上传；文件大小<=BLOCK_SIZE：使用直接上传
```
使用
var uploadObj = wcs.wcsUpload(file, token, uploadUrl, extraConfig);

参数
file // 要上传的文件
token // 后台服务器获取的token
uploadUrl // 网宿云存储分配的上传地址
extraConfig={
    timeout: 0, //超时时间, 默认为0 超时错误可以重试上传
    concurrentRequestLimit:3, //并发数,默认为3
    retryCount:0 //上传重试, 默认为0
}
```

### 上传
```
uploadObj.putFile();
```

### 上传进度回调
```
uploadObj.uploadProgress = function (progress) {}
- progress格式
{
    total:{
        loaded: ?, //已经加载的大小
        size: ?,   //总文件大小
        percent: ? //百分比
    },
    chunks:[     //块信息, 数组形式
        {loaded: ?, size: ?, percent: ?},
        {loaded: ?, size: ?, percent: ?}
    ]
}
```

### 错误回调
```
uploadObj.onError = function (error) {}
- error格式
{
    code: ?,  // 错误码, 其中uploadObj.stop()停止与超时上传没有code
    message: "",  //错误信息
    isRequestError: true //是否是请求错误, 即正常上传后, 服务端返回错误, 这种是正常的错误, 不必重新上传
}
```

### 完成回调
```
uploadObj.onComplete = function(res){}
- res 格式
{
    data: jsonObj/String   //服务端返回的结果
}
```

### 停止
```
uploadObj.stop();
```