# **Phase 31 – WebSockets, Socket.IO and Real-Time Applications**



Advanced Level

Next.js 16.x

Node.js + TypeScript



## **1. Learning Objectives**

In this phase, you will learn how real-time communication works and how to build applications where users receive updates without repeatedly refreshing the page.

By the end, you should be able to:

1. Understand real-time communication and its use cases.
2. Compare HTTP request-response communication with WebSockets.
3. Understand the WebSocket protocol and handshake.
4. Understand full-duplex communication.
5. Build WebSocket servers using Node.js.
6. Understand Socket.IO architecture.
7. Work with events, rooms, namespaces, and broadcasting.
8. Implement authentication and authorization for socket connections.
9. Build a real-time chat application.
10. Implement online/offline presence, typing indicators, and read receipts.
11. Scale Socket.IO across multiple server instances using Redis.
12. Integrate real-time communication with Next.js.
13. Compare WebSockets, Socket.IO, and Server-Sent Events (SSE).
14. Secure and deploy real-time applications in production.

## **2. What Is Real-Time Communication?**

Real-Time Communication (RTC) is a communication approach in which information is delivered between systems with very little delay, often while users are actively interacting with an application.

Consider a normal website.

When you open a page, the browser sends a request to the server, and the server returns a response.

But imagine these applications:

- WhatsApp-like chat application.
- Live sports score application.
- Online multiplayer game.
- Real-time notification system.
- Collaborative document editor.
- Live delivery tracking.
- Stock market dashboard.

If a new message arrives, should the user refresh the page to see it?

No. The application should receive the update automatically.

That is where real-time communication becomes useful.

[20: Real-time Chat with WebSockets | Code with PHP](https://images.openai.com/static-rsc-4/KqeTJuARPlC1cl5Fy_iyMbo_foDJM6dQXirmHnruj-AsKq-U6hK5p2GdvSEMtNmQIHfYB7Tz_egjFlRZv_Dlb5AX9IyUMSohRVtp_qM_3v40gK6PO4MSz2ylFogEdJ2subPBMmHcbF8q9rTiwyB0UkbVxnqrS-geVodPgqtqvxw?purpose=inline)

[codewithphp.com](https://codewithphp.com/series/claude-php-developers/chapters/20-realtime-chat-websockets/?utm_source=chatgpt.com)

### **2.1 Real-World Examples**

| Application         | Real-time functionality          |
| ------------------- | -------------------------------- |
| Chat application    | Messages appear instantly        |
| E-commerce          | Live order status updates        |
| Food delivery       | Driver location updates          |
| Multiplayer game    | Player movements synchronize     |
| Trading dashboard   | Live price updates               |
| Collaboration tool  | Multiple users edit together     |
| Notification system | Notifications appear immediately |

### **2.2 Technologies Used for Real-Time Communication**

There are several ways to implement real-time communication.

| Technology               | Communication model                       | Typical use                   |
| ------------------------ | ----------------------------------------- | ----------------------------- |
| Polling                  | Repeated HTTP requests                    | Simple status checks          |
| Long Polling             | HTTP request held until data is available | Legacy real-time applications |
| Server-Sent Events (SSE) | Server-to-client stream                   | Live feeds and notifications  |
| WebSockets               | Bidirectional persistent connection       | Chat, games, collaboration    |
| WebRTC                   | Peer-to-peer media/data communication     | Audio/video calls             |

We will focus primarily on WebSockets and Socket.IO.

## **3. HTTP Request-Response vs Real-Time Communication**

Before understanding WebSockets, you must understand how traditional HTTP communication works.

### **3.1 Traditional HTTP Communication**

HTTP normally follows a request-response model.

Example:

```
Browser
   |
   | HTTP Request
   |
   v
Web Server
   |
   | HTTP Response
   |
   v
Browser
```

Example:

```
GET /api/products HTTP/1.1
Host: example.com
```

Server response:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "products": [
    {
      "id": 1,
      "name": "Laptop"
    }
  ]
}
```

The server responds to the request.

If new data becomes available after the response, the server does not ordinarily send another response through that completed HTTP exchange.

The client must make another request or use another communication mechanism.

### **3.2 The Polling Approach**

One simple way to receive updates is Polling.

Polling means the client repeatedly asks the server whether new data is available.

Example:

```
Browser              Server
   |                    |
   |--- Request ------->|
   |<-- Response -------|
   |                    |
   |--- Request ------->|
   |<-- Response -------|
   |                    |
   |--- Request ------->|
   |<-- Response -------|
```

For example, the browser requests new messages every 5 seconds.

JavaScript example:

```
setInterval(async () => {
  const response = await fetch("/api/messages");

  const messages = await response.json();

  console.log(messages);
}, 5000);
```

**Advantages:**

- Easy to implement.
- Uses normal HTTP endpoints.
- Works with conventional HTTP infrastructure.

**Disadvantages:**

- Repeated unnecessary requests.
- Updates can be delayed until the next polling interval.
- Higher request overhead.
- Not efficient for frequent updates.

### **3.3 Long Polling**

Long Polling is an improvement over ordinary polling.

The client sends a request, but the server keeps it open until new data is available or a timeout occurs.

```
Client                 Server
  |                       |
  |------ Request ------->|
  |                       |
  |      Wait...          |
  |                       |
  |      New data         |
  |                       |
  |<----- Response -------|
  |                       |
  |------ Request ------->|
  |                       |
```

Example use case:

A chat client asks the server for new messages. The server waits until a message arrives and then responds.

The client immediately sends another request.

**Advantages:**

- Updates can arrive without repeatedly making short polling requests.
- Uses HTTP infrastructure.

**Disadvantages:**

- Requests must be reopened repeatedly.
- Connection management can be complicated.
- Less efficient than a suitable persistent connection for frequent bidirectional updates.

### **3.4 Polling vs Long Polling**

| Polling                              | Long Polling                        |
| ------------------------------------ | ----------------------------------- |
| Repeated requests at fixed intervals | Request waits for new data          |
| Can generate many empty responses    | Reduces unnecessary empty responses |
| Simple implementation                | More complex server management      |
| Updates delayed by polling interval  | Updates delivered when available    |

## **4. What Is WebSocket?**

WebSocket is a communication protocol that provides a persistent, bidirectional connection between a client and a server.

Unlike traditional HTTP request-response communication, WebSocket allows both sides to send messages independently after the connection has been established.

The WebSocket protocol is standardized in RFC 6455.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.rfc-editor.org\&sz=32)

rfc-editor.org+1



### **4.1 Traditional HTTP vs WebSocket**

**HTTP:**

```
Browser                 Server
   |                       |
   |------ Request ------->|
   |<----- Response -------|
   |                       |
   |------ Request ------->|
   |<----- Response -------|
```

The client initiates each normal request.

**WebSocket:**

```
Browser                 Server
   |                       |
   |--- Connection ------->|
   |<-- Connection Ready --|
   |                       |
   |<---- Message ----------|
   |                       |
   |------ Message -------->|
   |                       |
   |<---- Message ----------|
   |                       |
   |------ Message -------->|
```

Once connected, either side can send messages.

### **4.2 What Does Bidirectional Mean?**

Bidirectional means data can travel in both directions.

For example, in a chat application:

1. User A sends a message.
2. Server receives the message.
3. Server sends it to User B.
4. User B can immediately send a reply.
5. Server sends the reply to User A.

Neither side needs to initiate a new HTTP request for every message.

### **4.3 What Is Full-Duplex Communication?**

Full-duplex communication means both parties can transmit and receive data independently at the same time.

Compare:

| Communication | Description                                           |
| ------------- | ----------------------------------------------------- |
| Simplex       | Data travels in one direction only                    |
| Half-duplex   | Both directions are supported, but not simultaneously |
| Full-duplex   | Both directions can operate simultaneously            |

WebSockets support full-duplex communication.

[Full Duplex(전이중), Half Duplex(반이중), Simplex(단방향)](https://images.openai.com/static-rsc-4/NF0Skoqv2iiIlarP7t2_bwD4T5jSYeAjPWOZjgiq5D8f7Lqk1jtr0HjsYgA9n0rXr92XEeAHYrj8hjWRGPdz2QVSHycOY05F-aOkw_WVAJ9-xHT3ee1aZNOy5yA7tVN9kz7zed3oq8BksmStB7NcMaIz9_c6YNzhE86J4xbHTpU?purpose=inline)

[vir-us.tistory.com](https://vir-us.tistory.com/m/entry/Full-Duplex%EC%A0%84%EC%9D%B4%EC%A4%91-Half-Duplex%EB%B0%98%EC%9D%B4%EC%A4%91-Simplex%EB%8B%A8%EB%B0%A9%ED%96%A5?utm_source=chatgpt.com)

### **4.4 Is WebSocket Built on HTTP?**

WebSocket starts with an HTTP-based opening handshake in the classic WebSocket protocol.

After the server accepts the upgrade, communication uses WebSocket frames rather than ordinary HTTP request-response messages.

A WebSocket connection can remain open until either side closes it or the underlying connection fails.

### **4.5 WebSocket URLs**

There are two common WebSocket URL schemes:

| Scheme   | Meaning                    |
| -------- | -------------------------- |
| `ws://`  | WebSocket without TLS      |
| `wss://` | WebSocket protected by TLS |

Examples:

```
ws://localhost:4000
wss://example.com
```

For production websites, use `wss://`.

It protects WebSocket traffic using TLS, just as HTTPS protects HTTP traffic.

## **5. WebSocket Handshake**

The WebSocket Handshake is the process through which a client requests that an HTTP connection be upgraded to a WebSocket connection.

Let's understand the process.

### **5.1 Step 1 – Client Sends an HTTP Upgrade Request**

The browser sends a request similar to:

```
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: random-generated-key
Sec-WebSocket-Version: 13
Origin: https://example.com
```

Important headers:

| Header                  | Purpose                                      |
| ----------------------- | -------------------------------------------- |
| `Upgrade`               | Requests switching to the WebSocket protocol |
| `Connection`            | Indicates an upgrade request                 |
| `Sec-WebSocket-Key`     | Random value used in handshake verification  |
| `Sec-WebSocket-Version` | WebSocket protocol version                   |
| `Origin`                | Identifies the originating web page          |

### **5.2 Step 2 – Server Accepts the Upgrade**

If the server accepts the request, it responds with:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: calculated-value
```

The status code `101` means Switching Protocols.

### **5.3 Step 3 – WebSocket Communication Begins**

After the handshake succeeds, both sides can exchange WebSocket frames.

```
HTTP Connection
      |
      v
Upgrade Request
      |
      v
Server Returns 101
      |
      v
WebSocket Connection
      |
      v
Bidirectional Messaging
```

[WebSocket Protocol: Understanding RFC 6455 | Ayyoub MAHFOUD posted on the topic | LinkedIn](https://images.openai.com/static-rsc-4/ToXIDPTgHRO222D_OAtEEuqJt4WsxdnxL2ZrHW0QLq8HGOPxhpR8-XfkY6BPEG2Sx-MbKa1d63SIWgbuhaITmU9-4r1dOXpFFNX_JXjGdJe8v0QY6GkdrMGhKdiHt7UVjnJ020BwDmeawqDEIQqf9bgRqRO8D6hM1FjO21l7uUE?purpose=inline)

[linkedin.com](https://www.linkedin.com/posts/ayyoubmahfoud_websockets-backendengineering-networking-activity-7401639163923423232-NnAK?utm_source=chatgpt.com)

### **5.4 Important: WebSocket Is Not the Same as Socket.IO**

This distinction is extremely important.

| WebSocket                          | Socket.IO                                                             |
| ---------------------------------- | --------------------------------------------------------------------- |
| Communication protocol             | Real-time communication library                                       |
| Standardized protocol              | Library with its own protocol                                         |
| Provides low-level messaging       | Provides higher-level events and abstractions                         |
| No built-in rooms                  | Provides rooms                                                        |
| No built-in automatic reconnection | Provides reconnection functionality                                   |
| No built-in acknowledgements API   | Provides event acknowledgements                                       |
| Uses WebSocket frames              | Uses Socket.IO protocol, commonly over WebSocket or HTTP long-polling |

Socket.IO is not a plain WebSocket implementation.

A standard WebSocket client cannot directly communicate with a Socket.IO server simply by connecting to its URL.

[image](https://www.google.com/s2/favicons?domain=https://www.rfc-editor.org\&sz=32)

rfc-editor.org+1

## **6. Building a Native WebSocket Server with Node.js**

Before learning Socket.IO, let's implement WebSockets directly.

We will use the `ws` library.

`ws` is a popular WebSocket implementation for Node.js.

### **6.1 Install Dependencies**

Create a project:

```
mkdir websocket-server
cd websocket-server

npm init -y

npm install ws

npm install -D typescript tsx @types/node @types/ws
```

### **6.2 Create a Basic WebSocket Server**

Create:

```
src/server.ts
```

```
import { createServer } from "http";

import { WebSocketServer } from "ws";

const httpServer = createServer();

const wss = new WebSocketServer({
  server: httpServer,
});

wss.on("connection", (socket) => {
  console.log("Client connected");

  socket.send("Welcome to the WebSocket server");

  socket.on("message", (data) => {
    const message = data.toString();

    console.log("Received:", message);

    socket.send(`Server received: ${message}`);
  });

  socket.on("close", () => {
    console.log("Client disconnected");
  });

  socket.on("error", (error) => {
    console.error("WebSocket error:", error);
  });
});

httpServer.listen(4000, () => {
  console.log("WebSocket server running on port 4000");
});
```

Add this script:

```
{
  "scripts": {
    "dev": "tsx watch src/server.ts"
  }
}
```

Run:

```
npm run dev
```

### **6.3 Connect from the Browser**

Create a simple HTML page:

```
<!DOCTYPE html>
<html>
<head>
  <title>WebSocket Demo</title>
</head>
<body>

  <h1>WebSocket Client</h1>

  <input id="message" placeholder="Enter message" />

  <button id="send">Send</button>

  <div id="output"></div>

  <script>
    const socket = new WebSocket("ws://localhost:4000");

    const output = document.getElementById("output");

    socket.addEventListener("open", () => {
      output.innerHTML += "<p>Connected</p>";
    });

    socket.addEventListener("message", (event) => {
      output.innerHTML += `<p>${event.data}</p>`;
    });

    document.getElementById("send").addEventListener("click", () => {
      const message = document.getElementById("message").value;

      if (socket.readyState === WebSocket.OPEN) {
        socket.send(message);
      }
    });

    socket.addEventListener("close", () => {
      output.innerHTML += "<p>Disconnected</p>";
    });
  </script>

</body>
</html>
```

### **6.4 Understand WebSocket Client Events**

| Event     | Meaning                           |
| --------- | --------------------------------- |
| `open`    | Connection established            |
| `message` | Message received                  |
| `error`   | Connection or communication error |
| `close`   | Connection closed                 |

The browser's WebSocket API also provides methods and properties:

| Method / Property | Purpose                                |
| ----------------- | -------------------------------------- |
| `send()`          | Send data                              |
| `close()`         | Close connection                       |
| `readyState`      | Current connection state               |
| `bufferedAmount`  | Amount of data queued for transmission |

### **6.5 WebSocket Ready States**

```
WebSocket.CONNECTING
WebSocket.OPEN
WebSocket.CLOSING
WebSocket.CLOSED
```

You should check the connection state before sending messages.

```
if (socket.readyState === WebSocket.OPEN) {
  socket.send("Hello");
}
```

### **6.6 Broadcast to All Connected Clients**

The server can send a message to all connected clients.

Update the server:

```
wss.on("connection", (socket) => {
  socket.on("message", (data) => {
    const message = data.toString();

    wss.clients.forEach((client) => {
      if (client.readyState === client.OPEN) {
        client.send(message);
      }
    });
  });
});
```

Now if one client sends a message, all connected clients receive it.

This is a simple broadcast.

### **6.7 Limitations of Native WebSockets**

Native WebSockets are powerful, but you must implement many application-level features yourself:

- Event naming conventions.
- Rooms and groups.
- Automatic reconnection.
- Acknowledgements.
- Connection authentication.
- Message validation.
- Presence tracking.
- Broadcasting across multiple servers.

This is why developers often use Socket.IO for application-level real-time communication.

[image](https://www.google.com/s2/favicons?domain=https://developer.mozilla.org\&sz=32)

MDN+1

## **7. What Is Socket.IO?**

Socket.IO is a JavaScript library that enables real-time, bidirectional, event-based communication between clients and servers.

It is commonly used with:

- Node.js.
- Express.
- React.
- Next.js.
- Vue.
- Angular.

Socket.IO provides features on top of its underlying transports.

### **7.1 Why Use Socket.IO Instead of Native WebSockets?**

Consider implementing a complete chat application using native WebSockets.

You would need to implement:

1. Connection management.
2. Reconnection handling.
3. Event-based messaging.
4. Room management.
5. Broadcasting.
6. Acknowledgements.
7. Connection authentication.
8. Heartbeat and failure handling.

Socket.IO provides many of these capabilities.

### **7.2 Socket.IO Features**

| Feature                   | Description                                                                                           |
| ------------------------- | ----------------------------------------------------------------------------------------------------- |
| Event-based communication | Send named events                                                                                     |
| Automatic reconnection    | Reconnect after temporary disconnections                                                              |
| Rooms                     | Group clients                                                                                         |
| Namespaces                | Separate communication channels                                                                       |
| Broadcasting              | Send events to multiple clients                                                                       |
| Acknowledgements          | Receive confirmation of an event                                                                      |
| Fallback transport        | Can use HTTP long polling when WebSocket is unavailable                                               |
| Connection state recovery | Can recover some missed state and packets after temporary disconnection when supported and configured |

### **7.3 Socket.IO Architecture**

[Socket.IO Deep Dive. Everything You Need To Know About… | by Eve Martin | Ably: Serious, serverless realtime infrastructure | Medium](https://images.openai.com/static-rsc-4/Qbf7HyLWx8QH9UveIdytWb58tcdLzB3rL12dKLt6d_MIs-VxKuHirjfyYdwIbvG4SWml1g2iNjHH3Yf3F0Zf392_PpY-NGVb1HB_IxTg6_iFwwQftTEHvZecfCWMiLprYgLGZh0wMGv6xrgJ1dJLUreBGPu7exlR4Ok04lpNEJs?purpose=inline)

[medium.com](https://medium.com/ably-realtime/socket-io-deep-dive-8a6a4d0877de?utm_source=chatgpt.com)

```
          Browser / React / Next.js
                    |
                    v
               Socket.IO Client
                    |
                    v
              Socket.IO Server
                    |
             +------+------+
             |             |
             v             v
         Event System    Rooms
             |             |
             +------+------+
                    |
                    v
             Application Logic
                    |
                    v
                 Database
```

### **7.4 Does Socket.IO Always Use WebSockets?**

No.

Socket.IO commonly uses WebSocket when available, but it can fall back to HTTP long polling.

It also adds its own protocol on top of the underlying transport.

Therefore:

```
Socket.IO Client ≠ Native WebSocket Client
```

A Socket.IO client must communicate with a compatible Socket.IO server.

## **8. Building a Socket.IO Server with Node.js**

Now let's create a real-time server using Node.js, Express, and Socket.IO.

### **8.1 Install Dependencies**

```
mkdir socketio-server
cd socketio-server

npm init -y

npm install express socket.io cors

npm install -D typescript tsx @types/node @types/express
```

### **8.2 Create the Server**

Create:

```
src/server.ts
```

```
import express from "express";

import { createServer } from "http";

import { Server } from "socket.io";

import cors from "cors";

const app = express();

app.use(cors({
  origin: "http://localhost:3000",
}));

app.use(express.json());

const httpServer = createServer(app);

const io = new Server(httpServer, {
  cors: {
    origin: "http://localhost:3000",
    methods: ["GET", "POST"],
  },
});

io.on("connection", (socket) => {
  console.log("User connected:", socket.id);

  socket.emit("welcome", {
    message: "Welcome to Socket.IO server",
  });

  socket.on("send-message", (data) => {
    console.log("Message received:", data);

    io.emit("receive-message", data);
  });

  socket.on("disconnect", (reason) => {
    console.log("User disconnected:", reason);
  });
});

httpServer.listen(4000, () => {
  console.log("Server running on port 4000");
});
```

Add:

```
{
  "scripts": {
    "dev": "tsx watch src/server.ts"
  }
}
```

Run:

```
npm run dev
```

Your Socket.IO server is now running.

### **8.3 Install Socket.IO Client**

In a separate frontend project:

```
npm install socket.io-client
```

### **8.4 Connect from a React Component**

```
"use client";

import { useEffect, useState } from "react";

import { io, Socket } from "socket.io-client";

export default function ChatPage() {
  const [socket, setSocket] = useState<Socket | null>(null);

  const [message, setMessage] = useState("");

  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    const socketInstance = io("http://localhost:4000");

    setSocket(socketInstance);

    socketInstance.on("welcome", (data) => {
      console.log(data.message);
    });

    socketInstance.on("receive-message", (data) => {
      setMessages((previous) => [
        ...previous,
        data.message,
      ]);
    });

    return () => {
      socketInstance.disconnect();
    };
  }, []);

  function sendMessage() {
    if (!socket || !message.trim()) return;

    socket.emit("send-message", {
      message,
    });

    setMessage("");
  }

  return (
    <div>
      <h1>Real-Time Chat</h1>

      <input
        value={message}
        onChange={(event) => setMessage(event.target.value)}
        placeholder="Enter message"
      />

      <button onClick={sendMessage}>
        Send
      </button>

      <div>
        {messages.map((item, index) => (
          <p key={index}>{item}</p>
        ))}
      </div>
    </div>
  );
}
```

### **8.5 Understand the Code**

Server:

```
io.on("connection", (socket) => {
  // A client connected
});
```

This event runs when a client establishes a Socket.IO connection.

Client:

```
const socket = io("http://localhost:4000");
```

This creates a Socket.IO client connection.

Sending:

```
socket.emit("send-message", {
  message: "Hello",
});
```

Receiving:

```
socket.on("receive-message", (data) => {
  console.log(data);
});
```

Disconnecting:

```
socket.disconnect();
```

### **8.6 Important React Cleanup**

Always clean up your socket connection and listeners when a component unmounts.

Otherwise, you may encounter:

- Duplicate event listeners.
- Duplicate messages.
- Unnecessary connections.
- Memory leaks.

The example disconnects the socket during cleanup.

For a larger application, create a shared socket provider or connection manager rather than creating separate connections in every component.

## **9. Events and Event Emitters**

Socket.IO uses event-based communication.

An Event is an identifiable action or message that a client or server can emit and another party can listen for.

Examples:

```
send-message
receive-message
user-joined
user-left
typing-started
typing-stopped
message-read
notification
```

### **9.1 Emit an Event**

Server:

```
socket.emit("welcome", {
  message: "Welcome!",
});
```

Client:

```
socket.emit("send-message", {
  message: "Hello",
});
```

### **9.2 Listen for an Event**

Server:

```
socket.on("send-message", (data) => {
  console.log(data);
});
```

Client:

```
socket.on("receive-message", (data) => {
  console.log(data);
});
```

### **9.3&#x20;**`socket.emit()`**&#x20;vs&#x20;**`io.emit()`

This distinction is important.

| Method                    | Who receives the event?                       |
| ------------------------- | --------------------------------------------- |
| `socket.emit()`           | Only the current connected socket             |
| `io.emit()`               | All connected sockets in the namespace        |
| `socket.broadcast.emit()` | All sockets except the sender                 |
| `io.to(room).emit()`      | Sockets in a particular room                  |
| `socket.to(room).emit()`  | Other sockets in a room, excluding the sender |

Example:

```
socket.emit("private-message", {
  message: "Only you receive this",
});
```

Broadcast to everyone:

```
io.emit("announcement", {
  message: "Server announcement",
});
```

Broadcast to everyone except the sender:

```
socket.broadcast.emit("user-joined", {
  userId: "user-101",
});
```

### **9.4 Use Acknowledgements**

An acknowledgement allows the sender to receive a confirmation that an event handler processed the event.

Client:

```
socket.emit(
  "send-message",
  { message: "Hello" },
  (response: { success: boolean }) => {
    console.log(response);
  }
);
```

Server:

```
socket.on("send-message", (data, callback) => {
  console.log(data.message);

  callback({
    success: true,
  });
});
```

### **9.5 Acknowledgement with Timeout**

The client can also set an acknowledgement timeout.

```
socket.timeout(5000).emit(
  "send-message",
  { message: "Hello" },
  (error, response) => {
    if (error) {
      console.log("Acknowledgement timed out");
      return;
    }

    console.log(response);
  }
);
```

**Important:** An acknowledgement only confirms that the receiving handler returned an acknowledgement. It does not automatically guarantee that data has been durably stored or delivered to every recipient.

For important messages, persist the message in the database and use delivery/read states.

## **10. Rooms in Socket.IO**

A Room is a logical group of connected sockets.

It allows you to send an event to a selected group of users instead of broadcasting to everyone.

Imagine a chat application.

You have three conversations:

```
Room 1: user-101 and user-102
Room 2: user-101 and user-103
Room 3: user-104 and user-105
```

When a message is sent in Room 1, only its participants should receive it.

### **10.1 Join a Room**

Server:

```
io.on("connection", (socket) => {
  socket.on("join-room", (roomId: string) => {
    socket.join(roomId);

    console.log(`${socket.id} joined ${roomId}`);
  });
});
```

Client:

```
socket.emit("join-room", "room-101");
```

### **10.2 Send Message to a Room**

Server:

```
socket.on("send-message", (data) => {
  io.to(data.roomId).emit("receive-message", {
    message: data.message,
  });
});
```

Only sockets in that room receive the event.

### **10.3 Exclude the Sender**

If the sender already displays the message locally:

```
socket.to(data.roomId).emit("receive-message", {
  message: data.message,
});
```

The sender will not receive this broadcast.

### **10.4 Leave a Room**

```
socket.leave("room-101");
```

Socket.IO automatically removes a disconnected socket from its rooms.

### **10.5 Room Use Cases**

| Use Case        | Example Room       |
| --------------- | ------------------ |
| Private chat    | `conversation-101` |
| Group chat      | `group-201`        |
| Live game       | `game-301`         |
| Notifications   | `user-101`         |
| Live tracking   | `delivery-501`     |
| Admin dashboard | `admin-dashboard`  |

### **10.6 Important Security Rule**

Never trust the client simply because it asks to join a room.

For example:

```
socket.emit("join-room", "conversation-101");
```

The server must verify that the authenticated user is actually a participant in that conversation.

Otherwise, a malicious user might join another person's private chat.

Rooms are routing mechanisms, not authorization systems.

[image](https://www.google.com/s2/favicons?domain=https://socket.io\&sz=32)

socket.io

## **11. Namespaces in Socket.IO**

A Namespace is a separate communication channel within a Socket.IO server.

It allows you to divide different real-time features into logical communication spaces.

Example:

```
Socket.IO Server
       |
       +----------------+
       |                |
       v                v
      /chat           /admin
       |                |
       v                v
  Chat Events       Admin Events
```

### **11.1 Create a Namespace**

Server:

```
const chatNamespace = io.of("/chat");

chatNamespace.on("connection", (socket) => {
  console.log("Connected to chat namespace");

  socket.on("send-message", (data) => {
    chatNamespace.emit("receive-message", data);
  });
});
```

Another namespace:

```
const adminNamespace = io.of("/admin");

adminNamespace.on("connection", (socket) => {
  console.log("Admin connected");
});
```

### **11.2 Connect to a Namespace**

Client:

```
const chatSocket = io("http://localhost:4000/chat");

const adminSocket = io("http://localhost:4000/admin");
```

### **11.3 Namespace vs Room**

This is a common interview question.

| Namespace                                  | Room                                  |
| ------------------------------------------ | ------------------------------------- |
| Separate communication channel             | Group of sockets within a namespace   |
| Has its own event handlers and middleware  | Used to target selected sockets       |
| Example: `/chat`                           | Example: `conversation-101`           |
| Can have namespace-specific authentication | Room membership must be authorized    |
| Logical separation of features             | Logical grouping of connected clients |

**Remember:**

A Namespace separates communication channels. A Room groups connections within a namespace.

## **12. Socket.IO Middleware and Authentication**

Real-time connections must be protected.

For example, an unauthenticated user should not be able to join private chat rooms or receive private notifications.

Socket.IO provides middleware that runs during connection establishment.

### **12.1 Install Authentication Dependencies**

For a JWT-based example:

```
npm install jsonwebtoken

npm install -D @types/jsonwebtoken
```

### **12.2 Create Authentication Middleware**

```
import jwt from "jsonwebtoken";

io.use((socket, next) => {
  const token = socket.handshake.auth.token;

  if (!token) {
    return next(new Error("Authentication required"));
  }

  try {
    const payload = jwt.verify(
      token,
      process.env.JWT_SECRET!
    );

    socket.data.user = payload;

    next();
  } catch {
    next(new Error("Invalid token"));
  }
});
```

### **12.3 Send Token from Client**

```
const socket = io("http://localhost:4000", {
  auth: {
    token: accessToken,
  },
});
```

### **12.4 Access Authenticated User**

```
io.on("connection", (socket) => {
  console.log("Authenticated user:", socket.data.user);
});
```

### **12.5 Important: Middleware Runs at Connection Time**

Socket.IO connection middleware runs when a socket connects.

It does not automatically authorize every event.

For example, a user may connect successfully but still not have permission to delete a message.

You must check permissions inside the relevant event handler or application service.

### **12.6 Join a Private Conversation Safely**

```
socket.on("join-conversation", async (conversationId: string) => {
  const userId = socket.data.user.id;

  const allowed = await isConversationMember(
    userId,
    conversationId
  );

  if (!allowed) {
    socket.emit("error-message", {
      message: "Access denied",
    });

    return;
  }

  socket.join(`conversation:${conversationId}`);
});
```

This ensures room membership is based on server-side authorization.

**Security note:** Verify JWT signatures and claims, use secure secrets or keys, enforce token expiration, and handle logout and revoked sessions. A long-lived socket may need to be disconnected when its session expires or is revoked.

## **13. Real-Time Chat Application – Complete Architecture**

Now let's combine what we have learned.

Imagine building a WhatsApp-like chat application.

The application needs:

- User authentication.
- One-to-one conversations.
- Group conversations.
- Real-time messages.
- Online/offline status.
- Typing indicators.
- Read receipts.
- Message history.

### **13.1 Overall Architecture**

[I Built a ‘Scalable’ System That Couldn’t Handle 100 Users | by The Latency Gambler | Medium](https://images.openai.com/static-rsc-4/Uo1SUeSZqaIPzFHsr1VIcJccCQZsxWBOkDbhpnooPGt71inTuOmZ7alxS7mL3Q8F5PNpXLG1Z4IGTk2FsQKYeLPE-MQ6eKVpNrNb0C9jLTPsC381tEA48RGPza4MiDDjCv7LZ6Ywd_TEPX-DVbqNitGipSiqaBMbAPsj71GbrP8?purpose=inline)

[medium.com](https://medium.com/%40kanishks772/i-built-a-scalable-system-that-couldn-t-handle-100-users-7c84e8c8dee2?utm_source=chatgpt.com)

```
                Browser / Mobile
                       |
                       v
                  Next.js App
                       |
                       v
                Socket.IO Client
                       |
                       v
                Socket.IO Server
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
    Auth Service   Chat Service   Presence Service
                       |
                       v
                    MongoDB
                       |
                       v
                     Redis
```

### **13.2 Suggested Folder Structure**

```
chat-application/
│
├── frontend/
│   └── next-app/
│       ├── src/
│       │   ├── app/
│       │   ├── components/
│       │   ├── hooks/
│       │   └── lib/
│       │       └── socket.ts
│       │
│       └── package.json
│
├── backend/
│   └── socket-server/
│       ├── src/
│       │   ├── config/
│       │   ├── middleware/
│       │   ├── events/
│       │   ├── services/
│       │   ├── sockets/
│       │   ├── models/
│       │   └── server.ts
│       │
│       └── package.json
│
└── docker-compose.yml
```

### **13.3 Define Message Structure**

A chat message should contain more than just its text.

Example:

```
type Message = {
  id: string;
  conversationId: string;
  senderId: string;
  content: string;
  createdAt: Date;
  status: "SENT" | "DELIVERED" | "READ";
};
```

For a real system, also consider:

- Message type.
- Attachment metadata.
- Edited timestamp.
- Deleted timestamp.
- Client-generated idempotency ID.
- Reply-to message ID.

### **13.4 Define Socket Events**

| Event               | Direction       | Purpose                  |
| ------------------- | --------------- | ------------------------ |
| `join-conversation` | Client → Server | Join a conversation      |
| `send-message`      | Client → Server | Send a message           |
| `new-message`       | Server → Client | Deliver a message        |
| `typing-started`    | Client → Server | Notify typing            |
| `typing-stopped`    | Client → Server | Stop typing notification |
| `message-read`      | Client → Server | Mark message as read     |
| `user-online`       | Server → Client | Announce online status   |
| `user-offline`      | Server → Client | Announce offline status  |

### **13.5 Send a Message**

Client:

```
socket.emit("send-message", {
  conversationId: "conversation-101",
  content: "Hello!",
});
```

Server:

```
socket.on("send-message", async (data, callback) => {
  try {
    const userId = socket.data.user.id;

    const message = await saveMessage({
      conversationId: data.conversationId,
      senderId: userId,
      content: data.content,
    });

    io.to(`conversation:${data.conversationId}`).emit(
      "new-message",
      message
    );

    callback({
      success: true,
      messageId: message.id,
    });
  } catch {
    callback({
      success: false,
      error: "Unable to send message",
    });
  }
});
```

This demonstrates the basic flow.

In a production implementation, validate the input, verify conversation membership, limit message size, and make message persistence idempotent.

### **13.6 Why Save Before Broadcasting?**

The recommended order is:

```
Receive Message
      |
      v
Validate Message
      |
      v
Check Authorization
      |
      v
Save Message to Database
      |
      v
Broadcast Message
      |
      v
Send Acknowledgement
```

Why?

Because if you broadcast first and the database operation subsequently fails, users may see a message that was never saved.

Persisting before broadcasting reduces that inconsistency.

However, broadcasting and persistence are still separate operations. For stronger delivery guarantees, use durable event processing and a transactional outbox or equivalent approach.

### **13.7 Load Previous Messages Using HTTP**

Real-time communication does not mean every operation must use WebSockets.

A good architecture often uses:

- HTTP or GraphQL to load historical messages.
- Socket.IO to deliver new messages.
- HTTP or Socket.IO acknowledgements to confirm actions.

For example:

```
Initial Chat Load:
Next.js → GET /api/conversations/101/messages

New Message:
Socket.IO → new-message event
```

This separation is practical and efficient.

## **14. Online and Offline Presence**

Presence refers to whether a user is currently available or connected to the application.

For example:

```
User A – Online
User B – Offline
User C – Online
```

### **14.1 Basic Presence Implementation**

Maintain a mapping of users to their active sockets.

```
const onlineUsers = new Map<string, Set<string>>();
```

When a user connects:

```
function addUserSocket(userId: string, socketId: string) {
  const sockets = onlineUsers.get(userId) ?? new Set();

  sockets.add(socketId);

  onlineUsers.set(userId, sockets);
}
```

When a socket disconnects:

```
function removeUserSocket(userId: string, socketId: string) {
  const sockets = onlineUsers.get(userId);

  if (!sockets) return;

  sockets.delete(socketId);

  if (sockets.size === 0) {
    onlineUsers.delete(userId);
  }
}
```

### **14.2 Why Use a Set of Socket IDs?**

A user may have multiple connections:

- One browser tab.
- Another browser tab.
- Mobile application.
- Desktop application.

If one connection disconnects, the user should not automatically become offline while another connection remains active.

### **14.3 Broadcast Presence**

```
io.emit("user-online", {
  userId,
});
```

When the final connection closes:

```
io.emit("user-offline", {
  userId,
});
```

### **14.4 Important: Presence Is Not the Same as Socket Connectivity**

A socket connection does not necessarily mean a person is actively using the application.

For production presence systems, consider:

- Heartbeats.
- Last-seen timestamps.
- Multiple active sessions.
- Grace periods for temporary network failures.
- Server restarts.
- Multiple backend instances.

Presence is usually best modeled as temporary, distributed state rather than a permanent database boolean.

## **15. Typing Indicators**

Typing indicators show that another user is currently composing a message.

Example:

```
User A: Online
User B is typing...
```

### **15.1 Client Emits Typing Event**

```
socket.emit("typing-started", {
  conversationId: "conversation-101",
});
```

When the user stops typing:

```
socket.emit("typing-stopped", {
  conversationId: "conversation-101",
});
```

### **15.2 Server Broadcasts the Event**

```
socket.on("typing-started", async (data) => {
  const userId = socket.data.user.id;

  const allowed = await isConversationMember(
    userId,
    data.conversationId
  );

  if (!allowed) return;

  socket.to(`conversation:${data.conversationId}`).emit(
    "user-typing",
    {
      userId,
      conversationId: data.conversationId,
    }
  );
});
```

### **15.3 Receive Typing Event**

```
socket.on("user-typing", (data) => {
  console.log(`${data.userId} is typing`);
});
```

### **15.4 Why Use Debouncing or Throttling?**

Imagine the user types 20 characters.

You do not want to emit 20 unnecessary events.

Instead:

- Emit `typing-started` once.
- Emit `typing-stopped` after a short period of inactivity.

This reduces event traffic.

## **16. Read Receipts and Delivery Receipts**

Read receipts indicate whether a message has been read by its recipient.

Example:

```
Sent       ✓
Delivered  ✓✓
Read       ✓✓ (highlighted)
```

### **16.1 Message Status Lifecycle**

```
Message Created
      |
      v
    SENT
      |
      v
  DELIVERED
      |
      v
     READ
```

These states are application-level concepts. Socket.IO itself does not automatically manage them.

### **16.2 Mark Message as Read**

Client:

```
socket.emit("message-read", {
  messageId: "message-101",
  conversationId: "conversation-101",
});
```

Server:

```
socket.on("message-read", async (data) => {
  const userId = socket.data.user.id;

  await markMessageAsRead({
    messageId: data.messageId,
    conversationId: data.conversationId,
    userId,
  });

  socket.to(`conversation:${data.conversationId}`).emit(
    "message-read-update",
    {
      messageId: data.messageId,
      userId,
    }
  );
});
```

The server must verify that the user is allowed to read the message and update its persistent read state.

### **16.3 Delivered vs Read**

| Status    | Meaning                                                                                               |
| --------- | ----------------------------------------------------------------------------------------------------- |
| Sent      | Server accepted and persisted the message                                                             |
| Delivered | Message reached the intended recipient's application or device, according to your delivery definition |
| Read      | Recipient opened or acknowledged the message as read                                                  |

Do not treat a successful Socket.IO emit as proof that a recipient read a message.

## **17. Redis Adapter and Horizontal Scaling**

Now let's study an important production architecture problem.

Suppose your chat application is running on one Socket.IO server.

```
             Clients
                |
                v
          Socket Server
                |
                v
              Rooms
```

Everything works.

Now traffic increases, so you deploy two Socket.IO servers.

```
              Clients
                 |
           Load Balancer
             /       \
            v         v
        Server 1   Server 2
```

Suppose:

- User A is connected to Server 1.
- User B is connected to Server 2.
- Both are in the same conversation room.

User A sends a message.

If Server 1 only broadcasts to its local connections, User B may not receive the message.

This is where a shared adapter becomes important.

### **17.1 What Is Redis Adapter?**

The Socket.IO Redis Adapter allows multiple Socket.IO servers to coordinate broadcasting using Redis Pub/Sub.

[Using multiple nodes | Socket.IO](https://images.openai.com/static-rsc-4/7mNoYraHI6AvUwFmAlw0sjqcLLXSPUync9nEU-KGn-OdP9Cnqhiz5pgFRp7Ys4CJZZlsyFpUEtGtdHZ1BHpLIHQGmwEFDwNXyeONmfccTwQfJkOftDmyrkgoJzXKmdgD9hOVnBJ9LBhJrUWPPHR9ONezlJQRJB7BJd5Xa1_wM8E?purpose=inline)

[socket.io](https://socket.io/docs/v3/using-multiple-nodes/?utm_source=chatgpt.com)

```
              Clients
                 |
           Load Balancer
             /       \
            v         v
        Server 1   Server 2
            |         |
            +----+----+
                 |
                 v
               Redis
                 |
                 v
        Cross-server broadcasts
```

Now when Server 1 broadcasts to a room, the Redis Adapter can distribute the broadcast to Server 2.

### **17.2 Install Redis Adapter**

```
npm install @socket.io/redis-adapter redis
```

### **17.3 Configure Redis Adapter**

```
import { createClient } from "redis";

import { Server } from "socket.io";

import { createServer } from "http";

const httpServer = createServer();

const io = new Server(httpServer);

const pubClient = createClient({
  url: process.env.REDIS_URL,
});

const subClient = pubClient.duplicate();

await Promise.all([
  pubClient.connect(),
  subClient.connect(),
]);

const { createAdapter } = await import("@socket.io/redis-adapter");

io.adapter(createAdapter(pubClient, subClient));

io.on("connection", (socket) => {
  console.log("Connected:", socket.id);
});

httpServer.listen(4000);
```

In a real project, configure Redis error handling, connection shutdown, TLS where appropriate, and startup health checks.

### **17.4 Important Redis Adapter Limitation**

The classic Redis Pub/Sub adapter supports cross-server broadcasting, but it does not persist packets for Socket.IO connection state recovery.

If you need connection-state recovery across servers, consider an adapter that supports it, such as the Redis Streams adapter, and verify the feature compatibility for your selected versions.

Also remember that Redis Pub/Sub is not a durable message queue.

For durable business events, use a persistent database or message broker such as RabbitMQ or Kafka.

[image](https://www.google.com/s2/favicons?domain=https://socket.io\&sz=32)

socket.io+1

### **17.5 Sticky Sessions**

If Socket.IO uses HTTP long polling, requests from the same client may need to reach the same server instance during a session.

This is why sticky sessions may be required behind a load balancer.

If you configure WebSocket-only transport, the transport behavior differs, but you lose Socket.IO's HTTP long-polling fallback.

The Redis Adapter does not automatically remove the need for sticky sessions when long polling is enabled.

## **18. WebSockets with Next.js 16**

Now let's understand how to integrate Socket.IO with Next.js.

There are two common architectures.

### **18.1 Recommended: Separate Socket.IO Server**

For larger applications, a clean architecture is:

```
              Next.js Application
                     |
                     v
              Socket.IO Client
                     |
                     v
             Separate Socket Server
                     |
                     v
                  MongoDB
```

Benefits:

- Next.js handles rendering and frontend routing.
- Socket.IO handles persistent real-time connections.
- Backend real-time services can scale independently.
- Deployment and infrastructure can be managed separately.

This is generally a good architecture for a serious chat application.

### **18.2 Create a Shared Socket Client**

Inside Next.js:

```
npm install socket.io-client
```

Create:

```
src/lib/socket.ts
```

```
import { io, type Socket } from "socket.io-client";

let socket: Socket | null = null;

export function getSocket() {
  if (!socket) {
    socket = io(
      process.env.NEXT_PUBLIC_SOCKET_URL!,
      {
        autoConnect: false,
      }
    );
  }

  return socket;
}
```

Environment variable:

```
NEXT_PUBLIC_SOCKET_URL=http://localhost:4000
```

### **18.3 Use the Socket in a Client Component**

```
"use client";

import { useEffect, useState } from "react";

import { getSocket } from "@/lib/socket";

export default function ChatPage() {
  const [connected, setConnected] = useState(false);

  useEffect(() => {
    const socket = getSocket();

    function onConnect() {
      setConnected(true);
    }

    function onDisconnect() {
      setConnected(false);
    }

    socket.on("connect", onConnect);
    socket.on("disconnect", onDisconnect);

    socket.connect();

    return () => {
      socket.off("connect", onConnect);
      socket.off("disconnect", onDisconnect);

      socket.disconnect();
    };
  }, []);

  return (
    <main>
      <h1>Chat Application</h1>

      <p>
        Status: {connected ? "Connected" : "Disconnected"}
      </p>
    </main>
  );
}
```

For a single-page chat application, a shared provider can manage the socket lifecycle so that changing pages does not unnecessarily disconnect and reconnect the socket.

### **18.4 Can Socket.IO Run Inside a Next.js Route Handler?**

A Route Handler is designed around HTTP request-response handling.

A persistent Socket.IO server needs a long-lived HTTP server that can handle connection upgrades.

These are different execution models.

You should not assume that adding a Socket.IO instance inside a normal `route.ts` file creates a reliable persistent WebSocket server.

### **18.5 What About a Custom Next.js Server?**

You can create a custom Node.js server that integrates Next.js with Socket.IO.

Conceptually:

```
             Custom Node.js Server
                       |
               +-------+-------+
               |               |
               v               v
            Next.js         Socket.IO
            Handler          Server
```

This can be useful for certain self-hosted deployments.

However, Next.js officially recommends using a custom server only when the integrated server cannot meet your requirements. Custom servers can remove important optimizations, and they cannot be combined with Next.js standalone output mode in the documented configuration.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



**My recommendation:** Keep your Socket.IO backend separate from Next.js unless you have a specific reason to integrate them into one custom server.

## **19. WebSockets vs Socket.IO vs SSE**

These technologies are often confused.

Let's compare them.

[Polling Vs WebSockets Vs SSE. First off, Happy new year 🥳🥳. | by Adewale Ayomide | Medium](https://images.openai.com/static-rsc-4/hZMU4XNa3mjEG6hEw3skrhnlwu-cGOYUqAfFpjfy_lUp3P-6hZ2uFEN82e2dDf5Vk0687fMAILTNomFWZzRKLfbjfAHSc9N9Tr0uY01Y_UsybXLUUrjv8ucKYTBYEw4XGcmZwMvTvaJmow-8skzEUI3aa9v-DIkAiPByn4NpI2Q?purpose=inline)

[medium.com](https://medium.com/%40adewalemaxwell11/polling-vs-websockets-vs-sse-245b899c2e60?utm_source=chatgpt.com)

| Feature               | WebSocket                        | Socket.IO                       | SSE                                       |
| --------------------- | -------------------------------- | ------------------------------- | ----------------------------------------- |
| Type                  | Protocol                         | Library                         | HTTP streaming mechanism                  |
| Direction             | Bidirectional                    | Bidirectional                   | Server to client                          |
| Persistent connection | Yes                              | Yes, while connected            | Yes                                       |
| Reconnection          | Implement yourself               | Built-in support                | Browser EventSource reconnects by default |
| Rooms                 | No built-in rooms                | Yes                             | No built-in rooms                         |
| Broadcasting          | Implement yourself               | Built-in APIs                   | Implement on server                       |
| Acknowledgements      | Application-level implementation | Built-in event acknowledgements | Application-level implementation          |
| Transport             | WebSocket                        | WebSocket or HTTP long polling  | HTTP                                      |
| Best suited for       | Low-level real-time messaging    | Feature-rich real-time apps     | Live feeds and notifications              |

### **19.1 When to Use WebSockets?**

Use WebSockets when:

- You need bidirectional communication.
- You want low-level protocol control.
- You are building a custom real-time protocol.
- You do not need Socket.IO's additional abstractions.

### **19.2 When to Use Socket.IO?**

Use Socket.IO when:

- You are building chat applications.
- You need rooms and broadcasting.
- You need automatic reconnection.
- You need event acknowledgements.
- You want a higher-level event-based API.

### **19.3 When to Use SSE?**

SSE is useful when the server needs to push updates to the browser, but the browser does not need a persistent bidirectional connection.

Examples:

- Live notifications.
- AI text streaming.
- Progress updates.
- Live activity feeds.
- Monitoring dashboards.

Example browser client:

```
const eventSource = new EventSource("/api/events");

eventSource.onmessage = (event) => {
  console.log(event.data);
};
```

The browser automatically reconnects after certain connection interruptions.

The server can send events through an HTTP streaming response.

[image](https://www.google.com/s2/favicons?domain=https://developer.mozilla.org\&sz=32)

MDN+1

## **20. Production Deployment and Security**

Real-time applications require special deployment and security considerations.

### **20.1 Use Secure WebSocket Connections**

Use:

```
wss://example.com
```

Avoid exposing production WebSocket services over unencrypted `ws://` connections.

### **20.2 Validate Origins**

Restrict connections to trusted origins.

Example:

```
const io = new Server(httpServer, {
  cors: {
    origin: ["https://example.com"],
    methods: ["GET", "POST"],
  },
});
```

CORS configuration alone is not a substitute for proper WebSocket origin validation and authentication. Configure the server's handshake-origin policy explicitly.

### **20.3 Validate Every Message**

Treat all socket messages as untrusted input.

Use Zod:

```
import { z } from "zod";

const messageSchema = z.object({
  conversationId: z.string().min(1),
  content: z.string().trim().min(1).max(2000),
});
```

Validate before processing:

```
socket.on("send-message", async (data, callback) => {
  const result = messageSchema.safeParse(data);

  if (!result.success) {
    callback({
      success: false,
      error: "Invalid message",
    });

    return;
  }

  // Process validated message
});
```

### **20.4 Implement Rate Limiting**

For example:

```
Maximum 30 messages per minute per user
```

The actual limit should depend on your application's use case.

Use per-user limits where possible, and consider IP-based limits for unauthenticated connections.

### **20.5 Limit Message Size**

Prevent excessively large messages from exhausting memory or CPU resources.

### **20.6 Handle Disconnections**

Network disconnections are normal.

Implement:

- Reconnection strategies.
- Heartbeats.
- Message resynchronization.
- Connection cleanup.
- Session validation.

### **20.7 Avoid Sensitive Logging**

Do not log:

- JWT access tokens.
- Session IDs.
- Passwords.
- Private message contents unnecessarily.
- Sensitive personal data.

OWASP specifically recommends origin validation, authentication, message-level authorization, payload limits, rate limiting, heartbeat monitoring, and careful logging for WebSocket applications.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub+1



### **20.8 Reverse Proxy Configuration**

If you use NGINX in front of a WebSocket server, configure WebSocket upgrade forwarding.

Example:

```
location /socket.io/ {
    proxy_pass http://socket_server;

    proxy_http_version 1.1;

    proxy_set_header Upgrade $http_upgrade;

    proxy_set_header Connection "upgrade";

    proxy_set_header Host $host;

    proxy_read_timeout 3600s;
}
```

The proxy and load balancer must support long-lived connections and appropriate timeouts.

## **21. Common Mistakes in Real-Time Applications**

1. Creating a new socket connection every time a React component renders.
2. Forgetting to remove event listeners.
3. Trusting client-provided user IDs.
4. Allowing users to join private rooms without authorization.
5. Broadcasting private messages to all users.
6. Assuming `socket.emit()` means the message was saved.
7. Not handling reconnection.
8. Keeping online status only in a local in-memory Map when running multiple servers.
9. Not configuring Redis or another shared coordination system for cross-instance broadcasting.
10. Sending large messages without limits.
11. Using HTTP polling when a more appropriate real-time transport is available.
12. Assuming Socket.IO is directly compatible with every native WebSocket client.
13. Putting all business logic inside socket event handlers.
14. Not persisting chat messages in a database.
15. Forgetting to handle expired sessions and revoked permissions.

## **22. Interview Questions and Answers**

### **Basic Level**

**Q1. What is WebSocket?**

WebSocket is a communication protocol that provides persistent, bidirectional communication between a client and server.

**Q2. What is full-duplex communication?**

Full-duplex communication allows both sides to send and receive data simultaneously.

**Q3. What is the difference between HTTP and WebSocket?**

HTTP commonly uses request-response communication, whereas WebSocket establishes a persistent connection that supports bidirectional messaging.

**Q4. What is the WebSocket handshake?**

It is the HTTP-based process through which a client requests that the server upgrade the connection to the WebSocket protocol.

**Q5. What is Socket.IO?**

Socket.IO is a JavaScript library for real-time, bidirectional, event-based communication.

**Q6. What is the difference between&#x20;**`ws://`**&#x20;and&#x20;**`wss://`**?**

`ws://` is unencrypted WebSocket communication, whereas `wss://` uses TLS encryption.

**Q7. What is a Socket.IO event?**

An event is a named message that a client or server emits and another party listens for.

### **Intermediate Level**

**Q8. What is the difference between WebSocket and Socket.IO?**

WebSocket is a communication protocol. Socket.IO is a higher-level library that provides events, reconnection, rooms, acknowledgements, and transport fallback.

**Q9. What is Polling?**

Polling is the repeated sending of requests by a client to check whether new data is available.

**Q10. What is Long Polling?**

Long Polling keeps an HTTP request open until the server has data to return or a timeout occurs.

**Q11. What is a Room in Socket.IO?**

A Room is a group of sockets that can receive targeted broadcasts.

**Q12. What is a Namespace?**

A Namespace is a logical communication channel within a Socket.IO server.

**Q13. What is the difference between Room and Namespace?**

A Namespace separates communication channels, while a Room groups sockets within a namespace.

**Q14. What is Broadcasting?**

Broadcasting means sending an event to multiple connected clients.

**Q15. What is a Socket.IO acknowledgement?**

An acknowledgement is a callback mechanism that allows the sender to receive confirmation from the receiver's event handler.

**Q16. How do you authenticate Socket.IO connections?**

You can use Socket.IO middleware to validate a session or token during the connection process.

### **Advanced Level**

**Q17. What is the Redis Adapter?**

It allows multiple Socket.IO server instances to coordinate room broadcasts and other supported inter-server operations using Redis Pub/Sub.

**Q18. Why is Redis useful for scaling Socket.IO?**

It allows events emitted on one server instance to be propagated to sockets connected to other instances.

**Q19. What are sticky sessions?**

Sticky sessions ensure that requests belonging to a particular session are routed to the same backend server when required by the transport, especially HTTP long polling.

**Q20. How do you maintain online status for multiple devices?**

Track multiple active socket connections per user and mark the user offline only when all their connections have disconnected, using shared state in a distributed deployment.

**Q21. What is the difference between delivered and read receipts?**

Delivered indicates that the recipient's application or device received the message according to the application's definition. Read indicates that the recipient has acknowledged viewing it.

**Q22. How do you prevent unauthorized room access?**

Authenticate the user and verify conversation membership or permissions on the server before joining the room.

**Q23. Can Socket.IO run inside a Next.js Route Handler?**

A normal Route Handler is not a persistent WebSocket server. A separate Socket.IO server or an appropriately configured custom Node.js server is generally a better approach.

**Q24. What is the difference between WebSockets and SSE?**

WebSockets support bidirectional communication, whereas SSE provides a server-to-client event stream over HTTP.

**Q25. How do you prevent duplicate messages after reconnection?**

Use client-generated idempotency IDs, persist messages, and deduplicate on the server using unique constraints or equivalent logic.

**Q26. How do you scale a Socket.IO application?**

Use multiple server instances, a load balancer, an appropriate Socket.IO adapter such as Redis, and shared persistence for important application state.

## **23. Practical Assignment – Build a Real-Time Chat Application**

Hands-on project

Build a real-time chat application similar to WhatsApp Web.

### **Technology Stack**

| Component               | Technology              |
| ----------------------- | ----------------------- |
| Frontend                | Next.js 16 + TypeScript |
| Backend                 | Node.js + Express       |
| Real-time communication | Socket.IO               |
| Database                | MongoDB                 |
| Authentication          | JWT or secure session   |
| Shared state / Adapter  | Redis                   |
| Validation              | Zod                     |
| Testing                 | Vitest + Playwright     |

### **Task 1 – Authentication**

Implement:

- User registration.
- Login.
- Logout.
- JWT or session authentication.
- Socket connection authentication.

### **Task 2 – One-to-One Chat**

Implement:

- User search.
- Create conversation.
- Send messages.
- Receive messages.
- Load previous messages.
- Persist messages in MongoDB.

### **Task 3 – Group Chat**

Implement:

- Create group.
- Add members.
- Remove members.
- Join conversation rooms.
- Send group messages.
- Authorize membership changes.

### **Task 4 – Real-Time Features**

Implement:

- Online/offline presence.
- Typing indicators.
- Delivered receipts.
- Read receipts.
- Message notifications.

### **Task 5 – Advanced Features**

Implement:

- Message editing.
- Message deletion.
- Reply to message.
- Pagination.
- Attachment metadata.
- Reconnection and synchronization.

### **Task 6 – Scaling**

Implement:

- Redis Adapter.
- Multiple Socket.IO instances.
- Load balancing.
- Shared presence state.

### **Task 7 – Security**

Implement:

- Origin validation.
- Message validation.
- Authentication and authorization.
- Rate limiting.
- Message size limits.
- Safe logging.

### **Assignment Completion Checklist**

**Project progress**

# **0%**

0 of 16 tasks completed

Create Next.js frontend

Create Node.js Socket.IO server

Connect frontend and backend

Implement user authentication

Authenticate socket connections

Implement one-to-one chat

Implement group chat and rooms

Persist messages in MongoDB

Load message history with pagination

Implement online/offline presence

Implement typing indicators

Implement delivered and read receipts

Implement reconnection and synchronization

Configure Redis Adapter

Implement message validation and rate limiting

Deploy the frontend and real-time backend

## **24. Quick Revision Notes**

| Concept                   | Remember                                     |
| ------------------------- | -------------------------------------------- |
| Real-Time Communication   | Low-delay information exchange               |
| Polling                   | Repeated HTTP requests                       |
| Long Polling              | Server holds request until data is available |
| WebSocket                 | Persistent bidirectional protocol            |
| Full-Duplex               | Both sides communicate simultaneously        |
| Handshake                 | HTTP-based protocol upgrade                  |
| `ws://`                   | Unencrypted WebSocket                        |
| `wss://`                  | TLS-protected WebSocket                      |
| Socket.IO                 | Event-based real-time library                |
| `socket.emit()`           | Send to current socket                       |
| `io.emit()`               | Broadcast to namespace                       |
| `socket.broadcast.emit()` | Broadcast excluding sender                   |
| Room                      | Group of sockets                             |
| Namespace                 | Separate communication channel               |
| Middleware                | Connection-level processing                  |
| Acknowledgement           | Event-handler confirmation                   |
| Presence                  | Online/offline state                         |
| Typing Indicator          | Shows active message composition             |
| Read Receipt              | Indicates message has been read              |
| Redis Adapter             | Cross-server communication                   |
| Sticky Sessions           | Consistent routing when required             |
| SSE                       | Server-to-client event streaming             |

### **The 8 Most Important Things to Remember**

1. WebSockets provide persistent, bidirectional communication.
2. Socket.IO is a library built on top of its own protocol, not simply a WebSocket wrapper.
3. Rooms are used to group users; namespaces separate communication channels.
4. Authentication at connection time does not replace authorization for individual events.
5. Persist important messages before broadcasting them.
6. Redis helps multiple Socket.IO servers communicate.
7. Next.js and Socket.IO can be deployed separately.
8. Real-time systems need reconnection, synchronization, security, and observability.

## **25. Phase 31 Completion Checklist**

**Learning progress**

# **0/15**

I understand real-time communication.

I understand HTTP, Polling and Long Polling.

I understand WebSockets and full-duplex communication.

I can explain the WebSocket handshake.

I can create a native Node.js WebSocket server.

I understand Socket.IO and its features.

I understand events, broadcasting and acknowledgements.

I understand Rooms and Namespaces.

I can authenticate Socket.IO connections.

I understand chat architecture and message persistence.

I can implement presence, typing indicators and read receipts.

I understand Redis Adapter and horizontal scaling.

I can integrate Socket.IO with Next.js.

I can compare WebSockets, Socket.IO and SSE.

I understand production security and deployment.

## **26. Next Phase Preview**

Phase 32 – Advanced Backend Architecture

# **Caching Strategies, Redis and Distributed Caching**

In the next phase, we will study how caching improves application performance and reduces database load.

**Topics planned:**

1. What Is Caching?
2. Cache Hit vs Cache Miss.
3. Browser, CDN, Application and Database Caching.
4. In-Memory Caching.
5. Redis Architecture.
6. Redis Data Types.
7. Redis with Node.js.
8. Redis with Next.js.
9. Cache-Aside Pattern.
10. Read-Through and Write-Through Caching.
11. Write-Behind Caching.
12. Cache Invalidation.
13. TTL and Expiration.
14. Cache Stampede and Cache Penetration.
15. Distributed Caching.
16. Redis Pub/Sub.
17. Redis Transactions and Lua Scripts.
18. Redis Persistence and High Availability.
19. Caching Strategies in Microservices.
20. Performance Monitoring and Best Practices.

**Phase 31 is complete.** When you're ready, say **“OK, Phase 32”** and we'll continue with detailed notes.

### **References and Official Documentation**

1. [RFC 6455 – The WebSocket Protocol](https://www.rfc-editor.org/info/rfc6455/?utm_source=chatgpt.com).
2. [MDN – WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API?utm_source=chatgpt.com).
3. [Socket.IO – Documentation](https://socket.io/docs/v4/?utm_source=chatgpt.com).
4. [Socket.IO – Rooms](https://socket.io/docs/v4/rooms/?utm_source=chatgpt.com).
5. [Socket.IO – Redis Adapter](https://socket.io/docs/v4/redis-adapter?utm_source=chatgpt.com).
6. [Socket.IO – Connection State Recovery](https://sockjs.com/docs/v4/connection-state-recovery?utm_source=chatgpt.com).
7. [Next.js – Custom Server](https://nextjs.org/docs/app/guides/custom-server?utm_source=chatgpt.com).
8. [Next.js – Self-Hosting](https://nextjs.org/docs/pages/guides/self-hosting?utm_source=chatgpt.com).
9. [MDN – Server-Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events?utm_source=chatgpt.com).
10. [OWASP – WebSocket Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/WebSocket_Security_Cheat_Sheet.html?utm_source=chatgpt.com).