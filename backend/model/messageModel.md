
# Message Model Documentation

This file defines the Mongoose schema for the message model, representing message data in the MongoDB database. The schema outlines the structure and types of data associated with a message, including sender, content, chat association, and read status.

## 1. Imports

```javascript
const mongoose = require("mongoose");
```
### Description:
- **mongoose**: Library for MongoDB object modeling, providing a schema-based solution for modeling application data.

## 2. Message Schema Definition

```javascript
const messageSchema = mongoose.Schema(
  {
    sender: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
    content: { type: String, trim: true },
    chat: { type: mongoose.Schema.Types.ObjectId, ref: "Chat" },
    readBy: [{ type: mongoose.Schema.Types.ObjectId, ref: "User" }],
  },
  { timestamps: true }
);
```
### Description:
- **Schema Fields**:
  - `sender`: (ObjectId) References the `User` document representing the user who sent the message.
  - `content`: (String) The content of the message, trimmed of whitespace.
  - `chat`: (ObjectId) References the `Chat` document to which the message belongs.
  - `readBy`: (Array of ObjectId) An array referencing `User` documents representing users who have read the message.
- **Timestamps**: Automatically adds `createdAt` and `updatedAt` fields to the schema.

## 3. Message Model Creation

```javascript
const Message = mongoose.model("Message", messageSchema);
```
### Description:
- **Model**: Creates a Mongoose model named `Message` based on the defined schema, allowing for interaction with the `messages` collection in the MongoDB database.

## 4. Module Export

```javascript
module.exports = Message;
```
### Description:
- **Export**: Exports the `Message` model for use in other parts of the application, enabling the ability to create, read, update, and delete message data.
