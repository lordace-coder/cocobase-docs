---
title: Project Settup
slug: /
sidebar_position: 2
---

# Project Setup

Get your Cocobase project up and running in minutes. This guide covers everything from initial setup to advanced configuration options.

## Prerequisites

Before starting, ensure you have:

- Node.js 16.x or higher
- npm, yarn, or pnpm package manager
- A modern code editor (VS Code recommended)

## Creating Your First Project

### 1. Sign Up and Create Project

1. Visit [cocobase.buzz](https://cocobase.buzz) and create your account
2. Click "Create New Project" in your dashboard
3. Choose your project name and region
4. Copy your API key from the project settings

### 2. Installation

Install the Cocobase SDK in your project:

```bash
# Using npm
npm install cocobase

# Using yarn
yarn add cocobase

# Using pnpm
pnpm add cocobase
```

### 3. Basic Configuration

Create a new file to initialize Cocobase:

```typescript
// lib/cocobase.ts
import { Cocobase } from "cocobase";

const db = new Cocobase({
  apiKey: process.env.COCOBASE_API_KEY, // Your API key
  projectId: process.env.COCOBASE_PROJECT_ID, // Your project ID
  environment: "production", // or 'development'
});

export default db;
```

### 4. Environment Variables

Create a `.env.local` file in your project root:

```env
COCOBASE_API_KEY=your_api_key_here
COCOBASE_PROJECT_ID=your_project_id_here
```

## Framework-Specific Setup

### React/Next.js Setup

```typescript
// pages/_app.tsx (Next.js) or App.tsx (React)
import { CocobaseProvider } from "cocobase/react";
import db from "../lib/cocobase";

function MyApp({ Component, pageProps }) {
  return (
    <CocobaseProvider client={db}>
      <Component {...pageProps} />
    </CocobaseProvider>
  );
}

export default MyApp;
```

Using the React hook:

```typescript
// components/UserProfile.tsx
import { useCocobase } from "cocobase/react";

export default function UserProfile() {
  const { db, user, loading } = useCocobase();

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>Welcome, {user?.name}!</h1>
    </div>
  );
}
```

### Vue/Nuxt Setup

```typescript
// plugins/cocobase.client.ts (Nuxt 3)
import { Cocobase } from "cocobase";

export default defineNuxtPlugin(() => {
  const db = new Cocobase({
    apiKey: useRuntimeConfig().public.cocobaseApiKey,
    projectId: useRuntimeConfig().public.cocobaseProjectId,
  });

  return {
    provide: {
      db,
    },
  };
});
```

Using in Vue components:

```vue
<!-- pages/profile.vue -->
<template>
  <div>
    <h1>Welcome, {{ user?.name }}!</h1>
  </div>
</template>

<script setup>
const { $db } = useNuxtApp();
const user = ref(null);

onMounted(async () => {
  user.value = await $db.getCurrentUser();
});
</script>
```

### React Native Setup

```typescript
// App.tsx
import { Cocobase } from "cocobase/react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";

const db = new Cocobase({
  apiKey: "your_api_key",
  projectId: "your_project_id",
  storage: AsyncStorage, // For session persistence
});

export default function App() {
  return (
    <CocobaseProvider client={db}>
      <YourAppComponents />
    </CocobaseProvider>
  );
}
```

## Advanced Configuration

### Custom Configuration Options

```typescript
const db = new Cocobase({
  apiKey: "your_api_key",
  projectId: "your_project_id",

  // Optional configurations
  environment: "production", // 'development' | 'staging' | 'production'
  region: "us-east-1", // Specify your preferred region
  timeout: 10000, // Request timeout in milliseconds
  retries: 3, // Number of automatic retries

  // Custom headers
  headers: {
    "X-Custom-Header": "value",
  },

  // Real-time configuration
  realtime: {
    enabled: true,
    reconnectInterval: 5000,
    maxReconnectAttempts: 10,
  },

  // Caching configuration
  cache: {
    enabled: true,
    ttl: 300000, // 5 minutes
    maxSize: 100, // Maximum cached items
  },
});
```

### TypeScript Configuration

For better type safety, define your data models:

```typescript
// types/index.ts
export interface User {
  id: string;
  name: string;
  email: string;
  avatar?: string;
  role: "admin" | "user" | "moderator";
  createdAt: Date;
  updatedAt: Date;
}

export interface Post {
  id: string;
  title: string;
  content: string;
  authorId: string;
  published: boolean;
  tags: string[];
  createdAt: Date;
  updatedAt: Date;
}

export interface Comment {
  id: string;
  postId: string;
  authorId: string;
  content: string;
  createdAt: Date;
}
```

Use generic types with Cocobase:

```typescript
// lib/cocobase.ts
import { Cocobase } from "cocobase";
import type { User, Post, Comment } from "../types";

const db = new Cocobase({
  apiKey: process.env.COCOBASE_API_KEY!,
  projectId: process.env.COCOBASE_PROJECT_ID!,
});

// Typed database operations
export const users = db.collection<User>("users");
export const posts = db.collection<Post>("posts");
export const comments = db.collection<Comment>("comments");

export default db;
```

## Project Structure

Here's a recommended project structure for Cocobase applications:

```
your-project/
├── lib/
│   ├── cocobase.ts          # Cocobase configuration
│   ├── auth.ts              # Authentication helpers
│   └── collections.ts       # Collection helpers
├── types/
│   └── index.ts             # TypeScript type definitions
├── components/
│   ├── auth/
│   │   ├── LoginForm.tsx
│   │   └── RegisterForm.tsx
│   └── ui/
├── pages/ (or app/)
│   ├── login.tsx
│   ├── register.tsx
│   └── dashboard.tsx
├── hooks/
│   ├── useAuth.ts
│   └── useData.ts
└── .env.local               # Environment variables
```

## Environment Management

### Development Environment

```typescript
// lib/cocobase.dev.ts
const db = new Cocobase({
  apiKey: "dev_api_key",
  projectId: "dev_project_id",
  environment: "development",
  debug: true, // Enable debug logging
});
```

### Production Environment

```typescript
// lib/cocobase.prod.ts
const db = new Cocobase({
  apiKey: process.env.COCOBASE_API_KEY,
  projectId: process.env.COCOBASE_PROJECT_ID,
  environment: "production",
  debug: false,
  timeout: 15000,
  retries: 5,
});
```

## Testing Setup

### Unit Testing with Jest

```typescript
// __tests__/setup.ts
import { Cocobase } from "cocobase";

// Mock Cocobase for testing
jest.mock("cocobase", () => ({
  Cocobase: jest.fn().mockImplementation(() => ({
    createDocument: jest.fn(),
    getDocument: jest.fn(),
    updateDocument: jest.fn(),
    deleteDocument: jest.fn(),
    listDocuments: jest.fn(),
    register: jest.fn(),
    login: jest.fn(),
    logout: jest.fn(),
  })),
}));
```

### Integration Testing

```typescript
// __tests__/integration.test.ts
import db from "../lib/cocobase";

describe("Cocobase Integration", () => {
  beforeAll(async () => {
    // Setup test data
    await db.createDocument("test-users", {
      name: "Test User",
      email: "test@example.com",
    });
  });

  afterAll(async () => {
    // Cleanup test data
    const users = await db.listDocuments("test-users");
    for (const user of users) {
      await db.deleteDocument("test-users", user.id);
    }
  });

  it("should create and retrieve documents", async () => {
    const doc = await db.createDocument("test-posts", {
      title: "Test Post",
      content: "This is a test post",
    });

    const retrieved = await db.getDocument("test-posts", doc.id);
    expect(retrieved.title).toBe("Test Post");
  });
});
```

## Error Handling

### Global Error Handler

```typescript
// lib/errorHandler.ts
import { CocobaseError } from "cocobase";

export function handleCocobaseError(error: CocobaseError) {
  switch (error.code) {
    case "AUTH_REQUIRED":
      // Redirect to login
      window.location.href = "/login";
      break;
    case "PERMISSION_DENIED":
      // Show permission error
      alert("You do not have permission to perform this action");
      break;
    case "NETWORK_ERROR":
      // Show network error
      alert("Network error. Please check your connection");
      break;
    default:
      console.error("Cocobase error:", error);
  }
}
```

### Using with React Error Boundaries

```typescript
// components/ErrorBoundary.tsx
import { Component, ErrorInfo, ReactNode } from "react";
import { CocobaseError } from "cocobase";

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export class CocobaseErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    if (error instanceof CocobaseError) {
      console.error("Cocobase error:", error.code, error.message);
    }
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <p>{this.state.error?.message}</p>
        </div>
      );
    }

    return this.props.children;
  }
}
```

## Performance Optimization

### Caching Strategy

```typescript
// lib/cache.ts
import db from "./cocobase";

class DataCache {
  private cache = new Map();
  private ttl = 5 * 60 * 1000; // 5 minutes

  async get<T>(key: string, fetcher: () => Promise<T>): Promise<T> {
    const cached = this.cache.get(key);

    if (cached && Date.now() - cached.timestamp < this.ttl) {
      return cached.data;
    }

    const data = await fetcher();
    this.cache.set(key, { data, timestamp: Date.now() });
    return data;
  }

  clear(key?: string) {
    if (key) {
      this.cache.delete(key);
    } else {
      this.cache.clear();
    }
  }
}

export const cache = new DataCache();

// Usage
export async function getCachedPosts() {
  return cache.get("posts", () => db.listDocuments("posts"));
}
```

## Next Steps

Now that your project is set up, you're ready to:

1. [Learn about CRUD operations →](./crud-operations)
2. [Implement authentication →](./authentication)
3. [Explore real-time features →](./realtime)
4. [Set up file storage →](./file-storage)

## Troubleshooting

### Common Issues

**API Key Issues**

- Ensure your API key is correct and not expired
- Check that environment variables are properly loaded
- Verify your project ID matches your dashboard

**Network Issues**

- Check your internet connection
- Verify firewall settings allow HTTPS requests
- Ensure your region is correctly configured

**TypeScript Issues**

- Install type definitions: `npm install @types/node`
- Enable strict mode in tsconfig.json for better type safety
- Use generic types for better IntelliSense

Need help? Join our [Discord community](https://discord.gg/cocobase) or contact support at hello@cocobase.buzz.
