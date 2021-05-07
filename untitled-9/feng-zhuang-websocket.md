# 封装websocket

## 1. 封装websocket <a id="&#x5C01;&#x88C5;websocket"></a>

### 1.1.1. 协议 <a id="&#x534F;&#x8BAE;"></a>

websocket是个二进制协议，需要先通过Http协议进行握手，从而协商完成从Http协议向websocket协议的转换。一旦握手结束，当前的TCP连接后续将采用二进制websocket协议进行双向双工交互，自此与Http协议无关。

可以通过这篇知乎了解一下websocket协议的基本原理：[《WebSocket 是什么原理？为什么可以实现持久连接？》](https://www.zhihu.com/question/20215561)。

下面代码是封装的websocket

目录结构：

-impl

--connection.go

-main.go

-client.html

connection.go文件代码

```text
package impl

import (
    "errors"
    "sync"

    "github.com/gorilla/websocket"
)

type Connection struct {
    wsConn *websocket.Conn
    
    inChan chan []byte
    
    outChan   chan []byte
    closeChan chan byte
    mutex     sync.Mutex
    
    isClosed bool
}


func InitConnection(wsConn *websocket.Conn) (conn *Connection, err error) {
    conn = &Connection{
        wsConn:    wsConn,
        inChan:    make(chan []byte, 1000),
        outChan:   make(chan []byte, 1000),
        closeChan: make(chan byte, 1),
    }
    
    go conn.readLoop()
    
    go conn.writeLoop()
    return
}


func (conn *Connection) ReadMessage() (data []byte, err error) {
    select {
    case data = <-conn.inChan:
    case <-conn.closeChan:
        err = errors.New("connection is closed")
    }
    return
}


func (conn *Connection) WriteMessage(data []byte) (err error) {
    select {
    case conn.outChan <- data:
    case <-conn.closeChan:
        err = errors.New("connection is closed")
    }
    return
}


func (conn *Connection) Close() {
    
    conn.wsConn.Close()

    
    conn.mutex.Lock()
    if !conn.isClosed {
        close(conn.closeChan)
        conn.isClosed = true
    }
    conn.mutex.Unlock()
}

func (conn *Connection) readLoop() {
    var (
        data []byte
        err  error
    )
    for {
        if _, data, err = conn.wsConn.ReadMessage(); err != nil {
            goto ERR
        }
        
        select {
        case conn.inChan <- data:
        case <-conn.closeChan:
            
            goto ERR

        }
    }
ERR:
    conn.Close()
}

func (conn *Connection) writeLoop() {
    var (
        data []byte
        err  error
    )
    for {
        select {
        case data = <-conn.outChan:
        case <-conn.closeChan:
            goto ERR

        }
        if err = conn.wsConn.WriteMessage(websocket.TextMessage, data); err != nil {
            goto ERR
        }
    }
ERR:
    conn.Close()
}
```

main.go文件代码

```text
package main

import (
    "net/http"
    "time"

    "github.com/gorilla/websocket"
    "github.com/student/1330/impl"
)

var (
    upgrade = websocket.Upgrader{
        
        CheckOrigin: func(r *http.Request) bool {
            return true
        },
    }
)

func wsHandler(w http.ResponseWriter, r *http.Request) {
    var (
        
        wsConn *websocket.Conn
        err    error
        conn   *impl.Connection
        data   []byte
    )
    
    if wsConn, err = upgrade.Upgrade(w, r, nil); err != nil {
        return
    }

    if conn, err = impl.InitConnection(wsConn); err != nil {
        goto ERR
    }

    go func() {
        var (
            err error
        )
        for {
            if err = conn.WriteMessage([]byte("heartbeat")); err != nil {
                return
            }
            time.Sleep(time.Second * 1)
        }
    }()

    for {
        if data, err = conn.ReadMessage(); err != nil {
            goto ERR
        }
        if err = conn.WriteMessage(data); err != nil {
            goto ERR
        }
    }

ERR:
    conn.Close()
}

func main() {
    
    http.HandleFunc("/ws", wsHandler)
    http.ListenAndServe("0.0.0.0:5555", nil)
}
```

client.html文件代码

```text
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <script>
        window.addEventListener("load", function(evt) {
            var output = document.getElementById("output");
            var input = document.getElementById("input");
            var ws;
            var print = function(message) {
                var d = document.createElement("div");
                d.innerHTML = message;
                output.appendChild(d);
            };
            document.getElementById("open").onclick = function(evt) {
                if (ws) {
                    return false;
                }
                ws = new WebSocket("ws://localhost:5555/ws");
                ws.onopen = function(evt) {
                    print("OPEN");
                }
                ws.onclose = function(evt) {
                    print("CLOSE");
                    ws = null;
                }
                ws.onmessage = function(evt) {
                    print("RESPONSE: " + evt.data);
                }
                ws.onerror = function(evt) {
                    print("ERROR: " + evt.data);
                }
                return false;
            };
            document.getElementById("send").onclick = function(evt) {
                if (!ws) {
                    return false;
                }
                print("SEND: " + input.value);
                ws.send(input.value);
                return false;
            };
            document.getElementById("close").onclick = function(evt) {
                if (!ws) {
                    return false;
                }
                ws.close();
                return false;
            };
        });
    </script>
</head>
<body>
<table>
    <tr><td valign="top" width="50%">
            <p>Click "Open" to create a connection to the server,
                "Send" to send a message to the server and "Close" to close the connection.
                You can change the message and send multiple times.
            </p>
            <form>
                <button id="open">Open</button>
                <button id="close">Close</button>
                <input id="input" type="text" value="Hello world!">
                <button id="send">Send</button>
            </form>
        </td><td valign="top" width="50%">
            <div id="output"></div>
        </td></tr></table>
</body>
</html>
```

