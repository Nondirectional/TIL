# Server-Sent Events (SSE)
## 1. SSE是什么？

Server-Sent Events (SSE) 是一种网络通信技术，允许服务器向客户端推送事件流，而不需要客户端不断地发起请求。它是HTML5规范的一部分，提供了一种简单的方式来实现服务器向客户端的单向实时通信。

与WebSocket不同，SSE：
- 仅支持服务器到客户端的单向通信
- 使用标准的HTTP协议
- 自动重连机制
- 具有更简单的API和实现方式

SSE的通信格式非常简单，由纯文本组成，每个事件由一个或多个字段组成，字段之间用换行符分隔，事件之间用两个换行符分隔。

## 2. SSE的应用场景

SSE特别适合以下场景：

- **实时数据更新**：股票价格、天气信息、体育比分等需要实时更新的数据
- **通知系统**：用户通知、系统警报、消息提醒等
- **实时日志**：查看应用程序或系统的实时日志
- **聊天应用**：单向消息推送
- **进度指示器**：长时间运行任务的进度更新
- **新闻订阅**：实时新闻推送

相比轮询（客户端定期请求服务器），SSE更加高效，减少了不必要的请求和响应，降低了服务器负载和网络流量。

## 3. SSE的JS实现简单示例

在浏览器中，可以使用原生的`EventSource` API来接收SSE事件：

```html
<!DOCTYPE html>
<html>
<head>
    <title>SSE Client Example</title>
</head>
<body>
    <h1>Server-Sent Events Basic Example</h1>
    <div id="messages"></div>

    <script>
        // 创建EventSource对象，连接到SSE接口
        const eventSource = new EventSource("/api/events");
        
        // 连接成功建立时触发
        eventSource.onopen = function(event) {
            console.log("Connection to SSE stream established");
        };
        
        // 接收到默认类型的事件时触发
        eventSource.onmessage = function(event) {
            const messagesDiv = document.getElementById("messages");
            const p = document.createElement("p");
            p.textContent = event.data;
            messagesDiv.appendChild(p);
        };
        
        // 监听特定类型的事件
        eventSource.addEventListener('update', function(event) {
            const data = JSON.parse(event.data);
            console.log("Received update:", data);
            // 处理特定类型的事件...
        });
        
        // 发生错误时触发
        eventSource.onerror = function(error) {
            console.error("SSE connection error:", error);
            // 可以在这里实现重连逻辑
        };
        
        // 关闭连接
        function closeConnection() {
            eventSource.close();
            console.log("SSE connection closed");
        }
    </script>
</body>
</html>
```

**主要API说明：**

- `new EventSource(url, [options])`：创建新的SSE连接
- `eventSource.onopen`：连接建立时的回调
- `eventSource.onmessage`：接收默认事件的回调
- `eventSource.addEventListener(eventName, callback)`：监听特定类型的事件
- `eventSource.onerror`：连接错误时的回调
- `eventSource.close()`：关闭连接
- `event.data`：事件数据
- `event.lastEventId`：事件ID，用于断线重连
- `event.origin`：事件源的URL

## 4. SSE使用FastAPI实现的简单示例

在FastAPI中创建SSE接口主要依赖于`starlette`库中的`EventSourceResponse`。以下是一个基本示例：

### 安装必要的库

```bash
pip install fastapi uvicorn sse-starlette
```

### 基本的SSE接口示例

```python
from fastapi import FastAPI, Request
from sse_starlette.sse import EventSourceResponse
import asyncio
import datetime

app = FastAPI()

@app.get("/stream")
async def message_stream(request: Request):
    async def event_generator():
        while True:
            # 如果客户端断开连接，停止生成事件
            if await request.is_disconnected():
                break

            current_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            message = f"data: Current time is {current_time}\n\n" 
            yield message

            await asyncio.sleep(1) # 每秒发送一次事件

    return EventSourceResponse(event_generator())

# 发送带有事件类型和ID的事件
@app.get("/stream_with_type_and_id")
async def message_stream_with_type_and_id(request: Request):
    event_id_counter = 0
    
    async def event_generator():
        nonlocal event_id_counter
        while True:
            if await request.is_disconnected():
                break

            event_id_counter += 1
            current_time = datetime.datetime.now().strftime("%H:%M:%S")

            # 发送一个带有事件类型和ID的事件
            message = (
                f"event: time_update\n"
                f"id: {event_id_counter}\n"
                f"data: The current time is {current_time}.\n\n"
            )
            yield message

            # 发送另一个不同事件类型的事件
            if event_id_counter % 5 == 0:
                message_special = (
                    f"event: special_message\n"
                    f"id: {event_id_counter}\n"
                    f"data: This is a special message at count {event_id_counter}!\n\n"
                )
                yield message_special

            await asyncio.sleep(1)

    return EventSourceResponse(event_generator())

# 发送JSON数据
@app.get("/json_stream")
async def json_stream(request: Request):
    async def event_generator():
        counter = 0
        while True:
            if await request.is_disconnected():
                break
                
            counter += 1
            import json
            data_object = {
                "counter": counter,
                "timestamp": str(datetime.datetime.now()),
                "message": f"Update #{counter}"
            }
            message = f"data: {json.dumps(data_object)}\n\n"
            yield message
            
            await asyncio.sleep(1)
            
    return EventSourceResponse(event_generator())

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

### SSE事件的格式

SSE事件可以有不同的字段：

- **`data:`**: 必须有。事件数据。
- **`event:`**: 可选。事件的类型。客户端可以使用`addEventListener`监听特定类型的事件。
- **`id:`**: 可选。事件的ID。客户端可以利用此ID实现断线重连时从上次接收到的事件开始。
- **`retry:`**: 可选。客户端在断线重连时等待的时间（毫秒）。

### 注意事项和最佳实践

1. **断开连接检测**：使用`request.is_disconnected()`检查客户端是否断开连接，及时释放资源。
2. **错误处理**：实现完善的错误处理机制，包括日志记录和重连策略。
3. **Ping/Keep-alive**：`EventSourceResponse`默认会发送ping消息保持连接活跃。
4. **资源管理**：SSE连接会长时间保持，注意服务器资源管理。
5. **数据序列化**：发送复杂数据结构时，使用JSON序列化。