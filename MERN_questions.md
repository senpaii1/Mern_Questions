# Mern_Questions

## 1. Write a basic Node.js server that listens on port 3000 and returns a "Hello, World!" message when the root URL is accessed.

Here's a quick and dirty Node.js server that listens on port 3000 and spits out "Hello, World!" when you hit the root URL:

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello, World!");
  } else {
    res.writeHead(404, { "Content-Type": "text/plain" });
    res.end("404 Not Found");
  }
});

server.listen(3000, () => {
  console.log("Server running on http://localhost:3000/");
});
```

## 2. Create a simple REST API using Express.js with a single route `/users` that returns a JSON list of users.

Here’s how you can create a simple REST API using Express.js with a single route `/users` that returns a list of users in JSON format:

```javascript
const express = require("express");
const app = express();

// Sample list of users
const users = [
  { id: 1, name: "John Doe" },
  { id: 2, name: "Jane Smith" },
  { id: 3, name: "Alice Johnson" },
];

// Define /users route
app.get("/users", (req, res) => {
  res.json(users);
});

// Start server
const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## 3 . Build a basic React component that fetches the list of users from the /users API route (from question 2) and displays them in a table.

```javascript
import React, { useEffect, useState } from "react";
import axios from "axios";

const UsersTable = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // Fetch users from the /users route
    const fetchUsers = async () => {
      try {
        const response = await axios.get("/users");
        setUsers(response.data);
      } catch (error) {
        console.error("Error fetching users:", error);
      }
    };

    fetchUsers();
  }, []);

  return (
    <div>
      <h2>User List</h2>
      {users.length > 0 ? (
        <table border="1">
          <thead>
            <tr>
              <th>ID</th>
              <th>Name</th>
            </tr>
          </thead>
          <tbody>
            {users.map((user) => (
              <tr key={user.id}>
                <td>{user.id}</td>
                <td>{user.name}</td>
              </tr>
            ))}
          </tbody>
        </table>
      ) : (
        <p>No users found.</p>
      )}
    </div>
  );
};

export default UsersTable;
```

## 4. Create a MongoDB schema for storing user data (name, email, age), and write a script to insert a new user into the collection.

```javascript
// Import Mongoose
const mongoose = require("mongoose");

// Connect to MongoDB
mongoose
  .connect("mongodb://localhost:27017/mydatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("MongoDB connected"))
  .catch((err) => console.log(err));

// Define the User schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number, required: true },
});

// Create the User model
const User = mongoose.model("User", userSchema);

// Script to insert a new user
const createUser = async () => {
  const newUser = new User({
    name: "Alice Johnson",
    email: "alice@example.com",
    age: 28,
  });

  try {
    const savedUser = await newUser.save();
    console.log("User created:", savedUser);
  } catch (err) {
    console.error("Error creating user:", err);
  }
};

// Call the function to insert the user
createUser();
```

## 5 : Create an Express.js route to fetch a user by their email from the MongoDB database.

```javascript
// Import Mongoose and the User model
const express = require("express");
const mongoose = require("mongoose");
const app = express();

// Connect to MongoDB (ensure you're already connected as shown in previous examples)
mongoose
  .connect("mongodb://localhost:27017/mydatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("MongoDB connected"))
  .catch((err) => console.log(err));

// User Schema and Model (as previously defined)
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number, required: true },
});

const User = mongoose.model("User", userSchema);

// Route to fetch a user by their email
app.get("/users/:email", async (req, res) => {
  const userEmail = req.params.email;

  try {
    const user = await User.findOne({ email: userEmail });
    if (user) {
      res.json(user);
    } else {
      res.status(404).json({ message: "User not found" });
    }
  } catch (err) {
    res.status(500).json({ message: "Error fetching user", error: err });
  }
});

// Start server
const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## 6: Build a form component in React that allows users to submit their name, email, and age. On submission, send the data to the backend API and update the state to display the new user.

```javascript
import React, { useState } from "react";
import axios from "axios";

const UserForm = () => {
  const [formData, setFormData] = useState({ name: "", email: "", age: "" });
  const [users, setUsers] = useState([]);

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();

    try {
      const response = await axios.post("/users", formData); // Send data to the backend
      setUsers([...users, response.data]); // Update state to display the new user
      setFormData({ name: "", email: "", age: "" }); // Reset the form
    } catch (error) {
      console.error("Error submitting user:", error);
    }
  };

  return (
    <div>
      <h2>Submit User Information</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label>Name:</label>
          <input
            type="text"
            name="name"
            value={formData.name}
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <label>Email:</label>
          <input
            type="email"
            name="email"
            value={formData.email}
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <label>Age:</label>
          <input
            type="number"
            name="age"
            value={formData.age}
            onChange={handleChange}
            required
          />
        </div>
        <button type="submit">Submit</button>
      </form>

      <h3>User List</h3>
      {users.length > 0 ? (
        <ul>
          {users.map((user, index) => (
            <li key={index}>
              {user.name} ({user.email}, {user.age} years old)
            </li>
          ))}
        </ul>
      ) : (
        <p>No users yet.</p>
      )}
    </div>
  );
};

export default UserForm;
```

## 7: React Routing: Set up React Router in an application to navigate between a Home page and a Users page

```javascript

// App.js
import React from 'react';
import { BrowserRouter as Router, Route, Switch, Link } from 'react-router-dom';
import Home from './Home';
import Users from './Users';

const App = () => {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/users">Users</Link>
            </li>
          </ul>
        </nav>

        <Switch>
          <Route exact path="/" component={Home} />
          <Route path="/users" component={Users} />
        </Switch>
      </div>
    </Router>
  );
};

export default App;

// Home.js
import React from 'react';

const Home = () => {
  return (
    <div>
      <h2>Home Page</h2>
      <p>Welcome to the home page!</p>
    </div>
  );
};

export default Home;

// Users.js
import React from 'react';

const Users = () => {
  return (
    <div>
      <h2>Users Page</h2>
      <p>Here is the list of users.</p>
    </div>
  );
};

export default Users;
```

## 8: Design and implement a REST API in Express.js for a simple blog application with routes for creating, reading, updating, and deleting blog posts.

```javascript
const express = require("express");
const mongoose = require("mongoose");
const app = express();

app.use(express.json()); // Middleware to parse JSON requests

// Connect to MongoDB
mongoose
  .connect("mongodb://localhost:27017/blogdb", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("MongoDB connected"))
  .catch((err) => console.log(err));

// Define the BlogPost schema
const blogPostSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: { type: String, required: true },
  author: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
});

// Create the BlogPost model
const BlogPost = mongoose.model("BlogPost", blogPostSchema);

// Create a new blog post (POST)
app.post("/posts", async (req, res) => {
  const { title, content, author } = req.body;

  try {
    const newPost = new BlogPost({ title, content, author });
    const savedPost = await newPost.save();
    res.status(201).json(savedPost);
  } catch (err) {
    res.status(500).json({ error: "Error creating post", message: err });
  }
});

// Get all blog posts (GET)
app.get("/posts", async (req, res) => {
  try {
    const posts = await BlogPost.find();
    res.json(posts);
  } catch (err) {
    res.status(500).json({ error: "Error fetching posts", message: err });
  }
});

// Get a single blog post by ID (GET)
app.get("/posts/:id", async (req, res) => {
  try {
    const post = await BlogPost.findById(req.params.id);
    if (post) {
      res.json(post);
    } else {
      res.status(404).json({ message: "Post not found" });
    }
  } catch (err) {
    res.status(500).json({ error: "Error fetching post", message: err });
  }
});

// Update a blog post by ID (PUT)
app.put("/posts/:id", async (req, res) => {
  const { title, content, author } = req.body;

  try {
    const updatedPost = await BlogPost.findByIdAndUpdate(
      req.params.id,
      { title, content, author },
      { new: true }
    );
    if (updatedPost) {
      res.json(updatedPost);
    } else {
      res.status(404).json({ message: "Post not found" });
    }
  } catch (err) {
    res.status(500).json({ error: "Error updating post", message: err });
  }
});

// Delete a blog post by ID (DELETE)
app.delete("/posts/:id", async (req, res) => {
  try {
    const deletedPost = await BlogPost.findByIdAndDelete(req.params.id);
    if (deletedPost) {
      res.json({ message: "Post deleted" });
    } else {
      res.status(404).json({ message: "Post not found" });
    }
  } catch (err) {
    res.status(500).json({ error: "Error deleting post", message: err });
  }
});

// Start the server
const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## 9. Basic Dockerfile

```DockerFile

Node.js Express Dockerfile
Docker
# Use an official Node.js runtime as a parent image
FROM node:14

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the package.json file
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the application code
COPY . .

# Expose the port
EXPOSE 3000

# Run the command to start the development server
CMD ["npm", "start"]
Explanation:
We use the official Node.js 14 image as the base.
We set the working directory to /usr/src/app.
We copy package.json and install dependencies.
We copy the application code.
We expose port 3000.
We set the default command to npm start.
```

## 10. Docker Compose

```DockerFile

Multi-Container Application with Node.js and MongoDB
YAML
version: '3'

services:
  node-app:
    build: .
    ports:
      - '3000:3000'
    depends_on:
      - mongo
    environment:
      - DATABASE_URL=mongodb://mongo:27017/

  mongo:
    image: mongo:latest
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
Explanation:
We define two services: node-app and mongo.
node-app is built from the current directory.
We map port 3000 from the container to the host.
node-app depends on mongo.
We set the DATABASE_URL environment variable.
We use the official MongoDB image.
We persist data using a named volume.
```

## 11. Docker Networking

```DockerFile

Enabling Communication between Containers
To enable communication between containers, Docker Compose automatically creates a network for the services defined in the configuration file.
In the example above, Docker Compose creates a network and connects both services (node-app and mongo) to it.
node-app can communicate with mongo using the service name as the hostname.
Example:
JavaScript
// Node.js application code
const mongoose = require('mongoose');

mongoose.connect(process.env.DATABASE_URL, { useNewUrlParser: true });
```

## 12. Containerization Advantages

```DockerFile

Benefits of Docker for MERN Stack Applications
Docker provides several benefits for deploying MERN Stack applications:
Isolation: Containers run independently, ensuring consistent environments.
Portability: Containers are platform-agnostic, simplifying deployment.
Lightweight: Containers share the host kernel, reducing overhead.
Scalability: Containers can be easily replicated and load-balanced.
Development Efficiency: Containers streamline development workflows.
Real-World Use Case:
A development team uses Docker to containerize their MERN Stack application. Each team member can run the application consistently, regardless of their local environment. During testing, Docker Compose enables seamless communication between the Node.js server and MongoDB database. Upon deployment, Docker containers ensure efficient resource utilization and scalability.
```

## 13. Basic Git Commands

```Github

Initializing a Repository, Making a Commit, and Pushing to GitHub
Step 1: Initialize a Repository
Bash
# Create a new directory for your project
mkdir my-project
cd my-project

# Initialize a Git repository
git init

# Link the repository to GitHub
git remote add origin https://github.com/your-username/your-repo-name.git
Step 2: Make a Commit
Bash
# Add files to the staging area
git add .

# Commit changes
git commit -m "Initial commit"
Step 3: Push to GitHub
Bash
# Push changes to the remote repository
git push -u origin master
```

## 14. Branching Strategy

````Github
GitFlow Branching Strategy
GitFlow is a popular branching strategy that uses two main branches:
master: Production-ready code
develop: Development branch for new features
Steps to Implement GitFlow for a New Feature
Create a feature branch from develop:
Bash
git checkout develop
git checkout -b feature/new-feature
2.  Work on the feature and commit changes.
3.  Merge **feature/new-feature** into **develop**:
    ```bash
git checkout develop
git merge feature/new-feature
Create a release branch from develop:
Bash
git checkout -b release/v1.0
5.  Merge **release/v1.0** into **master** and **develop**:
    ```bash
git checkout master
git merge release/v1.0
git checkout develop
git merge release/v1.0
````

## 15. Merging and Resolving Conflicts

```Github

Step-by-Step Guide to Resolve Merge Conflicts
Step 1: Merge Feature Branch into Main Branch
Bash
# Checkout the main branch
git checkout main

# Merge the feature branch
git merge feature/new-feature
Step 2: Identify Conflicts
Git will highlight conflicting files.
Step 3: Resolve Conflicts Manually
Open conflicting files.
Look for conflict markers (<<<<<<<, >>>>>>>, =======).
Resolve conflicts manually.
Step 4: Add Resolved Files and Commit
Bash
# Add resolved files
git add .

# Commit resolved changes
git commit -m "Resolved merge conflicts"
```

## 16. CI/CD Integration with GitHub Actions

```Github
Basic CI/CD Pipeline for Node.js Application
Create a new file .github/workflows/nodejs.yml:
YAML
name: Node.js CI/CD

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Deploy to production
        uses: deploy-to-production-action@v1
Explanation:
Trigger pipeline on push to main branch.
Run pipeline on Ubuntu environment.
Checkout code, install dependencies, run tests.
Deploy to production using a custom action.
```

## 17: Data Cleaning

```Python

Python Script to Clean Data
Python
import pandas as pd

# Read CSV file
def read_csv(file_path):
    try:
        data = pd.read_csv(file_path)
        return data
    except FileNotFoundError:
        print("File not found.")
        return None
    except pd.errors.EmptyDataError:
        print("File is empty.")
        return None

# Drop rows with missing values
def drop_missing_values(data):
    cleaned_data = data.dropna()
    return cleaned_data

# Output cleaned data to CSV
def output_cleaned_data(data, output_file):
    data.to_csv(output_file, index=False)

# Main function
def main():
    file_path = 'input.csv'
    output_file = 'cleaned_data.csv'

    data = read_csv(file_path)
    if data is not None:
        cleaned_data = drop_missing_values(data)
        output_cleaned_data(cleaned_data, output_file)
        print("Data cleaned and saved to", output_file)

if __name__ == "__main__":
    main()

```

## 18. Data Manipulation

```Python
Python Function to Filter Data
Python
import pandas as pd

# Function to filter top 5 rows where 'age' > 30
def filter_data(data):
    filtered_data = data[data['age'] > 30].head(5)
    return filtered_data

# Example usage:
data = pd.DataFrame({
    'name': ['John', 'Alice', 'Bob', 'Charlie', 'Dave', 'Emily'],
    'age': [25, 35, 40, 32, 38, 45]
})

filtered_data = filter_data(data)
print(filtered_data)
```

## 19. Data Visualization

Bar Chart to Visualize User Ages
Python

```Python

import matplotlib.pyplot as plt
import pandas as pd

# Sample dataset
data = pd.DataFrame({
    'age': [20, 25, 30, 35, 40, 45, 50]
})

# Create bar chart
plt.figure(figsize=(8, 6))
plt.bar(data['age'], [10, 15, 20, 25, 30, 35, 40])  # Sample frequencies
plt.xlabel('Age')
plt.ylabel('Frequency')
plt.title('Distribution of User Ages')
plt.show()
```

## 20. Descriptive Statistics

Script to Calculate Mean, Median, and Standard Deviation
Python

```Python

import numpy as np
import pandas as pd

# Sample dataset
data = pd.DataFrame({
    'age': [20, 25, 30, 35, 40, 45, 50]
})

# Calculate statistics
mean_age = np.mean(data['age'])
median_age = np.median(data['age'])
std_dev_age = np.std(data['age'])

print("Mean Age:", mean_age)
print("Median Age:", median_age)
print("Standard Deviation of Age:", std_dev_age)
```
