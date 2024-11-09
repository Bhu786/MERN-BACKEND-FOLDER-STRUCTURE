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



