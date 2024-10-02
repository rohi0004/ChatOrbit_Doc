
# User Controller Documentation

This file defines the controller functions for managing user operations in the application. It includes functions for fetching users, registering new users, and authenticating users, handling user interactions within the system.

## 1. Imports

```javascript
const asyncHandler = require("express-async-handler");
const User = require("../models/userModel");
const generateToken = require("../config/generateToken");
```

### Description:
- **asyncHandler**: Middleware to handle asynchronous route handlers and simplify error handling in Express.
- **User**: The user model used to interact with user data in the MongoDB database.
- **generateToken**: Function to generate a JWT for user authentication.

## 2. Get or Search All Users

```javascript
const allUsers = asyncHandler(async (req, res) => {
  const keyword = req.query.search
    ? {
        $or: [
          { name: { $regex: req.query.search, $options: "i" } },
          { email: { $regex: req.query.search, $options: "i" } },
        ],
      }
    : {};

  const users = await User.find(keyword).find({ _id: { $ne: req.user._id } });
  res.send(users);
});
```

### Description:
- **Function Purpose**: Retrieves all users or searches for users based on a query.
- **Parameters**:
  - `search`: A query parameter to filter users by name or email.
- **Logic**:
  - Constructs a search keyword using regular expressions to match user names or emails.
  - Excludes the current user from the results.
  - Returns the list of matching users as a JSON response.

## 3. Register New User

```javascript
const registerUser = asyncHandler(async (req, res) => {
  const { name, email, password, pic } = req.body;

  if (!name || !email || !password) {
    res.status(400);
    throw new Error("Please Enter all the Fields");
  }

  const userExists = await User.findOne({ email });

  if (userExists) {
    res.status(400);
    throw new Error("User already exists");
  }

  const user = await User.create({
    name,
    email,
    password,
    pic,
  });

  if (user) {
    res.status(201).json({
      _id: user._id,
      name: user.name,
      email: user.email,
      isAdmin: user.isAdmin,
      pic: user.pic,
      token: generateToken(user._id),
    });
  } else {
    res.status(400);
    throw new Error("User not found");
  }
});
```

### Description:
- **Function Purpose**: Registers a new user in the system.
- **Parameters**:
  - `name`: The name of the user.
  - `email`: The user's email address.
  - `password`: The user's password.
  - `pic`: (optional) The user's profile picture URL.
- **Logic**:
  - Validates the presence of required fields.
  - Checks if the user already exists based on the email.
  - Creates a new user and generates a JWT for authentication upon successful registration.
  - Returns the newly created user data and token as a JSON response.

## 4. Authenticate User

```javascript
const authUser = asyncHandler(async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });

  if (user && (await user.matchPassword(password))) {
    res.json({
      _id: user._id,
      name: user.name,
      email: user.email,
      isAdmin: user.isAdmin,
      pic: user.pic,
      token: generateToken(user._id),
    });
  } else {
    res.status(401);
    throw new Error("Invalid Email or Password");
  }
});
```

### Description:
- **Function Purpose**: Authenticates a user and logs them in.
- **Parameters**:
  - `email`: The user's email address.
  - `password`: The user's password.
- **Logic**:
  - Searches for the user by email.
  - Validates the password using the `matchPassword` method.
  - Returns user data and a token upon successful authentication; otherwise, it responds with an error.

## 5. Module Export

```javascript
module.exports = { allUsers, registerUser, authUser };
```

### Description:
- **Export**: Exports the `allUsers`, `registerUser`, and `authUser` functions for use in the routes, allowing for organized and modular handling of user-related operations.
