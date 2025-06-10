---
sidebar_position: 4

title: Basic Crud Operations
---
# CRUD Operations

Master data management with Cocobase's intuitive CRUD (Create, Read, Update, Delete) operations. This guide covers everything from basic operations to advanced querying and real-time updates.

## Basic CRUD Operations

### Creating Documents

The `createDocument` method adds new documents to your collections:

```typescript
import db from '../lib/cocobase';

// Create a simple document
const user = await db.createDocument('users', {
  name: 'John Doe',
  email: 'john@example.com',
  age: 30
});

console.log(user.id); // Auto-generated document ID
```

#### With Custom ID

```typescript
// Create document with custom ID
const post = await db.createDocument('posts', {
  title: 'My First Post',
  content: 'Hello, world!',
  authorId: user.id
}, 'my-custom-post-id');
```

#### Batch Creation

```typescript
// Create multiple documents at once
const posts = await db.createDocuments('posts', [
  { title: 'Post 1', content: 'Content 1' },
  { title: 'Post 2', content: 'Content 2' },
  { title: 'Post 3', content: 'Content 3' }
]);
```

### Reading Documents

#### Get Single Document

```typescript
// Get document by ID
const user = await db.getDocument('users', 'user-id-123');

if (user) {
  console.log(user.name);
} else {
  console.log('User not found');
}
```

#### List All Documents

```typescript
// Get all documents in a collection
const allUsers = await db.listDocuments('users');

console.log(`Found ${allUsers.length} users`);
allUsers.forEach(user => console.log(user.name));
```

#### With Pagination

```typescript
// Paginated results
const result = await db.listDocuments('posts', {
  limit: 10,
  offset: 0
});

console.log(`Page 1: ${result.documents.length} posts`);
console.log(`Total: ${result.total} posts`);

// Get next page
const nextPage = await db.listDocuments('posts', {
  limit: 10,
  offset: 10
});
```

### Updating Documents

#### Full Update

```typescript
// Replace entire document
const updatedUser = await db.updateDocument('users', 'user-id-123', {
  name: 'Jane Doe',
  email: 'jane@example.com',
  age: 28,
  lastLogin: new Date()
});
```

#### Partial Update

```typescript
// Update only specific fields
const updatedUser = await db.updateDocument('users', 'user-id-123', {
  lastLogin: new Date(),
  loginCount: 5
}, { merge: true }); // merge: true for partial updates
```

#### Increment/Decrement Operations

```typescript
// Increment numeric fields
await db.updateDocument('posts', 'post-id-123', {
  views: db.increment(1),
  likes: db.increment(5)
});

// Decrement
await db.updateDocument('users', 'user-id-123', {
  credits: db.increment(-10)
});
```

#### Array Operations

```typescript
// Add items to array
await db.updateDocument('posts', 'post-id-123', {
  tags: db.arrayUnion(['javascript', 'typescript'])
});

// Remove items from array
await db.updateDocument('posts', 'post-id-123', {
  tags: db.arrayRemove(['old-tag'])
});
```

### Deleting Documents

#### Delete Single Document

```typescript
// Delete a document
await db.deleteDocument('users', 'user-id-123');
```

#### Delete Multiple Documents

```typescript
// Delete multiple documents by IDs
await db.deleteDocuments('posts', [
  'post-id-1',
  'post-id-2',
  'post-id-3'
]);
```

#### Conditional Delete

```typescript
// Delete documents matching criteria
await db.deleteDocuments('users', {
  where: [
    ['status', '==', 'inactive'],
    ['lastLogin', '<', new Date('2023-01-01')]
  ]
});
```

## Advanced Querying

### Filtering

#### Basic Filters

```typescript
// Simple equality filter
const activeUsers = await db.listDocuments('users', {
  where: [['status', '==', 'active']]
});

// Multiple filters (AND operation)
const recentPosts = await db.listDocuments('posts', {
  where: [
    ['published', '==', true],
    ['createdAt', '>', new Date('2024-01-01')]
  ]
});
```

#### Comparison Operators

```typescript
// Greater than
const adultUsers = await db.listDocuments('users', {
  where: [['age', '>', 18]]
});

// Less than or equal
const recentPosts = await db.listDocuments('posts', {
  where: [['createdAt', '<=', new Date()]]
});

// Not equal
const nonAdmins = await db.listDocuments('users', {
  where: [['role', '!=', 'admin']]
});

// In array
const specificUsers = await db.listDocuments('users', {
  where: [['id', 'in', ['user1', 'user2', 'user3']]]
});

// Not in array
const excludedUsers = await db.listDocuments('users', {
  where: [['role', 'not-in', ['banned', 'suspended']]]
});
```

#### Array Queries

```typescript
// Array contains
const jsDevs = await db.listDocuments('users', {
  where: [['skills', 'array-contains', 'javascript']]
});

// Array contains any
const webDevs = await db.listDocuments('users', {
  where: [['skills', 'array-contains-any', ['html', 'css', 'javascript']]]
});
```

#### Text Search

```typescript
// Text search (case-insensitive)
const searchResults = await db.listDocuments('posts', {
  where: [['title', 'contains', 'react']]
});

// Starts with
const nameSearch = await db.listDocuments('users', {
  where: [['name', 'startsWith', 'John']]
});

// Ends with
const emailSearch = await db.listDocuments('users', {
  where: [['email', 'endsWith', '@company.com']]
});
```

### Sorting

```typescript
// Sort by single field
const usersByAge = await db.listDocuments('users', {
  orderBy: [['age', 'desc']]
});

// Sort by multiple fields
const sortedPosts = await db.listDocuments('posts', {
  orderBy: [
    ['featured', 'desc'],
    ['createdAt', 'desc']
  ]
});
```

### Complex Queries

#### OR Queries

```typescript
// OR operation using multiple queries
const [admins, moderators] = await Promise.all([
  db.listDocuments('users', {
    where: [['role', '==', 'admin']]
  }),
  db.listDocuments('users', {
    where: [['role', '==', 'moderator']]
  })
]);

const staffMembers = [...admins, ...moderators];
```

#### Nested Queries

```typescript
// Query with nested object properties
const posts = await db.listDocuments('posts', {
  where: [
    ['author.role', '==', 'admin'],
    ['metadata.featured', '==', true]
  ]
});
```

## Real-time Subscriptions

### Listen to Document Changes

```typescript
// Listen to a specific document
const unsubscribe = db.subscribeToDocument('users', 'user-id-123', (user) => {
  console.log('User updated:', user);
});

// Stop listening
unsubscribe();
```

### Listen to Collection Changes

```typescript
// Listen to all documents in a collection
const unsubscribe = db.subscribeToCollection('posts', (posts) => {
  console.log(`Collection updated: ${posts.length} posts`);
});

// Listen with filters
const unsubscribe = db.subscribeToCollection('posts', (posts) => {
  console.log(`Active posts: ${posts.length}`);
}, {
  where: [['status', '==', 'published']]
});
```

### Real-time with React

```typescript
// Custom hook for real-time data
import { useState, useEffect } from 'react';
import db from '../lib/cocobase';

function useRealTimeDocument(collection: string, id: string) {
  const [document, setDocument] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const unsubscribe = db.subscribeToDocument(collection, id, (doc) => {
      setDocument(doc);
      setLoading(false);
    });

    return unsubscribe;
  }, [collection, id]);

  return { document, loading };
}

// Usage in component
function UserProfile({ userId }) {
  const { document: user, loading } = useRealTimeDocument('users', userId);

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

## TypeScript Integration

### Typed Collections

```typescript
// Define your data types
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
  role: 'admin' | 'user' | 'moderator';
  skills: string[];
  createdAt: Date;
  updatedAt: Date;
}

interface Post {
  id: string;
  title: string;
  content: string;
  authorId: string;
  published: boolean;
  tags: string[];
  createdAt: Date;
  updatedAt: Date;
}

// Use generic types for type safety
const users = db.collection<User>('users');
const posts = db.collection<Post>('posts');

// Now you get full type safety
const user = await users.create({
  name: 'John Doe',
  email: 'john@example.com',
  age: 30,
  role: 'user',
  skills: ['javascript', 'react'],
  createdAt: new Date(),
  updatedAt: new Date()
});

// TypeScript will catch errors
const invalidUser = await users.create({
  name: 'Jane',
  email: 'jane@example.com',
  age: 'thirty', // Error: Type 'string' is not assignable to type 'number'
  role: 'superuser', // Error: Type 'superuser' is not assignable to type 'admin' | 'user' | 'moderator'
});