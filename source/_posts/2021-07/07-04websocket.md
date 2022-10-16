---
title: WebSocket 基于实现
date: 2021-07-04 21:05:28
tags: webSocket
---

## WebSocket

> [webSocket](https://websocket.org/)是一种在单个 TCP 连接上进行全双工通信的协议,可以使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。是基于 HTML5 的新协议，一个持久化的协议，相对于 HTTP 这种非持久的协议来说。

### 应用场景

应用提供多个用户相互交流，应用是展示服务器端经常变动的数据。涉及，社交订阅、流动数据、股票基金报价、天气更新、多媒体聊天、位置、视频.....

> 主要用于客户端部分（HTML5 是一个很宽广的概念，是对大量新 API 的总称）

### 特点包括

-   建立在 TCP 协议之上，服务器端的实现比较容易。
-   与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
-   数据格式比较轻量，性能开销小，通信高效。
-   可以发送文本，也可以发送二进制数据。
-   没有同源限制，客户端可以与任意服务器通信。
-   协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL。

## 主要

详细参考于阮老师日志，便于细节了解 [阮一峰的网络日志-websocket 教程](https://www.ruanyifeng.com/blog/2017/05/websocket.html)

### 实例代码

```
let that = null;
export default class WebSocketClient {

    constructor() {
        this.ws = null;
        this.url = null;
        this.from = null;
        this.to = null;
        that = this;
    }

    /**
     * 获取WebSocket单例
     * @returns {WebSocketClient}
     */
    static getInstance() {
        if (!this.instance) {
            this.instance = new WebSocketClient();
        }
        return this.instance;
    }

    /**
     * 初始化WebSocket
     */
    initWebSocket(url) {
        if(!url) return
        try {
            //timer为发送心跳的计时器
            this.timer && clearInterval(this.timer);
            this.ws = new WebSocket(url);
            this.initWsEvent();

        } catch (e) {
            console.log('WebSocket err:', e);
            //重连
            this.reconnect();
        }
    }

    /**
     * 初始化WebSocket相关事件
     */
    initWsEvent() {
        //建立WebSocket连接
        this.ws.onopen = function () {

            console.log('WebSocket:', '连接成功了');
            // 加载历史消息
            // that.getMessageHistory(from,to);
        };

       // 客户端接收服务端数据时触发
        this.ws.onmessage = function (evt) {
            // console.log("websocket:",evt);
            if (evt.data !== 'pong') {
                //不是心跳消息，消息处理逻辑
                //接收到消息，处理逻辑...
                // console.log("websocket response:",evt.data);
            } else {
                console.log('WebSocket: response pong msg=', evt.data);
            }
        };

        // this.getMessage();
       //连接错误
        this.ws.onerror = function (err) {
            // console.log('WebSocket:', 'connect to server error');
            //重连
            that.reconnect();
        };
        //连接关闭
        this.ws.onclose = function () {
            // console.log('WebSocket:', 'connect close');
            //重连
            that.reconnect();
        };

        //每隔15s向服务器发送一次心跳
        this.timer = setInterval(() => {
            if (this.ws && this.ws.readyState === WebSocket.OPEN) {
                console.log('WebSocket:', 'ping');
                this.ws.send("ping");
            }
        }, 15000);
    }

    //获取历史消息
    getMessageHistory(userId,shopId) {
        if (this.ws && this.ws.readyState === WebSocket.OPEN) {
            try {
                let params = {
                    "to ":shopId,
                    "from ":userId
                };

                this.ws.send(JSON.stringify(params));
                console.log("发送成功")
            } catch (err) {
                console.warn('ws sendMessage', err.message);
            }
        } else {
            console.log('WebSocket:', 'connect not open to send message');
        }
    }

    //发送消息
    sendMessage(msg,id) {
        if (this.ws && this.ws.readyState === WebSocket.OPEN) {
            try {
                let params = {
                    "cmd":1,
                    "msgType":msgType,//0=文本 1=图片 3=视频 4=语音
                    "to":cusId,
                    "from":id,
                    "msg": msg
                };

                this.ws.send(JSON.stringify(params));
            } catch (err) {
                console.warn('ws sendMessage', err.message);
            }
        } else {
            console.log('WebSocket:', 'connect not open to send message');
        }
    }

    //重连
    reconnect() {
        if (this.timeout) {
            clearTimeout(this.timeout);
        }
        this.timeout = setTimeout(function () {
            //重新连接WebSocket
            that.initWebSocket(this.url,this.from,this.to);
        }, 15000);
    }

    onClose(){
       if( this.ws ){
           this.ws.close();
       }

    }
}

```
