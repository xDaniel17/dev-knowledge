---
title: PostgreSQL & Supabase
date: 2026-03-30
tags: [database, backend, sql, supabase]
category: Tech-Services
author: OpenCode
version: 1.0.0
status: active
---

# PostgreSQL & Supabase - Complete Guide

PostgreSQL is a powerful, open-source relational database. Supabase is an open-source Firebase alternative that provides PostgreSQL with real-time capabilities, authentication, and storage.

## Table of Contents
1. [PostgreSQL Setup](#postgresql-setup)
2. [Basic SQL Operations](#basic-sql-operations)
3. [Advanced Queries](#advanced-queries)
4. [Supabase Integration](#supabase-integration)
5. [Authentication](#authentication)
6. [Real-time Subscriptions](#real-time-subscriptions)
7. [Storage](#storage)
8. [Best Practices](#best-practices)
9. [Performance Optimization](#performance-optimization)
10. [Troubleshooting](#troubleshooting)

---

## PostgreSQL Setup

### macOS Installation (Homebrew)

```bash
# Install PostgreSQL
brew install postgresql@15

# Start PostgreSQL service
brew services start postgresql@15

# Connect to PostgreSQL
psql postgres
```

### Windows Installation (PowerShell)

```powershell
# Using Chocolatey
choco install postgresql

# Or download from official site
# https://www.postgresql.org/download/windows/

# Start PostgreSQL service (after installation)
# Services app -> PostgreSQL -> Start
```

### Create Database & User

```sql
-- Connect to PostgreSQL
psql postgres

-- Create database
CREATE DATABASE mydatabase;

-- Create user
CREATE USER myuser WITH PASSWORD 'secure_password';

-- Grant privileges
ALTER ROLE myuser SET client_encoding TO 'utf8';
ALTER ROLE myuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE myuser SET default_transaction_deferrable TO on;
ALTER ROLE myuser SET default_time_zone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE mydatabase TO myuser;

-- Connect to new database
\c mydatabase

-- Revoke public schema permissions (security)
REVOKE ALL ON SCHEMA public FROM PUBLIC;
GRANT ALL ON SCHEMA public TO myuser;
```

### Connect from Application

```bash
# Connection string format
postgresql://username:password@localhost:5432/database_name

# Example with environment variable
DATABASE_URL=postgresql://myuser:secure_password@localhost:5432/mydatabase
```

---

## Basic SQL Operations

### Create Tables

```sql
-- Create users table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  username VARCHAR(100) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  profile_picture_url TEXT,
  bio TEXT,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create posts table
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title VARCHAR(255) NOT NULL,
  content TEXT NOT NULL,
  published BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create comments table
CREATE TABLE comments (
  id SERIAL PRIMARY KEY,
  post_id INTEGER NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
```

### Insert Data

```sql
-- Insert single row
INSERT INTO users (email, username, password_hash)
VALUES ('john@example.com', 'john_doe', 'hashed_password');

-- Insert multiple rows
INSERT INTO users (email, username, password_hash) VALUES
  ('jane@example.com', 'jane_doe', 'hashed_password'),
  ('bob@example.com', 'bob_smith', 'hashed_password');

-- Insert with RETURNING (get generated ID)
INSERT INTO users (email, username, password_hash)
VALUES ('alice@example.com', 'alice_wonder', 'hashed_password')
RETURNING id, email, created_at;
```

### Read Data

```sql
-- Select all users
SELECT * FROM users;

-- Select specific columns
SELECT id, email, username FROM users;

-- With WHERE clause
SELECT * FROM users WHERE is_active = true;

-- With LIKE for text search
SELECT * FROM users WHERE email LIKE '%@example.com';

-- Limit and offset (pagination)
SELECT * FROM users LIMIT 10 OFFSET 20;

-- Order by
SELECT * FROM users ORDER BY created_at DESC;

-- Count
SELECT COUNT(*) as total_users FROM users;
```

### Update Data

```sql
-- Update single column
UPDATE users SET username = 'john_updated' WHERE id = 1;

-- Update multiple columns
UPDATE users
SET username = 'john_v2', updated_at = CURRENT_TIMESTAMP
WHERE id = 1;

-- Conditional update
UPDATE posts
SET published = true, updated_at = CURRENT_TIMESTAMP
WHERE user_id = 1 AND created_at > NOW() - INTERVAL '7 days';
```

### Delete Data

```sql
-- Delete specific record
DELETE FROM users WHERE id = 1;

-- Delete with condition
DELETE FROM posts WHERE published = false AND created_at < NOW() - INTERVAL '30 days';

-- Delete all (dangerous!)
DELETE FROM users; -- Be careful!
```

---

## Advanced Queries

### Joins

```sql
-- INNER JOIN
SELECT
  u.username,
  p.title,
  p.created_at
FROM users u
INNER JOIN posts p ON u.id = p.user_id
WHERE p.published = true
ORDER BY p.created_at DESC;

-- LEFT JOIN (includes users with no posts)
SELECT
  u.username,
  COUNT(p.id) as post_count
FROM users u
LEFT JOIN posts p ON u.id = p.user_id
GROUP BY u.id, u.username;

-- Multiple joins
SELECT
  u.username,
  p.title,
  c.content as comment_content,
  c.created_at
FROM users u
INNER JOIN posts p ON u.id = p.user_id
INNER JOIN comments c ON p.id = c.post_id
WHERE p.published = true
ORDER BY c.created_at DESC;
```

### Aggregation & Grouping

```sql
-- Group by with aggregation
SELECT
  user_id,
  COUNT(*) as post_count,
  MAX(created_at) as latest_post
FROM posts
GROUP BY user_id
HAVING COUNT(*) > 0
ORDER BY post_count DESC;

-- Subquery
SELECT *
FROM users
WHERE id IN (
  SELECT user_id FROM posts WHERE published = true
);

-- With CTE (Common Table Expression)
WITH published_posts AS (
  SELECT user_id, COUNT(*) as post_count
  FROM posts
  WHERE published = true
  GROUP BY user_id
)
SELECT u.username, pp.post_count
FROM users u
INNER JOIN published_posts pp ON u.id = pp.user_id;
```

### Window Functions

```sql
-- ROW_NUMBER and PARTITION
SELECT
  id,
  username,
  created_at,
  ROW_NUMBER() OVER (ORDER BY created_at) as signup_order,
  RANK() OVER (PARTITION BY EXTRACT(YEAR FROM created_at) ORDER BY created_at) as yearly_rank
FROM users;

-- Running total
SELECT
  id,
  title,
  created_at,
  SUM(1) OVER (ORDER BY created_at) as running_total_posts
FROM posts;
```

---

## Supabase Integration

### Install Supabase Client

```bash
npm install @supabase/supabase-js
```

### Initialize Supabase

```javascript
// src/supabaseClient.js
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = process.env.REACT_APP_SUPABASE_URL;
const supabaseAnonKey = process.env.REACT_APP_SUPABASE_ANON_KEY;

export const supabase = createClient(supabaseUrl, supabaseAnonKey);
```

### Read Data

```javascript
import { supabase } from './supabaseClient';

// Get all users
const { data, error } = await supabase
  .from('users')
  .select('*');

// Get with filter
const { data, error } = await supabase
  .from('users')
  .select('*')
  .eq('is_active', true)
  .order('created_at', { ascending: false })
  .limit(10);

// Get single record
const { data, error } = await supabase
  .from('users')
  .select('*')
  .eq('id', 1)
  .single();
```

### Insert Data

```javascript
// Insert single record
const { data, error } = await supabase
  .from('users')
  .insert({
    email: 'newuser@example.com',
    username: 'newuser',
    password_hash: 'hashed_password',
  })
  .select();

// Bulk insert
const { data, error } = await supabase
  .from('users')
  .insert([
    {
      email: 'user1@example.com',
      username: 'user1',
      password_hash: 'hash1',
    },
    {
      email: 'user2@example.com',
      username: 'user2',
      password_hash: 'hash2',
    },
  ])
  .select();
```

### Update Data

```javascript
// Update record
const { data, error } = await supabase
  .from('users')
  .update({ username: 'updated_username' })
  .eq('id', 1)
  .select();

// Update multiple records
const { data, error } = await supabase
  .from('posts')
  .update({ published: true })
  .eq('user_id', 1)
  .select();
```

### Delete Data

```javascript
// Delete record
const { data, error } = await supabase
  .from('users')
  .delete()
  .eq('id', 1);

// Delete multiple records
const { data, error } = await supabase
  .from('posts')
  .delete()
  .lt('created_at', new Date('2024-01-01'));
```

---

## Authentication

### Email & Password Auth

```javascript
import { supabase } from './supabaseClient';

// Sign up
const signUp = async (email, password) => {
  const { data, error } = await supabase.auth.signUp({
    email,
    password,
  });
  return { data, error };
};

// Sign in
const signIn = async (email, password) => {
  const { data, error } = await supabase.auth.signInWithPassword({
    email,
    password,
  });
  return { data, error };
};

// Sign out
const signOut = async () => {
  const { error } = await supabase.auth.signOut();
  return { error };
};

// Get current user
const getCurrentUser = async () => {
  const { data } = await supabase.auth.getUser();
  return data.user;
};
```

### OAuth Providers

```javascript
// Google sign in
const signInWithGoogle = async () => {
  const { data, error } = await supabase.auth.signInWithOAuth({
    provider: 'google',
  });
  return { data, error };
};

// GitHub sign in
const signInWithGithub = async () => {
  const { data, error } = await supabase.auth.signInWithOAuth({
    provider: 'github',
  });
  return { data, error };
};
```

### Monitor Auth State

```javascript
import { useEffect, useState } from 'react';
import { supabase } from './supabaseClient';

const useAuth = () => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const getUser = async () => {
      const { data } = await supabase.auth.getUser();
      setUser(data.user);
      setLoading(false);
    };

    getUser();

    const { data: listener } = supabase.auth.onAuthStateChange(
      (event, session) => {
        setUser(session?.user || null);
      }
    );

    return () => {
      listener?.subscription.unsubscribe();
    };
  }, []);

  return { user, loading };
};

export default useAuth;
```

---

## Real-time Subscriptions

### Subscribe to Changes

```javascript
// Listen to all posts
const subscription = supabase
  .channel('posts')
  .on(
    'postgres_changes',
    { event: '*', schema: 'public', table: 'posts' },
    (payload) => {
      console.log('Change received!', payload);
    }
  )
  .subscribe();

// Listen to specific event types
supabase
  .channel('posts')
  .on(
    'postgres_changes',
    { event: 'INSERT', schema: 'public', table: 'posts' },
    (payload) => {
      console.log('New post created!', payload.new);
    }
  )
  .on(
    'postgres_changes',
    { event: 'UPDATE', schema: 'public', table: 'posts' },
    (payload) => {
      console.log('Post updated!', payload.new);
    }
  )
  .on(
    'postgres_changes',
    { event: 'DELETE', schema: 'public', table: 'posts' },
    (payload) => {
      console.log('Post deleted!', payload.old);
    }
  )
  .subscribe();

// Unsubscribe
subscription.unsubscribe();
```

### React Hook for Real-time Data

```javascript
import { useEffect, useState } from 'react';
import { supabase } from './supabaseClient';

const useRealtimePosts = () => {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    // Initial fetch
    const fetchPosts = async () => {
      const { data } = await supabase
        .from('posts')
        .select('*')
        .order('created_at', { ascending: false });
      setPosts(data || []);
    };

    fetchPosts();

    // Subscribe to changes
    const subscription = supabase
      .channel('posts')
      .on(
        'postgres_changes',
        { event: '*', schema: 'public', table: 'posts' },
        (payload) => {
          if (payload.eventType === 'INSERT') {
            setPosts((prev) => [payload.new, ...prev]);
          } else if (payload.eventType === 'UPDATE') {
            setPosts((prev) =>
              prev.map((p) =>
                p.id === payload.new.id ? payload.new : p
              )
            );
          } else if (payload.eventType === 'DELETE') {
            setPosts((prev) =>
              prev.filter((p) => p.id !== payload.old.id)
            );
          }
        }
      )
      .subscribe();

    return () => {
      subscription.unsubscribe();
    };
  }, []);

  return posts;
};

export default useRealtimePosts;
```

---

## Storage

### Upload Files

```javascript
// Upload file to bucket
const uploadFile = async (bucket, file, path) => {
  const { data, error } = await supabase.storage
    .from(bucket)
    .upload(path, file, {
      cacheControl: '3600',
      upsert: true,
    });

  return { data, error };
};

// Example usage
const handleFileUpload = async (event) => {
  const file = event.target.files[0];
  const { data, error } = await uploadFile(
    'avatars',
    file,
    `${user.id}/avatar.jpg`
  );
};
```

### Get File URL

```javascript
// Get public URL
const getPublicUrl = (bucket, path) => {
  const { data } = supabase.storage
    .from(bucket)
    .getPublicUrl(path);
  return data.publicUrl;
};

// Example
const avatarUrl = getPublicUrl('avatars', `${user.id}/avatar.jpg`);
```

### Delete Files

```javascript
const deleteFile = async (bucket, path) => {
  const { error } = await supabase.storage
    .from(bucket)
    .remove([path]);
  return { error };
};
```

---

## Best Practices

### Row Level Security (RLS)

Enable RLS policies to ensure data security:

```sql
-- Enable RLS on users table
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Create policy: users can only read their own data
CREATE POLICY "Users can read own data"
  ON users FOR SELECT
  USING (auth.uid() = id);

-- Create policy: users can only update their own data
CREATE POLICY "Users can update own data"
  ON users FOR UPDATE
  USING (auth.uid() = id);

-- Create policy: anyone can create a user
CREATE POLICY "Anyone can create a user"
  ON users FOR INSERT
  WITH CHECK (true);
```

### Data Validation

```javascript
// Validate before insert
const validateUser = (user) => {
  const errors = {};

  if (!user.email || !user.email.includes('@')) {
    errors.email = 'Valid email required';
  }

  if (!user.username || user.username.length < 3) {
    errors.username = 'Username must be at least 3 characters';
  }

  if (!user.password || user.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  }

  return Object.keys(errors).length === 0 ? null : errors;
};
```

### Connection Pooling

Use pgBouncer for connection pooling in production:

```bash
# Install pgBouncer
brew install pgbouncer

# Configure pgbouncer.ini
[databases]
mydatabase = host=localhost port=5432 dbname=mydatabase

[pgbouncer]
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 25
```

---

## Performance Optimization

### Indexes

```sql
-- Create index for frequently searched columns
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_comments_post_id ON comments(post_id);

-- Composite index for multiple columns
CREATE INDEX idx_posts_user_published ON posts(user_id, published);

-- Partial index (only indexed rows where condition is true)
CREATE INDEX idx_active_users ON users(email) WHERE is_active = true;
```

### Query Optimization

```sql
-- Use EXPLAIN to analyze query performance
EXPLAIN ANALYZE
SELECT u.username, COUNT(p.id) as post_count
FROM users u
LEFT JOIN posts p ON u.id = p.user_id
GROUP BY u.id, u.username;

-- Use LIMIT when possible
SELECT * FROM posts LIMIT 100; -- Much faster than SELECT *

-- Use specific columns instead of SELECT *
SELECT id, title, created_at FROM posts; -- Faster
```

### Caching Strategy

```javascript
// Implement caching with React Query
import { useQuery } from '@tanstack/react-query';

const useUserPosts = (userId) => {
  return useQuery({
    queryKey: ['posts', userId],
    queryFn: async () => {
      const { data } = await supabase
        .from('posts')
        .select('*')
        .eq('user_id', userId);
      return data;
    },
    staleTime: 1000 * 60 * 5, // 5 minutes
  });
};
```

---

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| `CORS error` | Add domain to Supabase allowed origins |
| `Authentication failed` | Check JWT token expiration and credentials |
| `RLS policy denies access` | Verify RLS policies allow the operation |
| `Connection timeout` | Check database URL and network connectivity |
| `Out of memory` | Optimize queries, add indexes, use pagination |

### Enable Query Logging

```sql
-- Enable query logging
SET log_statement = 'all';
SET log_duration = on;

-- View logs
SELECT * FROM pg_stat_statements ORDER BY total_time DESC;
```

---

## Related Documentation

- [[Firebase.md]] - Alternative backend solution
- [[MongoDB.md]] - NoSQL database alternative
- [[Next.js/Data-Fetching.md]] - Data fetching patterns
- [[TypeScript]] - Type-safe database operations

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-30 | Initial PostgreSQL & Supabase guide |

