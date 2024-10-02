
# Chat Model Documentation

This file defines the Mongoose schema for the chat model, representing chat data in the MongoDB database. The schema outlines the structure and types of data associated with a chat, including support for group chats.

## 1. Imports

```javascript
const mongoose = require("mongoose");
```
### Description:
- **mongoose**: Library for MongoDB object modeling, providing a schema-based solution for modeling application data.

## 2. Chat Schema Definition

```javascript
const chatModel = mongoose.Schema(
  {
    chatName: { type: String, trim: true },
    isGroupChat: { type: Boolean, default: false },
    users: [{ type: mongoose.Schema.Types.ObjectId, ref: "User" }],
    latestMessage: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Message",
    },
    groupAdmin: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
  },
  { timestamps: true }
);
```
### Description:
- **Schema Fields**:
  - `chatName`: (String) The name of the chat, trimmed of whitespace.
  - `isGroupChat`: (Boolean) Indicates if the chat is a group chat; defaults to `false`.
  - `users`: (Array of ObjectId) An array referencing `User` documents, representing participants in the chat.
  - `latestMessage`: (ObjectId) References the `Message` document representing the latest message in the chat.
  - `groupAdmin`: (ObjectId) References the `User` document representing the admin of the group chat.
- **Timestamps**: Automatically adds `createdAt` and `updatedAt` fields to the schema.

## 3. Chat Model Creation

```javascript
const Chat = mongoose.model("Chat", chatModel);
```
### Description:
- **Model**: Creates a Mongoose model named `Chat` based on the defined schema, allowing for interaction with the `chats` collection in the MongoDB database.

## 4. Module Export

```javascript
module.exports = Chat;
```
### Description:
- **Export**: Exports the `Chat` model for use in other parts of the application, enabling the ability to create, read, update, and delete chat data.
