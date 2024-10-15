# Basic Implementation of TypeORM

## Introduction to TypeORM
TypeORM is an Object-Relational Mapping (ORM) library for TypeScript and JavaScript that allows developers to interact with databases in an object-oriented way. It supports various databases such as MySQL, PostgreSQL, SQLite, and more.

This guide provides a step-by-step implementation of a basic TypeORM application using Node.js and Express.

---

## Prerequisites

1. **Node.js**: Ensure you have Node.js installed on your machine. You can download it from [nodejs.org](https://nodejs.org/).
2. **TypeScript**: Familiarity with TypeScript is helpful but not mandatory.
3. **Database**: A relational database (like PostgreSQL or MySQL) installed and running.

---

## Step 1: Setting Up the Project

### 1.1 Create a New Directory

```bash
mkdir typeorm-example
cd typeorm-example
```

### 1.2 Initialize a New Node.js Project

```bash
npm init -y
```

### 1.3 Install Dependencies

```bash
npm install typeorm reflect-metadata mysql2 express
npm install --save-dev typescript @types/node @types/express ts-node
```

### 1.4 Create tsconfig.json

Run the following command to create a TypeScript configuration file:

```bash
npx tsc --init
```
Then, update tsconfig.json with the following settings:

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "baseUrl": "./src",
    "typeRoots": ["node_modules/@types"]
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules"]
}
```

---

## Step 2: Setting Up TypeORM
### 2.1 Create Database Connection

Create a new directory called `src`, and inside it, create a file named `index.ts`.

```bash
mkdir src
touch src/index.ts
```

### 2.2 Add Connection Code

In `src/index.ts`, set up the database connection using TypeORM:

```typescript
import "reflect-metadata";
import { createConnection } from "typeorm";
import express from "express";

const app = express();
app.use(express.json());

createConnection({
  type: "mysql", // Change to your database type (e.g., 'postgres', 'sqlite', etc.)
  host: "localhost",
  port: 3306, // Default MySQL port
  username: "your-username", // Your database username
  password: "your-password", // Your database password
  database: "test", // Your database name
  synchronize: true, // Auto-create database schema
  logging: true,
  entities: [
    // Add entities here
  ],
})
  .then(() => {
    console.log("Connected to the database!");
    
    // Start server
    app.listen(3000, () => {
      console.log("Server running on http://localhost:3000");
    });
  })
  .catch((error) => console.log(error));
```

---

## Step 3: Create an Entity
### 3.1 Create a User Entity

Create a new file named `User.ts` inside the `src` directory.

```bash
touch src/User.ts
```

### 3.2 Define the User Entity

In src/User.ts, define a simple User entity:

```typescript
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm";

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  email: string;
}
```

### 3.3 Update the Connection Code

Update src/index.ts to include the User entity in the connection configuration:

```typescript
import { User } from "./User"; // Add this line

// ...

createConnection({
  // ...
  entities: [User], // Add User entity here
  // ...
});
```

---

## Step 4: Implement CRUD Operations
### 4.1 Create User Endpoint

Add the following code to src/index.ts to create a new user:

```typescript
app.post("/users", async (req, res) => {
  const user = new User();
  user.name = req.body.name;
  user.email = req.body.email;

  const connection = await createConnection();
  const userRepository = connection.getRepository(User);

  await userRepository.save(user);
  res.status(201).json(user);
});
```

### 4.2 Read Users Endpoint

Add the following code to read all users:

```typescript
app.get("/users", async (req, res) => {
  const connection = await createConnection();
  const userRepository = connection.getRepository(User);

  const users = await userRepository.find();
  res.json(users);
});
```

### 4.3 Update User Endpoint

Add the following code to update a user:

```typescript
app.put("/users/:id", async (req, res) => {
  const connection = await createConnection();
  const userRepository = connection.getRepository(User);

  const user = await userRepository.findOne(req.params.id);
  if (!user) {
    return res.status(404).json({ message: "User not found" });
  }

  user.name = req.body.name;
  user.email = req.body.email;

  await userRepository.save(user);
  res.json(user);
});
```

### 4.4 Delete User Endpoint

Add the following code to delete a user:

```typescript
app.delete("/users/:id", async (req, res) => {
  const connection = await createConnection();
  const userRepository = connection.getRepository(User);

  const result = await userRepository.delete(req.params.id);
  if (result.affected === 0) {
    return res.status(404).json({ message: "User not found" });
  }

  res.status(204).send();
});
```

---

## Step 5: Running the Application
### 5.1 Compile TypeScript to JavaScript

Run the following command to compile the TypeScript files:

```bash
npx tsc
```

### 5.2 Start the Application

Run the compiled JavaScript with Node.js:

```bash
node dist/index.js
```

### 5.3 Test the Endpoints

You can now test the CRUD operations using tools like Postman or curl:

- Create a new user: `POST http://localhost:3000/users`
- Read all users: `GET http://localhost:3000/users`
- Update a user: `PUT http://localhost:3000/users/:id`
- Delete a user: `DELETE http://localhost:3000/users/:id`

---

## Conclusion

You have now implemented a basic TypeORM application with CRUD operations. TypeORM simplifies the process of working with databases by allowing you to interact with your database using object-oriented concepts instead of raw SQL queries.

## Further Reading

- [TypeORM Documentation](https://typeorm.io/)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)