# Socket.IO Complete Guide

A comprehensive guide to using Socket.IO for real-time communication between client and server.  
This document explains key concepts such as **emit**, **on**, **join**, **broadcast**, **rooms**, **namespaces**, and more, with practical code examples.

---

## 1. What is Socket.IO?

Socket.IO is a library for **real-time, bi-directional communication** between client and server. It works primarily over **WebSockets**, with fallback support for HTTP long-polling.

**Features:**
- Event-based communication (`emit` / `on`)
- Automatic reconnection
- Rooms and namespaces
- Middleware for authentication
- Broadcasting and targeted messaging

---

## 2. Basic Setup

### Server (Node.js + Express)

```javascript
import express from "express";
import { createServer } from "http";
import { Server } from "socket.io";

const app = express();
const server = createServer(app);

const io = new Server(server, {
  cors: { origin: "http://localhost:5173", methods: ["GET", "POST"] }
});

io.on("connection", (socket) => {
  console.log("User connected:", socket.id);

  socket.on("disconnect", () => {
    console.log("User disconnected:", socket.id);
  });
});

server.listen(3000);


### Client 

import { io } from "socket.io-client";
const socket = io("http://localhost:3000");
```
---

## 3. Events: emit and on

### emit

Sends an event from client → server or server → client.

### Client → Server:

socket.emit("message", { text: "Hello" });


### Server listens:

socket.on("message", (data) => {
  console.log(data.text);
});

### on

Listens for a custom event.

### Server → Client:

io.on("connection", (socket) => {
  socket.emit("welcome", "Welcome user!");
});


### Client listens:

socket.on("welcome", (msg) => {
  console.log(msg);
});
```
---

## 4. Broadcasting

socket.broadcast.emit
Sends to all clients except the sender.

socket.on("message", (data) => {
  socket.broadcast.emit("receive-message", data);
});

### io.emit

Sends to all connected clients (including sender).

io.emit("announcement", "Server will restart soon!");
```
---

## 5. Rooms

Rooms allow grouping of sockets to target messages.

### Join a room
socket.join("room1");

### Send to a room
io.to("room1").emit("receive-message", "Hello Room 1");


### Send to room except sender
socket.to("room1").emit("receive-message", "Hi everyone except me!");
```
---

## 6. to and in

### Send to a specific room
io.to("room1").emit("room-event", "Only room1 users see this");

### Send to a specific user (by socket ID)
io.to(socket.id).emit("private-message", "Hello personally");

### in (alias of to)
io.in("room1").emit("message", "Same as to()");
```
---

## 7. broadcast

socket.broadcast sends to everyone except sender.
Can also target specific rooms.

socket.broadcast.to("room1").emit("room-message", "Hi room1 (except me)");
