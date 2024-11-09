# MERN-BACKEND-FOLDER-STRUCTURE

backend/
├── config/
│   ├── db.js
│   └── keys.js
│
├── controllers/
│   ├── userController.js
│   ├── postController.js
│   └── authController.js
│
├── middleware/
│   ├── authMiddleware.js
│   └── errorMiddleware.js
│
├── models/
│   ├── User.js
│   ├── Post.js
│   └── Comment.js
│
├── routes/
│   ├── userRoutes.js
│   ├── postRoutes.js
│   └── authRoutes.js
│
├── utils/
│   ├── sendEmail.js
│   └── logger.js
│
├── .env
├── server.js
└── package.json



---
---
---
# Basic Code Snippets Here Add Before Starting Project


## Example Code Snippets
### Add this code in db.file ==> config or connection folder name 
##### this code for every project neended.

### 1. db.js

```javascript
const mongoose = require('mongoose');
const dotenv = require('dotenv');

dotenv.config();

const connectDB = async () => {
    try {
        await mongoose.connect(process.env.MONGO_URL, {
            useNewUrlParser: true,
            useUnifiedTopology: true,
        });
        console.log('MongoDB connected');
    } catch (error) {
        console.error('MongoDB connection failed:', error.message);
        process.exit(1);
    }
};

module.exports = connectDB;
```


---
---

# MULTER ki code snippet
## add this in middleware folder inside this multer.middleware.js file name 

```javascript
const multer = require('multer');
const path = require('path');

// Define storage for uploaded files
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/'); // Change this to your desired upload path
        // If you want to change the upload directory, modify this line.
    },
    filename: (req, file, cb) => {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
        cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
        // If you want a different naming convention for uploaded files, modify this line.
    },
});

// Define file filter to allow specific file types
const fileFilter = (req, file, cb) => {
    const allowedTypes = /jpeg|jpg|png|gif/; // Allowed file types
    // If you want to allow additional file types, modify this regular expression.
    const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());
    const mimetype = allowedTypes.test(file.mimetype);

    if (extname && mimetype) {
        return cb(null, true);
    } else {
        cb(new Error('Only .png, .jpg, .jpeg, and .gif formats allowed!'), false);
        // You can customize this error message if needed.
    }
};

// Set up multer with the defined storage and file filter
const upload = multer({
    storage: storage,
    limits: {
        fileSize: 2 * 1024 * 1024, // Limit file size to 2 MB
        // If you want to change the file size limit, modify this line.
    },
    fileFilter: fileFilter,
});

// Export the multer upload middleware
module.exports = upload;
```


## kaise use karege iss multer.middleware.js file 
#### exports ki nam ko ham (req,**`middleware(yehi use karege)**`,res)



```javascript
const express = require('express');
const upload = require('../middleware/multer.middleware'); // Adjust the path as necessary
const router = express.Router();

router.post('/upload', upload.single('image'), (req, res) => {
    // 'image' is the name of the file input field in the form
    // If you want to handle multiple files, consider using upload.array('images', maxCount) instead.

    if (!req.file) {
        return res.status(400).send('No file uploaded.');
        // You can customize this error message if needed.
    }
    
    res.status(200).json({
        message: 'File uploaded successfully!',
        file: req.file,
        // If you want to return additional data, modify this response.
    });
});

module.exports = router;
```


```javascript
// For multiple files
app.post('/upload', upload.array('files', 10), (req, res) => {
    res.json({ message: 'Files uploaded successfully!', files: req.files });
});

// For multiple files of the same field name
router.post('/upload', upload.array('images', 5), (req, res) => {
    // Handle multiple files here
});

// Or for different field names
router.post('/upload', upload.fields([{ name: 'image1' }, { name: 'image2' }]), (req, res) => {
    // Handle files here
});


```
---
---
---
# ApiResponse.js ka kam 


```javascript
// utility/ApiResponse.js

class ApiResponse {
    static success(res, data = null, message = 'Request was successful', statusCode = 200) {
        return res.status(statusCode).json({
            success: true,
            message,
            data,
        });
    }

    static error(res, message = 'An error occurred', statusCode = 500, errors = null) {
        return res.status(statusCode).json({
            success: false,
            message,
            errors,
        });
    }

    static notFound(res, message = 'Resource not found') {
        return this.error(res, message, 404);
    }

    static validationError(res, errors) {
        return this.error(res, 'Validation error', 400, errors);
    }

    static unauthorized(res, message = 'Unauthorized access') {
        return this.error(res, message, 401);
    }

    static forbidden(res, message = 'Access forbidden') {
        return this.error(res, message, 403);
    }
}

module.exports = ApiResponse;


```

# Example
## how to use 
### bas import kar lena jaha jarurat hai jis file me kam ho 




```javascript
// routes/exampleRoute.js
const express = require('express');
const ApiResponse = require('../utility/ApiResponse'); // Importing ApiResponse utility
const router = express.Router();

// Example route
router.get('/example', (req, res) => {
    try {
        const data = { id: 1, name: 'Example Item' };
        return ApiResponse.success(res, data, 'Item retrieved successfully');
    } catch (error) {
        return ApiResponse.error(res, 'Failed to retrieve item', 500, error.message);
    }
});

module.exports = router;

```


---
---
---

# ApiError.js ki kam

## Step 1: Create ApiError.js

```javascript
// utility/ApiError.js

class ApiError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
        this.isOperational = true; // Indicates if the error is operational (i.e., expected)
        Error.captureStackTrace(this, this.constructor); // Capture stack trace
    }
}

module.exports = ApiError;

```

## Step 2: Use ApiError.js in Route Files




```javascript

// routes/exampleRoute.js
const express = require('express');
const ApiResponse = require('../utility/ApiResponse'); // Import ApiResponse utility
const ApiError = require('../utility/ApiError'); // Import ApiError utility
const router = express.Router();

// Example route
router.get('/example', (req, res, next) => {
    try {
        const data = { id: 1, name: 'Example Item' };
        return ApiResponse.success(res, data, 'Item retrieved successfully');
    } catch (error) {
        // Create an instance of ApiError and pass it to the next middleware
        return next(new ApiError('Failed to retrieve item', 500));
    }
});

// Example route with an operational error
router.get('/not-found', (req, res, next) => {
    // Simulate a not found error
    return next(new ApiError('Resource not found', 404));
});

module.exports = router;

```

# Step 3: Handle Errors in server.js


```javascript
// server.js
const express = require('express');
const exampleRoute = require('./routes/exampleRoute'); // Importing the example route
const ApiError = require('./utility/ApiError'); // Importing ApiError utility

const app = express();
const port = 3000;

// Middleware
app.use(express.json()); // Middleware to parse JSON request bodies

// Routes
app.use('/', exampleRoute); // Use the example route at the root path

// Error handling middleware
app.use((err, req, res, next) => {
    if (err instanceof ApiError) {
        return res.status(err.statusCode).json({
            success: false,
            message: err.message,
        });
    }
    
    // Handle unexpected errors
    return res.status(500).json({
        success: false,
        message: 'An unexpected error occurred',
    });
});

// Start the server
app.listen(port, () => {
    console.log(`Server is running on http://localhost:${port}`);
});


```
---
---
---
---

### Summary of Implementation
ApiError.js: This utility class allows you to create structured error objects with a message and a status code.

Usage in Route Files: In your route files, you can create an instance of ApiError and pass it to the next middleware to handle it.

Error Handling Middleware: In your main server file, you set up an error-handling middleware that checks if the error is an instance of ApiError. If it is, it sends a structured response with the appropriate status code and message.




# diff b/w

# Differences Between `ApiResponse.js` and `ApiError.js`

The `ApiResponse.js` and `ApiError.js` utilities serve different purposes in a Node.js application, specifically in the context of handling API responses and errors. Here’s a breakdown of their differences:

## 1. Purpose

- **`ApiResponse.js`**:
  - **Functionality**: This utility is designed to standardize successful API responses. It provides a consistent structure for sending responses back to the client, regardless of the endpoint or the data being returned.
  - **Use Case**: It is used when the API request is successful, and you want to send data back to the client along with a success message.

- **`ApiError.js`**:
  - **Functionality**: This utility is designed to create structured error objects. It extends the built-in `Error` class to include additional properties like `statusCode` and `isOperational`, which help in identifying and handling errors more effectively.
  - **Use Case**: It is used to throw errors in a structured way when something goes wrong during the processing of a request.

## 2. Structure

- **`ApiResponse.js`**:
  - **Methods**: It typically contains static methods for sending success responses, such as:
    - `success(res, data, message, statusCode)`: Sends a successful response with data.
    - `error(res, message, statusCode, errors)`: Sends an error response in a standardized format.
  - **Response Format**: The response typically includes:
    - `success`: A boolean indicating the success of the request.
    - `message`: A message describing the outcome.
    - `data`: The actual data returned (if any).

- **`ApiError.js`**:
  - **Class Structure**: It is a class that extends the built-in `Error` class, adding properties like `statusCode` and `isOperational`.
  - **Error Format**: When an error is thrown, it can include:
    - `message`: A description of the error.
    - `statusCode`: An HTTP status code indicating the type of error (e.g., 404 for Not Found, 500 for Internal Server Error).
    - `isOperational`: A boolean indicating whether the error is operational (expected) or programming (unexpected).

## 3. Usage in Application

- **Using `ApiResponse.js`**:
  - You call the methods to send responses in your route handlers when the operation is successful. For example:
    ```javascript
    return ApiResponse.success(res, data, 'Item retrieved successfully');
    ```

- **Using `ApiError.js`**:
  - You create an instance of `ApiError` and pass it to the next middleware when an error occurs. For example:
    ```javascript
    return next(new ApiError('Failed to retrieve item', 500));
    ```

## 4. Error Handling

- **Error Handling with `ApiResponse.js`**:
  - It does not handle errors directly; it focuses on successful responses.

- **Error Handling with `ApiError.js`**:
  - It is specifically designed for error handling. You can use it in conjunction with an error-handling middleware to send structured error responses to the client. For example:
    ```javascript
    app.use((err, req, res, next) => {
        if (err instanceof ApiError) {
            return res.status(err.statusCode).json({
                success: false,
                message: err.message,
            });
        }
        // Handle unexpected errors
    });
    ```

## Summary

In summary, `ApiResponse.js` is used for crafting successful API responses, while `ApiError.js` is used for creating structured error objects that can be thrown and caught in your application. Together, they provide a robust framework for handling both successful responses and errors in a consistent manner, improving the maintainability and readability of your API code.


---
---
---




























# Summary of Changeable Points for Multer Middleware

This document outlines the customizable points in the `multer.middleware.js` file for handling file uploads in an Express.js application.

## 1. Upload Directory
- **Location**: In the `destination` callback function.
- **Change**: Modify the path in `cb(null, 'uploads/');` to your desired directory.

## 2. File Naming Convention
- **Location**: In the `filename` function.
- **Change**: Modify the filename generation logic to implement a different naming scheme.

## 3. Allowed File Types
- **Location**: In the `fileFilter` function.
- **Change**: Update the `allowedTypes` regex to include more file types if needed (e.g., `/jpeg|jpg|png|gif|pdf|docx/`).

## 4. File Size Limit
- **Location**: In the `limits` object.
- **Change**: Adjust the `fileSize` limit as needed (e.g., `limits: { fileSize: 5 * 1024 * 1024 }` for 5 MB).

## 5. Multiple File Uploads
- **Location**: In the route handler.
- **Change**: If you want to handle multiple files, change `upload.single('image')` to:
  - `upload.array('images', maxCount)` for multiple files of the same field name.
  - `upload.fields([{ name: 'image1' }, { name: 'image2' }])` for different field names.

## 6. Error Messages
- **Location**: In the `fileFilter` function and route handler.
- **Change**: Customize the error messages returned for unsupported file types or upload errors.

---

By modifying these points, you can tailor the Multer middleware to meet the specific needs of your project.

---
---












































## Folder Descriptions

1. **`config/`**: 
   - **`db.js`**: Contains the database connection logic using Mongoose.
   - **`keys.js`**: Contains configurations for different environments (development, production), such as API keys and secrets.

2. **`controllers/`**: 
   - Contains the logic for handling requests and responses. Each file usually corresponds to a specific resource.
   - **`userController.js`**: Handles user-related operations (registration, login, etc.).
   - **`postController.js`**: Handles operations related to posts (creating, fetching, updating, deleting posts).
   - **`authController.js`**: Handles authentication logic (e.g., JWT token generation).

3. **`middleware/`**: 
   - Contains middleware functions that process requests before they reach the controller.
   - **`authMiddleware.js`**: Middleware for checking if a user is authenticated.
   - **`errorMiddleware.js`**: Middleware for handling errors in a centralized manner.

4. **`models/`**: 
   - Defines the data schema using Mongoose for MongoDB.
   - **`User .js`**: Defines the user schema.
   - **`Post.js`**: Defines the post schema.
   - **`Comment.js`**: Defines the comment schema (if applicable).

5. **`routes/`**: 
   - Defines the API endpoints and maps them to the appropriate controller functions.
   - **`userRoutes.js`**: Defines routes related to user operations.
   - **`postRoutes.js`**: Defines routes related to post operations.
   - **`authRoutes.js`**: Defines routes for authentication (login, logout).

6. **`utils/`**: 
   - Contains utility functions that can be reused across the application.
   - **`sendEmail.js`**: Functionality for sending emails (e.g., for password resets).
   - **`logger.js`**: Logging utility for logging application events.

7. **`.env`**: 
   - Environment variables for sensitive information like database URLs, API keys, and JWT secrets.

8. **`server.js`**: 
   - The entry point for the backend application where the server is set up and started. This file typically contains the Express app setup, middleware usage, and route definitions.

9. **`package.json`**: 
   - Contains the project metadata and dependencies for the backend application.



