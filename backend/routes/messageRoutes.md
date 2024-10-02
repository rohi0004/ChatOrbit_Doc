
# Message Routes Documentation

This file defines the routing for message-related operations using Express.js. It utilizes middleware for authentication and controller functions for handling the business logic related to message retrieval and sending.

## 1. Imports

```javascript
const express = require("express");
const {
  allMessages,
  sendMessage,
} = require("../controllers/messageControllers");
const { protect } = require("../middleware/authMiddleware");
```
### Description:
- **express**: Framework for building the server.
- **Message Controller Functions**:
  - `allMessages`: Function to retrieve all messages for a specific chat.
  - `sendMessage`: Function to send a new message in a chat.
- **Middleware**:
  - `protect`: Middleware that protects routes by ensuring the user is authenticated.

## 2. Router Initialization

```javascript
const router = express.Router();
```
### Description:
- **Router Instance**: Initializes an Express router instance to define message-related routes.

## 3. Route Definitions

### 3.1 Retrieve All Messages

```javascript
router.route("/:chatId").get(protect, allMessages);
```
- **GET /api/message/:chatId**: 
  - **Purpose**: Retrieve all messages for a specific chat identified by `chatId`.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.2 Send Message

```javascript
router.route("/").post(protect, sendMessage);
```
- **POST /api/message/**: 
  - **Purpose**: Send a new message in a chat.
  - **Middleware**: Requires authentication via the `protect` middleware.

## 4. Module Export

```javascript
module.exports = router;
```
### Description:
- **Export**: Exports the router instance for use in other parts of the application, allowing the defined routes to be accessible.
