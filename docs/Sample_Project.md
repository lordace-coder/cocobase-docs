--- 
title: Daily Journal
# slug: /sample
sidebar-position: 5
---

# Daily Journal – Cocobase Sample Project

A modern daily journal app built with Cocobase as the backend. Features public and private journals, user authentication, real-time updates, and seamless integration with React.

---

## 🚀 Quick Start / Installation

**Install the Cocobase SDK:**
```bash
npm install cocobase
```

**Initialize the Cocobase client:**
```js
import { Cocobase } from "cocobase";

const db = new Cocobase(import.meta.env.VITE_COCOBASE_API_KEY);
// Use your API key from the Cocobase dashboard, stored in .env
```

---

## 🔐 Authentication

**Register a new user:**
```js
await db.register(username, password);
```

**Login:**
```js
await db.login(username, password);
```

**Logout:**
```js
await db.logout();
```

**Get current user/session:**
```js
const user = await db.getCurrentUser();
if (user) {
  // user.id, user.email, etc.
}
```

---

## 📝 CRUD Operations

**Create a document:**
```js
const doc = await db.createDocument("collection-name", {
  field1: "value",
  field2: 123,
});
```

**Read documents:**
```js
const docs = await db.listDocuments("collection-name");
```

**Update a document:**
```js
await db.updateDocument("collection-name", docId, {
  field1: "new value"
});
```

**Delete a document:**
```js
await db.deleteDocument("collection-name", docId);
```

---

## 🔎 Advanced Querying

**Filtering and pagination:**
```js
const results = await db.listDocuments("collection-name", {
  filter: { status: "published" },
  limit: 10,
  offset: 0,
  sort: { created_at: -1 }
});
```

**Advanced operators:**
```js
const results = await db.listDocuments("collection-name", {
  filter: { age: { $gte: 18, $lte: 30 } }
});
```

---

## ⚡ Real-time Features

**Subscribe to real-time updates:**
```js
const unsubscribe = db.subscribe("collection-name", (change) => {
  // Handle insert, update, delete events
  console.log(change);
});

// To stop listening:
unsubscribe();
```

---

## 🛡️ TypeScript Integration

**Using generics for type safety:**
```ts
type Post = { title: string; content: string; published: boolean };

const posts = await db.listDocuments<Post>("posts");
```

**Defining collection types:**
```ts
const newPost: Post = { title: "Hello", content: "World", published: true };
await db.createDocument<Post>("posts", newPost);
```

---

## 💡 Use Cases

- Content management systems
- E-commerce product catalogs
- Social apps with user-generated content
- Analytics dashboards
- Personal journals and note-taking apps

---

## ⚙️ Framework Integration

**React Example:**
```jsx
import React, { useEffect, useState } from "react";
import db from "./db"; // your Cocobase instance

function Posts() {
  const [posts, setPosts] = useState([]);
  useEffect(() => {
    db.listDocuments("posts").then(setPosts);
  }, []);
  // ...render posts
}
```

**Vue, React Native, etc.:**  
Cocobase works with any JS framework—just import and use as shown above.

---

## 🛠️ Troubleshooting & FAQ

- **API Key not working:**  
  Ensure your .env file is in the project root and restart your dev server.

- **Not authenticated error:**  
  Call `db.login()` or `db.register()` before accessing private data.

- **CORS issues:**  
  Make sure your frontend URL is allowed in the Cocobase dashboard.

- **Real-time not updating:**  
  Check your network and ensure you call `unsubscribe()` when done.

---

## 📚 Links

- [Cocobase Documentation](#)
- [Community & Support](#)
- [GitHub](#)