---
title: Redis Caching
date: 2026-03-30
tags: [caching, database, performance, backend]
category: Tech-Services
author: OpenCode
version: 1.0.0
status: active
---

# Redis - Caching & In-Memory Database Guide

Redis is an in-memory data structure store used for caching, real-time analytics, and message queues. It provides extremely fast read/write operations with support for various data structures.

## Table of Contents
1. [Installation & Setup](#installation--setup)
2. [Data Types & Operations](#data-types--operations)
3. [Node.js Integration](#nodejs-integration)
4. [Caching Strategies](#caching-strategies)
5. [Session Management](#session-management)
6. [Real-time Features](#real-time-features)
7. [Persistence & Durability](#persistence--durability)
8. [Performance Optimization](#performance-optimization)
9. [Monitoring & Debugging](#monitoring--debugging)
10. [Best Practices](#best-practices)

---

## Installation & Setup

### macOS Installation (Homebrew)

```bash
# Install Redis
brew install redis

# Start Redis service
brew services start redis

# Verify installation
redis-cli ping
# Output: PONG
```

### Windows Installation

**Option 1: Windows Subsystem for Linux (WSL2)**
```powershell
# WSL2 Ubuntu
wsl
sudo apt update
sudo apt install redis-server
sudo service redis-server start

# Connect from Windows
redis-cli -h localhost
```

**Option 2: Third-party Windows port**
```powershell
# Install using Chocolatey
choco install redis

# Start Redis service
redis-server.exe

# In another PowerShell session
redis-cli.exe
```

### Docker Setup

```bash
# Run Redis in Docker
docker run -d --name redis -p 6379:6379 redis:latest

# Verify connection
docker exec -it redis redis-cli ping
```

### Basic Redis CLI Commands

```bash
# Connect to Redis
redis-cli

# Check server info
INFO

# List all keys
KEYS *

# Select database (0-15)
SELECT 0

# Monitor commands in real-time
MONITOR

# Clear database
FLUSHDB

# Exit
EXIT
```

---

## Data Types & Operations

### Strings

```bash
# Set key
SET mykey "Hello"

# Get key
GET mykey

# Set with expiration (30 seconds)
SETEX mykey 30 "Hello"

# Set if not exists
SETNX mykey "Hello"

# Get and set
GETSET mykey "NewValue"

# Increment (atomic operation)
SET counter 10
INCR counter        # 11
INCRBY counter 5    # 16
DECR counter        # 15
DECRBY counter 3    # 12

# Append to string
APPEND mykey " World"  # "Hello World"

# Get string length
STRLEN mykey  # 11

# Delete key
DEL mykey
```

### Hashes (Objects)

```bash
# Set hash field
HSET user:1 name "John" email "john@example.com"

# Get hash field
HGET user:1 name

# Get all hash fields
HGETALL user:1

# Get multiple fields
HMGET user:1 name email

# Check if field exists
HEXISTS user:1 name

# Get all field names
HKEYS user:1

# Get all values
HVALS user:1

# Get number of fields
HLEN user:1

# Delete field
HDEL user:1 email

# Increment hash field
HINCRBY user:1 age 1
```

### Lists

```bash
# Push to head
LPUSH mylist "a" "b" "c"

# Push to tail
RPUSH mylist "d" "e"

# Get range
LRANGE mylist 0 -1

# Pop from head
LPOP mylist

# Pop from tail
RPOP mylist

# Get list length
LLEN mylist

# Get element at index
LINDEX mylist 0

# Set element at index
LSET mylist 0 "x"

# Trim list
LTRIM mylist 0 2
```

### Sets (Unique Collections)

```bash
# Add to set
SADD tags "javascript" "nodejs" "redis"

# Get all members
SMEMBERS tags

# Check membership
SISMEMBER tags "javascript"

# Get set size
SCARD tags

# Remove member
SREM tags "redis"

# Union (combine sets)
SUNION tags tags2

# Intersection (common members)
SINTER tags tags2

# Difference
SDIFF tags tags2
```

### Sorted Sets (Scored Collections)

```bash
# Add with score
ZADD leaderboard 100 "player1" 200 "player2" 150 "player3"

# Get range by score
ZRANGE leaderboard 0 -1 WITHSCORES

# Get range by score value
ZRANGEBYSCORE leaderboard 150 200 WITHSCORES

# Get score of member
ZSCORE leaderboard "player1"

# Get rank (index)
ZRANK leaderboard "player1"

# Get reverse rank
ZREVRANK leaderboard "player1"

# Increment score
ZINCRBY leaderboard 50 "player1"

# Get cardinality (size)
ZCARD leaderboard

# Count members in score range
ZCOUNT leaderboard 100 200
```

---

## Node.js Integration

### Install Redis Client

```bash
npm install redis
```

### Connect to Redis

```javascript
// redis.js
import { createClient } from 'redis';

const client = createClient({
  url: process.env.REDIS_URL || 'redis://localhost:6379',
});

client.on('error', (err) => console.error('Redis error:', err));
client.on('connect', () => console.log('Connected to Redis'));

await client.connect();

export default client;
```

### String Operations

```javascript
import client from './redis.js';

// Set and get
await client.set('mykey', 'Hello');
const value = await client.get('mykey');
console.log(value); // "Hello"

// Set with expiration
await client.setEx('session:123', 3600, JSON.stringify({ userId: 1 }));

// Increment
await client.set('counter', 0);
await client.incr('counter'); // 1
await client.incrBy('counter', 5); // 6

// Append
await client.append('mykey', ' World');

// Delete
await client.del('mykey');
```

### Hash Operations

```javascript
// Set hash
await client.hSet('user:1', {
  name: 'John',
  email: 'john@example.com',
  age: '30',
});

// Get hash field
const name = await client.hGet('user:1', 'name');

// Get all hash fields
const user = await client.hGetAll('user:1');
console.log(user);
// { name: 'John', email: 'john@example.com', age: '30' }

// Increment hash field
await client.hIncrBy('user:1', 'age', 1);

// Delete hash field
await client.hDel('user:1', 'email');
```

### List Operations

```javascript
// Push to list
await client.lPush('mylist', ['c', 'b', 'a']);
await client.rPush('mylist', ['d', 'e']);

// Get range
const list = await client.lRange('mylist', 0, -1);

// Pop
const first = await client.lPop('mylist');
const last = await client.rPop('mylist');

// List length
const length = await client.lLen('mylist');
```

### Set Operations

```javascript
// Add to set
await client.sAdd('tags', ['javascript', 'nodejs', 'redis']);

// Get all members
const members = await client.sMembers('tags');

// Check membership
const isMember = await client.sIsMember('tags', 'javascript');

// Remove
await client.sRem('tags', 'redis');

// Set size
const size = await client.sCard('tags');
```

### Sorted Set Operations

```javascript
// Add to sorted set
await client.zAdd('leaderboard', [
  { score: 100, member: 'player1' },
  { score: 200, member: 'player2' },
  { score: 150, member: 'player3' },
]);

// Get range
const players = await client.zRange('leaderboard', 0, -1, {
  WITHSCORES: true,
});

// Get by score range
const top = await client.zRangeByScore('leaderboard', 150, 200, {
  WITHSCORES: true,
});

// Increment score
await client.zIncrBy('leaderboard', 50, 'player1');

// Get rank
const rank = await client.zRank('leaderboard', 'player1');
```

---

## Caching Strategies

### Cache-Aside Pattern (Lazy Loading)

```javascript
// Check cache first, load from DB if miss
const getUserWithCache = async (userId) => {
  const cacheKey = `user:${userId}`;

  // Try to get from cache
  let user = await client.get(cacheKey);

  if (user) {
    return JSON.parse(user); // Cache hit
  }

  // Cache miss - fetch from database
  user = await getUserFromDatabase(userId);

  // Store in cache for future requests
  await client.setEx(cacheKey, 3600, JSON.stringify(user));

  return user;
};
```

### Write-Through Pattern

```javascript
// Update both cache and database
const updateUserWithCache = async (userId, userData) => {
  // Update database first
  const user = await updateUserInDatabase(userId, userData);

  // Update cache
  const cacheKey = `user:${userId}`;
  await client.setEx(cacheKey, 3600, JSON.stringify(user));

  return user;
};
```

### Write-Behind Pattern

```javascript
// Update cache immediately, write to DB asynchronously
const updateUserFast = async (userId, userData) => {
  const cacheKey = `user:${userId}`;

  // Update cache immediately (fast response)
  await client.setEx(cacheKey, 3600, JSON.stringify(userData));

  // Queue database update for later
  await client.rPush('db-updates-queue', JSON.stringify({
    userId,
    userData,
    timestamp: Date.now(),
  }));

  return userData;
};

// Background worker to process queue
const processDbUpdates = async () => {
  while (true) {
    const item = await client.lPop('db-updates-queue');
    if (!item) continue;

    const { userId, userData } = JSON.parse(item);
    try {
      await updateUserInDatabase(userId, userData);
    } catch (error) {
      console.error('Failed to update user:', error);
      // Re-queue on failure
      await client.rPush('db-updates-queue', item);
    }
  }
};
```

### Distributed Caching

```javascript
// Cache across multiple instances
const cacheSettings = {
  ttl: 3600, // Time to live
  keyPrefix: 'app:', // Namespace keys
};

const getCachedData = async (key) => {
  const prefixedKey = `${cacheSettings.keyPrefix}${key}`;
  return await client.get(prefixedKey);
};

const setCachedData = async (key, value) => {
  const prefixedKey = `${cacheSettings.keyPrefix}${key}`;
  await client.setEx(
    prefixedKey,
    cacheSettings.ttl,
    JSON.stringify(value)
  );
};

// Invalidate cache when data changes
const invalidateCache = async (key) => {
  const prefixedKey = `${cacheSettings.keyPrefix}${key}`;
  await client.del(prefixedKey);
};
```

---

## Session Management

### Store Sessions in Redis

```javascript
import express from 'express';
import RedisStore from 'connect-redis';
import session from 'express-session';
import client from './redis.js';

const app = express();

app.use(
  session({
    store: new RedisStore({ client }),
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: false,
    cookie: {
      secure: true, // HTTPS only
      httpOnly: true,
      maxAge: 1000 * 60 * 60 * 24, // 24 hours
    },
  })
);

app.get('/login', (req, res) => {
  req.session.userId = 123;
  res.send('Session stored in Redis');
});

app.get('/profile', (req, res) => {
  const { userId } = req.session;
  if (!userId) {
    return res.status(401).send('Not authenticated');
  }
  res.send(`User ${userId} profile`);
});
```

### Session-Based Rate Limiting

```javascript
const checkRateLimit = async (userId, limit = 100, window = 3600) => {
  const key = `ratelimit:${userId}`;
  const current = await client.incr(key);

  if (current === 1) {
    // First request, set expiration
    await client.expire(key, window);
  }

  return current <= limit;
};

// Middleware
app.use(async (req, res, next) => {
  const userId = req.session?.userId;
  if (!userId) return next();

  const allowed = await checkRateLimit(userId);
  if (!allowed) {
    return res.status(429).send('Rate limit exceeded');
  }

  next();
});
```

---

## Real-time Features

### Pub/Sub Messaging

```javascript
// Publish message
await client.publish('notifications', JSON.stringify({
  type: 'user_registered',
  userId: 123,
  email: 'user@example.com',
}));

// Subscribe to messages
const subscriber = client.duplicate();
await subscriber.connect();

subscriber.subscribe('notifications', (message) => {
  const data = JSON.parse(message);
  console.log('Received:', data);
});
```

### Real-time Notifications

```javascript
// Server-Sent Events with Redis
app.get('/notifications', async (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');

  const subscriber = client.duplicate();
  await subscriber.connect();

  subscriber.subscribe('notifications', (message) => {
    res.write(`data: ${message}\n\n`);
  });

  req.on('close', () => {
    subscriber.disconnect();
  });
});
```

### Activity Stream

```javascript
// Add activity to stream
const recordActivity = async (userId, action) => {
  const key = `activity:${userId}`;
  await client.rPush(key, JSON.stringify({
    action,
    timestamp: Date.now(),
  }));

  // Keep only last 100 activities
  await client.lTrim(key, 0, 99);

  // Expire after 30 days
  await client.expire(key, 30 * 24 * 3600);
};

// Get recent activities
const getActivities = async (userId, count = 20) => {
  const key = `activity:${userId}`;
  const activities = await client.lRange(key, 0, count - 1);
  return activities.map(JSON.parse);
};
```

---

## Persistence & Durability

### Enable Persistence

```bash
# RDB (Snapshotting) - Periodic snapshots
# Default configuration saves every 900 seconds if 1+ key changes
SAVE 900 1

# AOF (Append-Only File) - Write every command
appendonly yes
appendfsync everysec  # Sync every second
```

### Configure Persistence

```javascript
// Check persistence status
const info = await client.info('persistence');
console.log(info);

// Manual save
await client.bgsave(); // Background save (non-blocking)
```

---

## Performance Optimization

### Pipelining

```javascript
// Send multiple commands at once (faster)
const pipeline = client.multi();
pipeline.set('key1', 'value1');
pipeline.set('key2', 'value2');
pipeline.get('key1');
pipeline.get('key2');

const results = await pipeline.exec();
console.log(results); // All results at once
```

### Key Expiration Strategy

```javascript
// Implement sliding window expiration
const accessWithSlideExpiry = async (key, duration = 3600) => {
  const value = await client.get(key);

  if (value) {
    // Extend expiration on access
    await client.expire(key, duration);
  }

  return value;
};

// Lazy expiration cleanup
const cleanupExpiredKeys = async () => {
  const keys = await client.keys('session:*');

  for (const key of keys) {
    const ttl = await client.ttl(key);
    if (ttl === -1) {
      // Key exists but has no expiration
      await client.del(key);
    }
  }
};
```

### Connection Pooling

```javascript
// Redis client already handles connection pooling
// For Cluster mode (multiple Redis nodes)
import { Cluster } from 'redis';

const cluster = new Cluster({
  rootNodes: [
    { url: 'redis://node1:6379' },
    { url: 'redis://node2:6379' },
    { url: 'redis://node3:6379' },
  ],
});

await cluster.connect();
```

---

## Monitoring & Debugging

### Monitor Commands

```bash
# Real-time command monitoring
MONITOR

# Get command statistics
INFO commandstats

# Slowlog
SLOWLOG GET 10  # Get 10 slowest commands
SLOWLOG RESET   # Clear slowlog
```

### Node.js Monitoring

```javascript
// Monitor Redis operations
client.on('error', (err) => {
  console.error('Redis error:', err);
});

client.on('connect', () => {
  console.log('Connected to Redis');
});

client.on('ready', () => {
  console.log('Redis is ready');
});

// Measure command performance
const measureCommand = async (name, command) => {
  const start = Date.now();
  const result = await command();
  const duration = Date.now() - start;
  console.log(`${name}: ${duration}ms`);
  return result;
};

// Usage
await measureCommand('get', () => client.get('mykey'));
```

---

## Best Practices

### Do's
- ✅ Use appropriate data structures for your use case
- ✅ Implement key expiration to prevent memory bloat
- ✅ Use pipelining for batch operations
- ✅ Monitor memory usage and eviction policies
- ✅ Implement error handling for all operations
- ✅ Use connection pooling for high-concurrency scenarios
- ✅ Namespace keys for clarity (e.g., `user:123:profile`)

### Don'ts
- ❌ Store everything in Redis (it's for hot data only)
- ❌ Use Redis as primary database without backup
- ❌ Ignore memory limits
- ❌ Use KEYS command in production (use SCAN instead)
- ❌ Store plaintext sensitive data
- ❌ Forget to handle connection failures
- ❌ Use synchronous operations with many keys

### Memory Management

```javascript
// Get memory stats
const info = await client.info('memory');

// Configure eviction policy (redis.conf)
// maxmemory-policy allkeys-lru  # Remove least recently used keys

// Monitor key size
const getKeySize = async (key) => {
  const value = await client.get(key);
  return Buffer.byteLength(JSON.stringify(value));
};
```

---

## Related Documentation

- [[PostgreSQL-Supabase.md]] - Database caching with Redis
- [[Next.js/Performance.md]] - Application performance
- [[TypeScript]] - Type-safe Redis operations

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-30 | Initial Redis caching guide |

