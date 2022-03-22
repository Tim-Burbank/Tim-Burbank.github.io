---
layout: post
title: 移动端视频直播（一）
tag: 🍞
---

![image](https://user-images.githubusercontent.com/16463294/158949781-570e462d-4ee6-47bb-95dc-c8a249c4cc97.png =300x100)

目前移动端上主流的视频直播方案有 HLS 和 RTMP，移动 WEB 端目前就只有 HLS 能用，我们重点介绍它。

HTTP Live Streaming  
--------------

HTTP Live Streaming（简称 HLS）是一个基于 HTTP 的视频流协议，由 Apple 公司实现，Mac OS 上的 QuickTime、Safari 以及 iOS 上的 Safari 都能很好的支持 HLS，高版本 Android 也增加了对 HLS 的支持。一些常见的客户端如：MPlayerX、VLC 也都支持 HLS 协议。

HLS 协议基于 HTTP，非常简单。一个提供 HLS 的服务器需要做两件事：

* 编码：以 H.264 格式对图像进行编码，以AAC, MP3, AC-3, EC-3对声音进行编码，最终打包到 MPEG-2 TS（Transport Stream）/ MP4 容器之中；
* 分割：把编码好的 TS 文件等长切分成后缀为 ts 的小文件，并生成一个 .m3u8 的纯文本索引文件；
  
播放器使用的是 m3u8 文件。m3u8(mp3 url utf-8) 跟音频列表格式 m3u 很像，可以简单的认为 m3u8 就是包含多个 ts 文件的播放列表。播放器按顺序逐个播放，全部放完再请求一下 m3u8 文件，获得包含最新 ts 文件的播放列表继续播，周而复始。整个直播过程就是依靠一个不断更新的 m3u8 和一堆小的 ts 文件组成，m3u8 必须动态更新，ts 可以走 CDN。一个典型的 m3u8 文件格式如下：

>#EXTM3U  
>#EXTINF:20.000000,  
>000001.ts  
>#EXTINF:20.000000,  
>000002.ts  
>#EXTINF:20.000000,  
>000003.ts  
  
特点：  

* 高扩展性  
* 客户端通过 HTTP 短连接不断请求内容片段实现了直播流的播放  
* CDN 友好，部署方便、快捷，具有良好的防火墙穿透能力; 由于可以通过内容分发网络进行缓存分发，极大降低直播平台的流量成本  
* 可以直接在 HTML 5中播放； HLS 在苹果生态完全支持，大部分 Android手机浏览器也可以  
* 分片传输码率自适应技术，播放器根据网络状况选择不同的版本让播放更平稳而不用额外缓冲  
* 高延迟，根据分片大小通常10-30秒左右  


Real Time Messaging Protocol  
-------
Real Time Messaging Protocol（简称 RTMP）是 Macromedia 开发的一套视频直播协议，现在属于 Adobe。这套方案需要搭建专门的 RTMP 流媒体服务如 Adobe Media Server，并且在浏览器中只能使用 Flash 实现播放器。基于TCP，低延迟，把数据流分片，分片大小根据客户端与服务器协商的大小进行传输，保证稳定。


HTTP-FLV  
-------
音视频封装成FLV的格式，通过HTTP传输，方案的优点是基于HTTP长链接能够穿透防火墙，避免被拦截，首开延迟也低于RTMP，低延迟，延迟在2-5秒之间，缺点是会在本地缓存流媒体资源，保密性不够好。


RTMP播放和HLS/HTTP-FLV 区别  
-------

![image](https://user-images.githubusercontent.com/16463294/159442279-c8e5beff-f1e3-48e4-9eb7-73a6f839e8d3.jpeg)


  

  
