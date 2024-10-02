
# Database Connection Documentation

This file defines the function for establishing a connection to the MongoDB database using Mongoose. It includes error handling for connection issues and logs the connection status.

## 1. Imports

```javascript
const mongoose = require("mongoose");
const colors = require("colors");
```

### Description:
- **mongoose**: Library for MongoDB object modeling, providing a schema-based solution for modeling application data.
- **colors**: Library for coloring console output, enhancing readability of logs.

## 2. Connect Database Function

```javascript
const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });

    console.log(`MongoDB Connected: ${conn.connection.host}`.cyan.underline);
  } catch (error) {
    console.error(`Error: ${error.message}`.red.bold);
    process.exit(1); // Exit with a non-zero status code to indicate an error
  }
};
```

### Description:
- **Function Purpose**: Establishes a connection to the MongoDB database using the connection string provided in the environment variable `MONGO_URI`.
- **Logic**:
  - Attempts to connect to the MongoDB database using Mongoose with the specified options:
    - `useNewUrlParser`: Enables the new URL string parser.
    - `useUnifiedTopology`: Uses the new topology engine.
  - Upon successful connection, logs the host of the connected database in cyan color.
  - If an error occurs during the connection, logs the error message in red and exits the process with a non-zero status code.

## 3. Module Export

```javascript
module.exports = connectDB;
```

### Description:
- **Export**: Exports the `connectDB` function for use in other parts of the application, enabling the application to connect to the MongoDB database when needed.
