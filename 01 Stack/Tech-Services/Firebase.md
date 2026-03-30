---
title: Firebase
date: 2026-03-30
tags: [backend, database, authentication, services]
category: Tech-Services
author: OpenCode
version: 1.0.0
status: active
---

# Firebase - Complete Guide

Firebase is Google's platform for building web and mobile applications. It provides a suite of services including authentication, real-time databases, cloud functions, hosting, and analytics.

## Table of Contents
1. [Setup & Installation](#setup--installation)
2. [Authentication](#authentication)
3. [Real-time Database (Realtime DB)](#real-time-database)
4. [Cloud Firestore](#cloud-firestore)
5. [Cloud Storage](#cloud-storage)
6. [Cloud Functions](#cloud-functions)
7. [Hosting & Deployment](#hosting--deployment)
8. [Analytics & Monitoring](#analytics--monitoring)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Setup & Installation

### Initialize Firebase Project

**Via Firebase Console:**
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Create a new project
3. Register a web app
4. Copy the configuration credentials

**Via CLI:**
```bash
npm install -g firebase-tools
firebase login
firebase init
```

### Install Firebase SDK

**npm:**
```bash
npm install firebase
```

**yarn:**
```bash
yarn add firebase
```

### Initialize Firebase in Your App

```javascript
// src/firebase.js
import { initializeApp } from 'firebase/app';
import { getAuth } from 'firebase/auth';
import { getFirestore } from 'firebase/firestore';
import { getStorage } from 'firebase/storage';

const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_FIREBASE_APP_ID,
};

const app = initializeApp(firebaseConfig);

export const auth = getAuth(app);
export const db = getFirestore(app);
export const storage = getStorage(app);
```

---

## Authentication

### Sign Up with Email & Password

```javascript
import { createUserWithEmailAndPassword } from 'firebase/auth';
import { auth } from './firebase';

const signUp = async (email, password) => {
  try {
    const userCredential = await createUserWithEmailAndPassword(
      auth,
      email,
      password
    );
    console.log('User created:', userCredential.user);
    return userCredential.user;
  } catch (error) {
    console.error('Sign up error:', error.message);
  }
};
```

### Sign In with Email & Password

```javascript
import { signInWithEmailAndPassword } from 'firebase/auth';

const signIn = async (email, password) => {
  try {
    const userCredential = await signInWithEmailAndPassword(
      auth,
      email,
      password
    );
    return userCredential.user;
  } catch (error) {
    console.error('Sign in error:', error.message);
  }
};
```

### Sign Out

```javascript
import { signOut } from 'firebase/auth';

const logout = async () => {
  try {
    await signOut(auth);
    console.log('User logged out');
  } catch (error) {
    console.error('Sign out error:', error.message);
  }
};
```

### Google Sign-In

```javascript
import { GoogleAuthProvider, signInWithPopup } from 'firebase/auth';

const googleProvider = new GoogleAuthProvider();

const signInWithGoogle = async () => {
  try {
    const result = await signInWithPopup(auth, googleProvider);
    return result.user;
  } catch (error) {
    console.error('Google sign in error:', error.message);
  }
};
```

### GitHub Sign-In

```javascript
import { GithubAuthProvider, signInWithPopup } from 'firebase/auth';

const githubProvider = new GithubAuthProvider();

const signInWithGithub = async () => {
  try {
    const result = await signInWithPopup(auth, githubProvider);
    return result.user;
  } catch (error) {
    console.error('GitHub sign in error:', error.message);
  }
};
```

### Monitor Auth State

```javascript
import { onAuthStateChanged } from 'firebase/auth';
import { useEffect, useState } from 'react';

const useAuthState = () => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (currentUser) => {
      setUser(currentUser);
      setLoading(false);
    });

    return () => unsubscribe();
  }, []);

  return { user, loading };
};
```

---

## Real-time Database

### Read Data

```javascript
import { ref, onValue } from 'firebase/database';
import { getDatabase } from 'firebase/database';

const database = getDatabase();

const readData = (path) => {
  const dbRef = ref(database, path);
  onValue(dbRef, (snapshot) => {
    const data = snapshot.val();
    console.log(data);
  });
};

// Example usage
readData('users/user1');
```

### Write Data

```javascript
import { ref, set } from 'firebase/database';

const writeData = async (path, data) => {
  try {
    await set(ref(database, path), data);
    console.log('Data written successfully');
  } catch (error) {
    console.error('Write error:', error);
  }
};

// Example
writeData('users/user1', {
  name: 'John Doe',
  email: 'john@example.com',
});
```

### Update Data

```javascript
import { ref, update } from 'firebase/database';

const updateData = async (path, updates) => {
  try {
    await update(ref(database, path), updates);
    console.log('Data updated');
  } catch (error) {
    console.error('Update error:', error);
  }
};

updateData('users/user1', { name: 'Jane Doe' });
```

### Delete Data

```javascript
import { ref, remove } from 'firebase/database';

const deleteData = async (path) => {
  try {
    await remove(ref(database, path));
    console.log('Data deleted');
  } catch (error) {
    console.error('Delete error:', error);
  }
};

deleteData('users/user1');
```

---

## Cloud Firestore

### Add Documents

```javascript
import { collection, addDoc } from 'firebase/firestore';

const addUser = async (userData) => {
  try {
    const docRef = await addDoc(
      collection(db, 'users'),
      {
        ...userData,
        createdAt: new Date(),
      }
    );
    console.log('Document written with ID:', docRef.id);
    return docRef.id;
  } catch (error) {
    console.error('Add error:', error);
  }
};
```

### Read Documents

```javascript
import { collection, getDocs } from 'firebase/firestore';

const getAllUsers = async () => {
  try {
    const snapshot = await getDocs(collection(db, 'users'));
    const users = [];
    snapshot.forEach((doc) => {
      users.push({ id: doc.id, ...doc.data() });
    });
    return users;
  } catch (error) {
    console.error('Read error:', error);
  }
};
```

### Real-time Listener

```javascript
import { collection, onSnapshot } from 'firebase/firestore';

const listenToUsers = () => {
  const unsubscribe = onSnapshot(
    collection(db, 'users'),
    (snapshot) => {
      const users = [];
      snapshot.forEach((doc) => {
        users.push({ id: doc.id, ...doc.data() });
      });
      console.log('Users:', users);
    }
  );

  return unsubscribe;
};
```

### Query Data

```javascript
import { collection, query, where, getDocs } from 'firebase/firestore';

const getActiveUsers = async () => {
  try {
    const q = query(
      collection(db, 'users'),
      where('status', '==', 'active')
    );
    const snapshot = await getDocs(q);
    const users = [];
    snapshot.forEach((doc) => {
      users.push({ id: doc.id, ...doc.data() });
    });
    return users;
  } catch (error) {
    console.error('Query error:', error);
  }
};
```

### Update Documents

```javascript
import { doc, updateDoc } from 'firebase/firestore';

const updateUser = async (userId, updates) => {
  try {
    await updateDoc(doc(db, 'users', userId), updates);
    console.log('Document updated');
  } catch (error) {
    console.error('Update error:', error);
  }
};
```

### Delete Documents

```javascript
import { doc, deleteDoc } from 'firebase/firestore';

const deleteUser = async (userId) => {
  try {
    await deleteDoc(doc(db, 'users', userId));
    console.log('Document deleted');
  } catch (error) {
    console.error('Delete error:', error);
  }
};
```

---

## Cloud Storage

### Upload Files

```javascript
import { ref, uploadBytes, uploadBytesResumable } from 'firebase/storage';
import { storage } from './firebase';

// Simple upload
const uploadFile = async (file, path) => {
  try {
    const fileRef = ref(storage, path);
    await uploadBytes(fileRef, file);
    console.log('File uploaded');
  } catch (error) {
    console.error('Upload error:', error);
  }
};

// Upload with progress tracking
const uploadWithProgress = (file, path) => {
  const fileRef = ref(storage, path);
  const uploadTask = uploadBytesResumable(fileRef, file);

  uploadTask.on(
    'state_changed',
    (snapshot) => {
      const progress =
        (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
      console.log('Upload progress:', progress);
    },
    (error) => console.error('Upload error:', error),
    () => console.log('Upload complete')
  );
};
```

### Download Files

```javascript
import { ref, getBytes, getUrl } from 'firebase/storage';

// Get download URL
const getDownloadUrl = async (path) => {
  try {
    const url = await getUrl(ref(storage, path));
    return url;
  } catch (error) {
    console.error('URL error:', error);
  }
};

// Download as bytes
const downloadFile = async (path) => {
  try {
    const bytes = await getBytes(ref(storage, path));
    return bytes;
  } catch (error) {
    console.error('Download error:', error);
  }
};
```

### Delete Files

```javascript
import { ref, deleteObject } from 'firebase/storage';

const deleteFile = async (path) => {
  try {
    await deleteObject(ref(storage, path));
    console.log('File deleted');
  } catch (error) {
    console.error('Delete error:', error);
  }
};
```

---

## Cloud Functions

### Deploy a Function

**functions/src/index.js:**
```javascript
const functions = require('firebase-functions');
const admin = require('firebase-admin');

admin.initializeApp();

exports.sendWelcomeEmail = functions.auth.user().onCreate((user) => {
  console.log('New user:', user.email);
  // Send welcome email logic here
  return null;
});

exports.updateUserProfile = functions.https.onCall((data, context) => {
  if (!context.auth) {
    throw new functions.https.HttpsError(
      'unauthenticated',
      'User must be authenticated'
    );
  }

  const userId = context.auth.uid;
  return admin.firestore()
    .collection('users')
    .doc(userId)
    .update(data)
    .then(() => {
      return { message: 'Profile updated' };
    });
});
```

**Deploy:**
```bash
firebase deploy --only functions
```

### Call Cloud Functions from Client

```javascript
import { httpsCallable } from 'firebase/functions';
import { getFunctions } from 'firebase/functions';

const functions = getFunctions();
const updateProfile = httpsCallable(functions, 'updateUserProfile');

const handleUpdateProfile = async (profileData) => {
  try {
    const result = await updateProfile(profileData);
    console.log('Result:', result.data);
  } catch (error) {
    console.error('Error:', error.message);
  }
};
```

---

## Hosting & Deployment

### Deploy to Firebase Hosting

**Initialize hosting:**
```bash
firebase init hosting
```

**Configure firebase.json:**
```json
{
  "hosting": {
    "public": "dist",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```

**Deploy:**
```bash
npm run build
firebase deploy --only hosting
```

### Environment Secrets

**Set secrets (via CLI):**
```bash
firebase functions:config:set stripe.secret_key="sk_live_..."
firebase functions:config:set sendgrid.api_key="SG...."
```

**Access in functions:**
```javascript
const secretKey = functions.config().stripe.secret_key;
```

---

## Analytics & Monitoring

### Log Events

```javascript
import { logEvent, getAnalytics } from 'firebase/analytics';

const analytics = getAnalytics();

logEvent(analytics, 'purchase', {
  currency: 'USD',
  value: 9.99,
  items: [
    {
      item_id: 'product_123',
      item_name: 'Premium Subscription',
      quantity: 1,
    },
  ],
});
```

### Monitor Performance

```javascript
import { initializePerformanceMonitoring } from 'firebase/performance';

const perf = initializePerformanceMonitoring();

// Automatic monitoring of page loads, navigation timing
const trace = perf.trace('my_custom_trace');
trace.start();
// ... do some work ...
trace.stop();
```

---

## Best Practices

### Security Rules

**Firestore Rules (firestore.rules):**
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }
    match /public/{document=**} {
      allow read: if true;
    }
  }
}
```

**Deploy rules:**
```bash
firebase deploy --only firestore:rules
```

### Performance Optimization

- **Index queries**: Use Firestore indexes for complex queries
- **Batch operations**: Use batched writes for multiple document updates
- **Pagination**: Implement pagination for large result sets
- **Real-time listeners**: Unsubscribe from listeners when components unmount
- **Denormalization**: Strategically denormalize data to reduce reads

```javascript
// Example: Unsubscribe on component unmount
useEffect(() => {
  const unsubscribe = onSnapshot(collection(db, 'users'), (snap) => {
    // Handle data
  });

  return () => unsubscribe(); // Cleanup
}, []);
```

### Cost Optimization

- Avoid reading unnecessary fields (use select specific fields)
- Implement caching with Redux or React Query
- Use Cloud Functions triggers instead of polling
- Archive old data to reduce storage costs
- Set TTL (Time To Live) for temporary data

---

## Troubleshooting

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `Missing or insufficient permissions` | Security rules too restrictive | Review and update Firestore rules |
| `User is not authenticated` | Auth state not ready | Check auth state before operations |
| `Quota exceeded` | Too many requests | Implement rate limiting or upgrade plan |
| `Network error` | Offline or connection issue | Add offline persistence support |
| `Document not found` | Wrong path or deleted | Verify document path exists |

### Enable Offline Persistence

```javascript
import { enableIndexedDbPersistence } from 'firebase/firestore';

enableIndexedDbPersistence(db)
  .catch((err) => {
    if (err.code === 'failed-precondition') {
      console.log('Multiple tabs open');
    } else if (err.code === 'unimplemented') {
      console.log('Browser does not support persistence');
    }
  });
```

### Debug Mode

```javascript
// Enable Firebase debug logging
localStorage.debug = 'firebase:*';
```

---

## Related Documentation

- [[Vercel.md]] - Hosting alternative
- [[MongoDB.md]] - Document database alternative
- [[Next.js/Data-Fetching.md]] - Data fetching patterns
- [[TypeScript]] - Type safety with Firebase types

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-30 | Initial comprehensive Firebase guide |

