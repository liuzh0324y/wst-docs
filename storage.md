# oss 云存储服务器

## 概述
* 资源业务服务器 
>>> 主要负责图片、消息以及音视频的索引、存储等，负责与前端和存储服务器业务处理和转发。
* 业务控制服务器
>>> 主要负责负载均衡，业务服务器和存储服务器业务转发等功能。
* 资源存储服务器
>>> 主要负责音视频存储，音视频转码和回放管理等功能。
* 新资源存储服务器
>>> 主要负责与云存储服务器的文件上传与下载，授权上传与下载等功能。

## 资源存储服务器

资源存储服务器主要业务如下：  

* 登陆业务服务器
* 开始存储
* 停止存储
* 上报音视频元文件
* 接收合成指令
* 合成完毕后上报合成音视频文件
* ~~通过 nginx 使用回放服务~~

```mermaid
sequenceDiagram
    participant A as 资源业务服务器
    participant B as 业务控制服务器
    participant C as 资源存储服务器
    participant D as Nginx
    A->>B: login
    B-->>A: ret_login
    C->>B: login
    B-->>C: ret_login
    A->>B: start record
    B->>C: start
    C-->>B: ret_start
    B-->>A: ret_start
    C-->>C: handle storage
    A->>B: stop record
    B->>C: stop
    C-->>B: ret_stop
    B-->>A: ret_stop
    C->>B: reportfile
    B->>A: reportfile
    A-->>B: ret_reportfile
    B-->>C: ret_reportfile
    C-->>C: handle mix file
    C-->>D: 合成后的文件放在 nginx 目录

    C->>B: reportmixfile
    
    B->>A: reportmixfile
    A-->>B: ret_reportmixfile
    B-->>C: ret_reportmixfile

    A->>D: Get media file
    D-->>A: file stream
```

## 新资源存储服务器

新资源存储服务器主要业务如下：
* 获取本地音视频文件
* 上传获取的文件到 oss 服务器并删除本地文件
* ~~上报 oss 服务器文件信息到业务服务器~~
* 接收回放指令并解析所需要的文件信息，返回文件 oss url

业务流程时序图
```mermaid
sequenceDiagram
    participant A as 资源业务服务器 
    participant B as 业务控制服务器
    participant C as 现有存储服务器
    participant D as 新存储服务器
    participant E as Aliyun OSS

    A->>B: login
    B-->>A: ret_login
    C->>B: login
    B-->>C: ret_login
    A->>B: start record
    B->>C: start
    C-->>B: ret_start
    B-->>A: ret_start
    C-->>C: handle storage
    A->>B: stop record
    B->>C: stop
    C-->>B: ret_stop
    B-->>A: ret_stop
    C->>B: reportfile
    B->>A: reportfile
    A-->>B: ret_reportfile
    B-->>C: ret_reportfile
    C-->>C: handle mix file and storage /record
    C->>B: reportmixfile
    B->>A: reportmixfile
    A-->>B: ret_reportmixfile
    B-->>C: ret_reportmixfile
    D-->>D: 扫描 /record/ 下的文件
    D->>E: 上传文件到 oss 服务器
    E-->>D: ret code
    D-->>D: if succeed 删除文件
    A->>D: Get media file
    D-->>D: 做请求解析，兼容以前的数据。
    D->>E: 授权请求url
    E-->>D: ret url
    D-->>A: ret url
    A->>E: Get url
    E-->>A: file stream
```
## Note

* ?