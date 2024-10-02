
# Auth Middleware Documentation

This file defines the authentication middleware used to protect routes in the application. It verifies the user's JSON Web Token (JWT) and attaches the user information to the request object for further processing.

## 1. Imports

```javascript
const jwt = require("jsonwebtoken");
const User = require("../models/userModel.js");
const asyncHandler = require("express-async-handler");
```
### Description:
- **jsonwebtoken**: Library for working with JSON Web Tokens (JWT), providing methods to sign, verify, and decode tokens.
- **User**: The user model used to interact with the user data in the MongoDB database.
- **express-async-handler**: Middleware to handle asynchronous route handlers, simplifying error handling in Express.

## 2. Protect Middleware Definition

```javascript
const protect = asyncHandler(async (req, res, next) => {
  let token;

  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith("Bearer")
  ) {
    try {
      token = req.headers.authorization.split(" ")[1];

      //decodes token id
      const decoded = jwt.verify(token, process.env.JWT_SECRET);

      req.user = await User.findById(decoded.id).select("-password");

      next();
    } catch (error) {
      res.status(401);
      throw new Error("Not authorized, token failed");
    }
  }

  if (!token) {
    res.status(401);
    throw new Error("Not authorized, no token");
  }
});
```

### Description:
- **Middleware Logic**:
  - **Token Extraction**: Checks if the `Authorization` header is present and starts with "Bearer". If so, it extracts the token from the header.
  - **Token Verification**: Uses `jwt.verify` to decode the token and retrieve the user ID (`decoded.id`). It verifies the token against the secret stored in `process.env.JWT_SECRET`.
  - **User Retrieval**: Finds the user in the database using the decoded ID and excludes the password from the result.
  - **Next Middleware**: Calls `next()` to proceed to the next middleware or route handler if everything is valid.
  - **Error Handling**: If an error occurs during token verification or if no token is provided, it responds with a `401 Unauthorized` status and throws an error with a relevant message.

## 3. Module Export

```javascript
module.exports = { protect };
```
### Description:
- **Export**: Exports the `protect` middleware for use in other parts of the application, allowing routes to be secured by requiring user authentication.
