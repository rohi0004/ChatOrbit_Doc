
# User Routes Documentation

This file defines the routing for user-related operations using Express.js. It utilizes middleware for authentication and controller functions for handling the business logic related to user registration, authentication, and fetching user data.

## 1. Imports

```javascript
const express = require("express");
const {
  registerUser,
  authUser,
  allUsers,
} = require("../controllers/userControllers");
const { protect } = require("../middleware/authMiddleware");
```
### Description:
- **express**: Framework for building the server.
- **User Controller Functions**:
  - `registerUser`: Function to register a new user.
  - `authUser`: Function to authenticate an existing user.
  - `allUsers`: Function to retrieve all registered users.
- **Middleware**:
  - `protect`: Middleware that protects routes by ensuring the user is authenticated.

## 2. Router Initialization

```javascript
const router = express.Router();
```
### Description:
- **Router Instance**: Initializes an Express router instance to define user-related routes.

## 3. Route Definitions

### 3.1 Fetch All Users

```javascript
router.route("/").get(protect, allUsers);
```
- **GET /api/user/**: 
  - **Purpose**: Retrieve all registered users.
  - **Middleware**: Requires authentication via the `protect` middleware.

### 3.2 Register User

```javascript
router.route("/").post(registerUser);
```
- **POST /api/user/**: 
  - **Purpose**: Register a new user.
  - **Middleware**: No authentication required for registration.

### 3.3 User Login

```javascript
router.post("/login", authUser);
```
- **POST /api/user/login**: 
  - **Purpose**: Authenticate a user and log them in.
  - **Middleware**: No authentication required for login.

## 4. Module Export

```javascript
module.exports = router;
```
### Description:
- **Export**: Exports the router instance for use in other parts of the application, allowing the defined routes to be accessible.
