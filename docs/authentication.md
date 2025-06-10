---
sidebar_position: 3

title: Handling Authentication
---

# Authentication

Cocobase provides a complete authentication system that handles user registration, login, session management, and user profiles out of the box. No complex setup required - just simple, secure authentication that works.

## Overview

Cocobase authentication includes:

- **User Registration**: Create new user accounts with email and password
- **User Login**: Authenticate existing users
- **Session Management**: Automatic token handling and persistence
- **User Profiles**: Extensible user data with custom fields
- **Security**: Industry-standard security practices built-in

## Getting Started

### Basic Authentication Setup

```typescript
import db from "../lib/cocobase";

// Register a new user
const newUser = await db.register("user@example.com", "securePassword123");
console.log("User registered:", newUser.email);

// Login an existing user
const user = await db.login("user@example.com", "securePassword123");
console.log("User logged in:", user.email);

// Logout the current user
await db.logout();
console.log("User logged out");
```

## User Registration

The `register` method creates a new user account and automatically logs them in.

### Basic Registration

```typescript
// Simple registration with email and password
const user = await db.register("john@example.com", "mySecurePassword");

console.log("New user created:");
console.log("ID:", user.id);
console.log("Email:", user.email);
console.log("Created at:", user.createdAt);
```

### Registration with Additional Data

You can include additional user data during registration:

```typescript
// Register user with profile information
const user = await db.register("jane@example.com", "password123", {
  name: "Jane Smith",
  role: "admin",
  department: "Engineering",
  preferences: {
    theme: "dark",
    notifications: true,
  },
});

console.log("User registered with profile:", user.name);
console.log("User role:", user.role);
```

### Registration Examples

**E-commerce User Registration**

```typescript
const customer = await db.register("customer@shop.com", "password123", {
  firstName: "Alice",
  lastName: "Johnson",
  phone: "+1234567890",
  shippingAddress: {
    street: "123 Main St",
    city: "Anytown",
    state: "CA",
    zipCode: "12345",
    country: "USA",
  },
  preferences: {
    newsletter: true,
    smsUpdates: false,
  },
});
```

**Team Member Registration**

```typescript
const teamMember = await db.register("dev@company.com", "securePass", {
  name: "Bob Developer",
  role: "developer",
  team: "frontend",
  startDate: new Date(),
  skills: ["React", "TypeScript", "Node.js"],
  manager: "manager-user-id",
});
```

**Student Registration**

```typescript
const student = await db.register("student@university.edu", "studentPass", {
  firstName: "Sarah",
  lastName: "Wilson",
  studentId: "STU2024001",
  major: "Computer Science",
  year: "sophomore",
  courses: [],
});
```

## User Login

The `login` method authenticates users with their email and password.

### Basic Login

```typescript
try {
  const user = await db.login("user@example.com", "password123");

  console.log("Login successful!");
  console.log("Welcome back,", user.name || user.email);

  // Redirect to dashboard or home page
  window.location.href = "/dashboard";
} catch (error) {
  console.error("Login failed:", error.message);
  // Show error message to user
}
```

### Login with Error Handling

```typescript
async function handleLogin(email, password) {
  try {
    const user = await db.login(email, password);

    // Login successful
    return {
      success: true,
      user: user,
      message: "Login successful!",
    };
  } catch (error) {
    // Handle different types of login errors
    let errorMessage = "Login failed. Please try again.";

    if (error.code === "INVALID_CREDENTIALS") {
      errorMessage = "Invalid email or password.";
    } else if (error.code === "USER_NOT_FOUND") {
      errorMessage = "No account found with this email.";
    } else if (error.code === "NETWORK_ERROR") {
      errorMessage = "Network error. Please check your connection.";
    }

    return {
      success: false,
      message: errorMessage,
    };
  }
}

// Usage
const result = await handleLogin(email, password);
if (result.success) {
  console.log("Logged in as:", result.user.email);
} else {
  console.error(result.message);
}
```

## Session Management

Cocobase automatically handles user sessions, including token storage and persistence.

### Getting Current User

```typescript
// Get the currently logged-in user
const currentUser = await db.getCurrentUser();

if (currentUser) {
  console.log("User is logged in:", currentUser.email);
} else {
  console.log("No user is logged in");
  // Redirect to login page
}
```

### Checking Authentication Status

```typescript
// Check if a user is currently authenticated
const isAuthenticated = await db.isAuthenticated();

if (isAuthenticated) {
  console.log("User is authenticated");
  // Show authenticated content
} else {
  console.log("User is not authenticated");
  // Show login form
}
```

### Session Persistence

Sessions are automatically persisted across browser sessions and app restarts:

```typescript
// This works automatically - no additional code needed
// When user returns to your app, their session is restored

window.addEventListener("load", async () => {
  const user = await db.getCurrentUser();

  if (user) {
    console.log("Welcome back,", user.email);
    // User is still logged in from previous session
  }
});
```

## User Logout

The `logout` method signs out the current user and clears their session.

```typescript
// Simple logout
await db.logout();
console.log("User logged out successfully");

// Logout with redirect
async function handleLogout() {
  try {
    await db.logout();
    console.log("Logged out successfully");

    // Redirect to login page
    window.location.href = "/login";
  } catch (error) {
    console.error("Logout failed:", error.message);
  }
}
```
