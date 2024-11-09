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



