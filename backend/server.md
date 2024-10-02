
# Express and Socket.IO Chat Server Documentation

This document describes the implementation of a chat server using Express.js and Socket.IO. The server handles user management, chat functionality, and real-time messaging.

## 1. Imports and Configuration

```javascript
const express = require("express");
const connectDB = require("./config/db");
const dotenv = require("dotenv");
const userRoutes = require("./routes/userRoutes");
const chatRoutes = require("./routes/chatRoutes");
const messageRoutes = require("./routes/messageRoutes");
const { notFound, errorHandler } = require("./middleware/errorMiddleware");
const path = require("path");
```

### Description:
- **Imports**:
  - `express`: Framework for building the server.
  - `connectDB`: Function for establishing a database connection.
  - `dotenv`: Loads environment variables from a `.env` file.
  - Route handlers for user, chat, and message functionalities.
  - Middleware for handling errors.

## 2. Environment Configuration and Database Connection

```javascript
dotenv.config();
connectDB();
```

### Description:
- **dotenv.config()**: Loads environment variables, making them accessible via `process.env`.
- **connectDB()**: Connects to the database, enabling interaction with data.

## 3. Creating the Express Application

```javascript
const app = express();
app.use(express.json()); // to accept JSON data
```

### Description:
- **Creating the App**: Initializes an Express application instance.
- **JSON Parsing**: Middleware that parses incoming JSON requests, enabling the server to handle JSON data from clients.

## 4. Route Definitions

```javascript
app.use("/api/user", userRoutes);
app.use("/api/chat", chatRoutes);
app.use("/api/message", messageRoutes);
```

### Description:
- **API Routes**:
  - `/api/user`: Handled by `userRoutes`.
  - `/api/chat`: Handled by `chatRoutes`.
  - `/api/message`: Handled by `messageRoutes`.

## 5. Deployment Handling

```javascript
const __dirname1 = path.resolve();

if (process.env.NODE_ENV === "production") {
  app.use(express.static(path.join(__dirname1, "/frontend/build")));

  app.get("*", (req, res) =>
    res.sendFile(path.resolve(__dirname1, "frontend", "build", "index.html"))
  );
} else {
  app.get("/", (req, res) => {
    res.send("API is running..");
  });
}
```

### Description:
- **Static File Serving**: 
  - In production, serves static files from the `frontend/build` directory and sends the `index.html` file for unmatched routes.
- **Development Mode**: Returns a message indicating the API is running when accessing the root URL.

## 6. Error Handling Middleware

```javascript
app.use(notFound);
app.use(errorHandler);
```

### Description:
- **Not Found Middleware**: Handles 404 errors for undefined routes.
- **Error Handler Middleware**: Catches and manages errors during request processing, providing a structured response.

## 7. Starting the Server

```javascript
const PORT = process.env.PORT;

const server = app.listen(
  PORT,
  console.log(`Server running on PORT ${PORT}...`.yellow.bold)
);
```

### Description:
- **Port Configuration**: Server listens on a port defined in the environment variables.
- **Server Initialization**: Starts the server and logs a message indicating the running port.

## 8. Socket.IO Integration

```javascript
const io = require("socket.io")(server, {
  pingTimeout: 60000,
  cors: {
    origin: "http://localhost:3000",
  },
});
```

### Description:
- **Socket.IO Setup**: Integrates Socket.IO with the Express server for real-time communication.
- **CORS Configuration**: Allows connections from `http://localhost:3000`, useful for development.

## 9. Socket.IO Event Handling

```javascript
io.on("connection", (socket) => {
  console.log("Connected to socket.io");
  
  socket.on("setup", (userData) => {
    socket.join(userData._id);
    socket.emit("connected");
  });

  socket.on("join chat", (room) => {
    socket.join(room);
    console.log("User Joined Room: " + room);
  });
  
  socket.on("typing", (room) => socket.in(room).emit("typing"));
  socket.on("stop typing", (room) => socket.in(room).emit("stop typing"));

  socket.on("new message", (newMessageReceived) => {
    var chat = newMessageReceived.chat;

    if (!chat.users) return console.log("chat.users not defined");

    chat.users.forEach((user) => {
      if (user._id == newMessageReceived.sender._id) return;

      socket.in(user._id).emit("message received", newMessageReceived);
    });
  });

  socket.off("setup", () => {
    console.log("USER DISCONNECTED");
    socket.leave(userData._id);
  });
});
```

### Description:
- **Connection Event**: Listens for new socket connections and logs a successful connection.
- **Setup Event**: Joins a room based on the user ID and emits a confirmation message.
- **Join Chat**: Allows clients to join specific chat rooms.
- **Typing Events**: Emits notifications for typing and stop typing to users in the room.
- **New Message Event**: Handles incoming messages and broadcasts them to other users in the chat, except the sender.
- **Disconnect Handling**: Logs when a user disconnects and leaves their associated room.

---

### Summary

This code establishes a backend server that integrates Express.js for API functionality and Socket.IO for real-time messaging. It handles routing, error management, and server startup, making it suitable for chat applications in both development and production environments.
