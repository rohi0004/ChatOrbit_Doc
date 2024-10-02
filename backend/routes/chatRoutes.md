
# Chat Routes Documentation

This file defines the routing for various chat-related operations using Express.js. It utilizes middleware for authentication and controller functions for handling the business logic related to chat access, fetching chats, and managing group chats.

## 1. Imports

```javascript
const express = require("express");
const {
  accessChat,
  fetchChats,
  createGroupChat,
  removeFromGroup,
  addToGroup,
  renameGroup,
} = require("../controllers/chatControllers");
const { protect } = require("../middleware/authMiddleware");
```
### Description:
- **express**: Framework for building the server.
- **Chat Controller Functions**:
  - `accessChat`: Function to handle accessing an existing chat.
  - `fetchChats`: Function to retrieve all chats for a user.
  - `createGroupChat`: Function to create a new group chat.
  - `removeFromGroup`: Function to remove a user from a group chat.
  - `addToGroup`: Function to add a user to a group chat.
  - `renameGroup`: Function to rename an existing group chat.
- **Middleware**:
  - `protect`: Middleware that protects routes by ensuring the user is authenticated.

## 2. Router Initialization

```javascript
const router = express.Router();
```
### Description:
- **Router Instance**: Initializes an Express router instance to define chat-related routes.

## 3. Route Definitions

### 3.1 Access Chat

```javascript
router.route("/").post(protect, accessChat);
```
- **POST /api/chat/**: 
  - **Purpose**: Access an existing chat.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.2 Fetch Chats

```javascript
router.route("/").get(protect, fetchChats);
```
- **GET /api/chat/**: 
  - **Purpose**: Fetch all chats for the authenticated user.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.3 Create Group Chat

```javascript
router.route("/group").post(protect, createGroupChat);
```
- **POST /api/chat/group**: 
  - **Purpose**: Create a new group chat.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.4 Rename Group

```javascript
router.route("/rename").put(protect, renameGroup);
```
- **PUT /api/chat/rename**: 
  - **Purpose**: Rename an existing group chat.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.5 Remove From Group

```javascript
router.route("/groupremove").put(protect, removeFromGroup);
```
- **PUT /api/chat/groupremove**: 
  - **Purpose**: Remove a user from a group chat.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.6 Add To Group

```javascript
router.route("/groupadd").put(protect, addToGroup);
```
- **PUT /api/chat/groupadd**: 
  - **Purpose**: Add a user to a group chat.
  - **Middleware**: Requires authentication via the `protect` middleware.

## 4. Module Export

```javascript
module.exports = router;
```
### Description:
- **Export**: Exports the router instance for use in other parts of the application, allowing the defined routes to be accessible.
