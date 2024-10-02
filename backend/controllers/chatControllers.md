
# Chat Controller Documentation

This file defines the controller functions for managing chat operations in the application. It includes functions for creating, fetching, and managing one-to-one and group chats, handling user interactions within those chats.

## 1. Imports

```javascript
const asyncHandler = require("express-async-handler");
const Chat = require("../models/chatModel");
const User = require("../models/userModel");
```

### Description:
- **asyncHandler**: Middleware to handle asynchronous route handlers and simplify error handling in Express.
- **Chat**: The chat model used to interact with chat data in the MongoDB database.
- **User**: The user model used to interact with user data in the MongoDB database.

## 2. Access Chat

```javascript
const accessChat = asyncHandler(async (req, res) => {
  const { userId } = req.body;

  if (!userId) {
    console.log("UserId param not sent with request");
    return res.sendStatus(400);
  }

  var isChat = await Chat.find({
    isGroupChat: false,
    $and: [
      { users: { $elemMatch: { $eq: req.user._id } } },
      { users: { $elemMatch: { $eq: userId } } },
    ],
  })
    .populate("users", "-password")
    .populate("latestMessage");

  isChat = await User.populate(isChat, {
    path: "latestMessage.sender",
    select: "name pic email",
  });

  if (isChat.length > 0) {
    res.send(isChat[0]);
  } else {
    var chatData = {
      chatName: "sender",
      isGroupChat: false,
      users: [req.user._id, userId],
    };

    try {
      const createdChat = await Chat.create(chatData);
      const FullChat = await Chat.findOne({ _id: createdChat._id }).populate(
        "users",
        "-password"
      );
      res.status(200).json(FullChat);
    } catch (error) {
      res.status(400);
      throw new Error(error.message);
    }
  }
});
```

### Description:
- **Function Purpose**: Creates or fetches a one-to-one chat between users.
- **Parameters**:
  - `userId`: The ID of the user to chat with.
- **Logic**:
  - Validates the presence of `userId`.
  - Checks for an existing chat between the current user and the specified user.
  - If no chat exists, creates a new chat and returns the chat data.

## 3. Fetch Chats

```javascript
const fetchChats = asyncHandler(async (req, res) => {
  try {
    Chat.find({ users: { $elemMatch: { $eq: req.user._id } } })
      .populate("users", "-password")
      .populate("groupAdmin", "-password")
      .populate("latestMessage")
      .sort({ updatedAt: -1 })
      .then(async (results) => {
        results = await User.populate(results, {
          path: "latestMessage.sender",
          select: "name pic email",
        });
        res.status(200).send(results);
      });
  } catch (error) {
    res.status(400);
    throw new Error(error.message);
  }
});
```

### Description:
- **Function Purpose**: Retrieves all chats for the authenticated user.
- **Logic**:
  - Fetches chats that include the current user in their user list.
  - Populates user, group admin, and latest message details.
  - Sorts chats by the latest update date and returns the results.

## 4. Create Group Chat

```javascript
const createGroupChat = asyncHandler(async (req, res) => {
  if (!req.body.users || !req.body.name) {
    return res.status(400).send({ message: "Please Fill all the fields" });
  }

  var users = JSON.parse(req.body.users);

  if (users.length < 2) {
    return res
      .status(400)
      .send("More than 2 users are required to form a group chat");
  }

  users.push(req.user);

  try {
    const groupChat = await Chat.create({
      chatName: req.body.name,
      users: users,
      isGroupChat: true,
      groupAdmin: req.user,
    });

    const fullGroupChat = await Chat.findOne({ _id: groupChat._id })
      .populate("users", "-password")
      .populate("groupAdmin", "-password");

    res.status(200).json(fullGroupChat);
  } catch (error) {
    res.status(400);
    throw new Error(error.message);
  }
});
```

### Description:
- **Function Purpose**: Creates a new group chat.
- **Parameters**:
  - `users`: An array of user IDs to be added to the group chat.
  - `name`: The name of the group chat.
- **Logic**:
  - Validates the presence of users and name.
  - Ensures that at least two users are provided for group creation.
  - Creates the group chat and returns the full group chat data.

## 5. Rename Group

```javascript
const renameGroup = asyncHandler(async (req, res) => {
  const { chatId, chatName } = req.body;

  const updatedChat = await Chat.findByIdAndUpdate(
    chatId,
    {
      chatName: chatName,
    },
    {
      new: true,
    }
  )
    .populate("users", "-password")
    .populate("groupAdmin", "-password");

  if (!updatedChat) {
    res.status(404);
    throw new Error("Chat Not Found");
  } else {
    res.json(updatedChat);
  }
});
```

### Description:
- **Function Purpose**: Renames an existing group chat.
- **Parameters**:
  - `chatId`: The ID of the chat to be renamed.
  - `chatName`: The new name for the chat.
- **Logic**:
  - Updates the chat name and returns the updated chat data.

## 6. Remove User from Group

```javascript
const removeFromGroup = asyncHandler(async (req, res) => {
  const { chatId, userId } = req.body;

  const removed = await Chat.findByIdAndUpdate(
    chatId,
    {
      $pull: { users: userId },
    },
    {
      new: true,
    }
  )
    .populate("users", "-password")
    .populate("groupAdmin", "-password");

  if (!removed) {
    res.status(404);
    throw new Error("Chat Not Found");
  } else {
    res.json(removed);
  }
});
```

### Description:
- **Function Purpose**: Removes a user from a group chat.
- **Parameters**:
  - `chatId`: The ID of the chat from which to remove the user.
  - `userId`: The ID of the user to be removed.
- **Logic**:
  - Updates the chat to remove the specified user and returns the updated chat data.

## 7. Add User to Group

```javascript
const addToGroup = asyncHandler(async (req, res) => {
  const { chatId, userId } = req.body;

  const added = await Chat.findByIdAndUpdate(
    chatId,
    {
      $push: { users: userId },
    },
    {
      new: true,
    }
  )
    .populate("users", "-password")
    .populate("groupAdmin", "-password");

  if (!added) {
    res.status(404);
    throw new Error("Chat Not Found");
  } else {
    res.json(added);
  }
});
```

### Description:
- **Function Purpose**: Adds a user to a group chat.
- **Parameters**:
  - `chatId`: The ID of the chat to which the user will be added.
  - `userId`: The ID of the user to be added.
- **Logic**:
  - Updates the chat to include the specified user and returns the updated chat data.

## 8. Module Export

```javascript
module.exports = {
  accessChat,
  fetchChats,
  createGroupChat,
  renameGroup,
  addToGroup,
  removeFromGroup,
};
```

### Description:
- **Export**: Exports all the defined controller functions for use in the routes, allowing for organized and modular handling of chat-related operations.
