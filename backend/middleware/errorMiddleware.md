
# Error Handling Middleware Documentation

This file defines middleware for handling errors in the application. It provides mechanisms for dealing with 404 errors and general error responses, ensuring that errors are captured and returned in a consistent format.

## 1. Not Found Middleware

```javascript
const notFound = (req, res, next) => {
  const error = new Error(`Not Found - ${req.originalUrl}`);
  res.status(404);
  next(error);
};
```
### Description:
- **Middleware Logic**:
  - **Error Creation**: Creates a new `Error` object with a message indicating that the requested URL was not found.
  - **Set Status**: Sets the HTTP status code to `404` (Not Found).
  - **Next Middleware**: Calls `next(error)` to pass the error to the next middleware, which will handle the error response.

## 2. Error Handler Middleware

```javascript
const errorHandler = (err, req, res, next) => {
  const statusCode = res.statusCode === 200 ? 500 : res.statusCode;
  res.status(statusCode);
  res.json({
    message: err.message,
    stack: process.env.NODE_ENV === "production" ? null : err.stack,
  });
};
```

### Description:
- **Middleware Logic**:
  - **Status Code Determination**: Checks if the current status code is `200` (OK). If so, it sets the status code to `500` (Internal Server Error); otherwise, it uses the current status code.
  - **Set Status**: Sets the response status using the determined status code.
  - **JSON Response**: Sends a JSON response containing:
    - `message`: The error message from the error object.
    - `stack`: The error stack trace, which is omitted in production environments (when `NODE_ENV` is set to "production").

## 3. Module Export

```javascript
module.exports = { notFound, errorHandler };
```
### Description:
- **Export**: Exports both the `notFound` and `errorHandler` middleware functions for use in other parts of the application, allowing for centralized error handling.
