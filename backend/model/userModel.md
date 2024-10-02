
# User Model Documentation

This file defines the Mongoose schema for the user model, representing user data in the MongoDB database. The schema outlines the structure and types of data associated with a user, including authentication details and user roles.

## 1. Imports

```javascript
const mongoose = require("mongoose");
const bcrypt = require("bcryptjs");
```
### Description:
- **mongoose**: Library for MongoDB object modeling, providing a schema-based solution for modeling application data.
- **bcrypt**: Library for hashing passwords, providing secure password storage.

## 2. User Schema Definition

```javascript
const userSchema = mongoose.Schema(
  {
    name: { type: "String", required: true },
    email: { type: "String", unique: true, required: true },
    password: { type: "String", required: true },
    pic: {
      type: "String",
      required: true,
      default:
        "https://icon-library.com/images/anonymous-avatar-icon/anonymous-avatar-icon-25.jpg",
    },
    isAdmin: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
  { timestamps: true }
);
```
### Description:
- **Schema Fields**:
  - `name`: (String) The name of the user, required for all users.
  - `email`: (String) The user's email address, must be unique and required.
  - `password`: (String) The user's password, required for authentication.
  - `pic`: (String) The user's profile picture URL, with a default value pointing to an anonymous avatar.
  - `isAdmin`: (Boolean) Indicates if the user has admin privileges; defaults to `false`.
- **Timestamps**: Automatically adds `createdAt` and `updatedAt` fields to the schema.

## 3. Password Matching Method

```javascript
userSchema.methods.matchPassword = async function (enteredPassword) {
  return await bcrypt.compare(enteredPassword, this.password);
};
```
### Description:
- **matchPassword**: Instance method for comparing a given password with the stored hashed password using bcrypt.

## 4. Password Hashing Middleware

```javascript
userSchema.pre("save", async function (next) {
  if (!this.isModified) {
    next();
  }

  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
});
```
### Description:
- **Pre-save Middleware**: Hashes the user's password before saving the user document to the database. It checks if the password is modified and then generates a salt to hash the password using bcrypt.

## 5. User Model Creation

```javascript
const User = mongoose.model("User", userSchema);
```
### Description:
- **Model**: Creates a Mongoose model named `User` based on the defined schema, allowing for interaction with the `users` collection in the MongoDB database.

## 6. Module Export

```javascript
module.exports = User;
```
### Description:
- **Export**: Exports the `User` model for use in other parts of the application, enabling the ability to create, read, update, and delete user data.
