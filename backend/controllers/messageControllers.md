
# Message Controller Documentation

This file defines the controller functions for managing messages in the application. It includes functions for retrieving all messages in a chat and sending new messages, handling user interactions within those chats.

## 1. Imports

```javascript
const asyncHandler = require("express-async-handler");
const Message = require("../models/messageModel");
const User = require("../models/userModel");
const Chat = require("../models/chatModel");
```

### Description:
- **asyncHandler**: Middleware to handle asynchronous route handlers and simplify error handling in Express.
- **Message**: The message model used to interact with message data in the MongoDB database.
- **User**: The user model used to interact with user data in the MongoDB database.
- **Chat**: The chat model used to interact with chat data in the MongoDB database.

## 2. Get All Messages

```javascript
const allMessages = asyncHandler(async (req, res) => {
  try {
    const messages = await Message.find({ chat: req.params.chatId })
      .populate("sender", "name pic email")
      .populate("chat");
    res.json(messages);
  } catch (error) {
    res.status(400);
    throw new Error(error.message);
  }
});
```

### Description:
- **Function Purpose**: Retrieves all messages for a specific chat.
- **Parameters**:
  - `chatId`: The ID of the chat for which to fetch messages (extracted from `req.params`).
- **Logic**:
  - Finds messages related to the specified chat and populates sender details (name, picture, email) and chat information.
  - Returns the messages as a JSON response.

## 3. Create New Message

```javascript
const sendMessage = asyncHandler(async (req, res) => {
  const { content, chatId } = req.body;

  if (!content || !chatId) {
    console.log("Invalid data passed into request");
    return res.sendStatus(400);
  }

  var newMessage = {
    sender: req.user._id,
    content: content,
    chat: chatId,
  };

  try {
    var message = await Message.create(newMessage);

    message = await message.populate("sender", "name pic").execPopulate();
    message = await message.populate("chat").execPopulate();
    message = await User.populate(message, {
      path: "chat.users",
      select: "name pic email",
    });

    await Chat.findByIdAndUpdate(req.body.chatId, { latestMessage: message });

    res.json(message);
  } catch (error) {
    res.status(400);
    throw new Error(error.message);
  }
});
```

### Description:
- **Function Purpose**: Creates a new message in a chat.
- **Parameters**:
  - `content`: The content of the message being sent.
  - `chatId`: The ID of the chat to which the message belongs.
- **Logic**:
  - Validates the presence of `content` and `chatId`.
  - Constructs a new message object with the sender's ID, content, and chat ID.
  - Creates the message in the database, populates sender and chat details, and updates the latest message in the chat.
  - Returns the newly created message as a JSON response.

## 4. Module Export

```javascript
module.exports = { allMessages, sendMessage };
```

### Description:
- **Export**: Exports the `allMessages` and `sendMessage` functions for use in the routes, allowing for organized and modular handling of message-related operations.
