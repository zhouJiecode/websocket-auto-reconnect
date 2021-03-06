# websocket-auto-reconnect
基于sockjs+stompjs的websocket自动重连工具类

封装内容：
1. 一个类对象只使用一个sessionId，断开重连后也是使用这一个sessionId进行重连
2. 对连接状态做封装，connected：true,connecting,false，防止重复调用connect导致重复连接
3. 断线重连机制
4. stomp内执行error回调时不会把错误信息带给回调，这里做了封装，会把原生websocket错误对象以参数形式传给回调
5. sockjs内原生websocket刚建立但尚未连接成功时调用disconnect会报错，对这种情况，会等到连接建立（成功/失败）再disconnect
6. 断网过程中不重连，网络恢复后再自动重连

## example

```javascript
const ws = new ReconnetWebsocket(
  '/yourUrl',
)
ws.connect(
  stompClient => {
    console.log('ws 连接成功')

    stompClient.subscribe(
      '/yourChannel',
      res => {
        this.processData(res)
      },
    )
  },
  e => {
    console.log('ws 连接失败', e)
  }
}
ws.onReconnect = () => {
  console.log('reconnected')
}
```
