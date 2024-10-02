
# Token Generation Documentation

This file defines the function for generating JSON Web Tokens (JWT) for user authentication. It uses the `jsonwebtoken` library to sign tokens with user information and expiration settings.

## 1. Imports

```javascript
const jwt = require("jsonwebtoken");
```

### Description:
- **jsonwebtoken**: Library for working with JSON Web Tokens (JWT), providing methods to sign, verify, and decode tokens.

## 2. Generate Token Function

```javascript
const generateToken = (id) => {
  return jwt.sign({ id }, process.env.JWT_SECRET, {
    expiresIn: "30d",
  });
};
```

### Description:
- **Function Purpose**: Generates a JWT for a user based on their unique identifier (ID).
- **Parameters**:
  - `id`: The ID of the user for whom the token is being generated.
- **Logic**:
  - Uses `jwt.sign` to create a new token that includes the user ID.
  - Signs the token using a secret stored in the environment variable `JWT_SECRET`.
  - Sets the token expiration to 30 days.

## 3. Module Export

```javascript
module.exports = generateToken;
```

### Description:
- **Export**: Exports the `generateToken` function for use in other parts of the application, allowing for the generation of JWTs for user authentication during login or registration.
