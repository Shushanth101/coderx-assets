# 🤝 Contributing Problem Challenges to CoderX

Thank you for helping expand the CoderX community problem library! This document provides complete technical guidelines, JSON schemas, workspace folder structures, and test writing specifications for creating and submitting problem challenges.

---

## 📁 Repository Structure

All problem challenges are stored as structured `.json` files inside the `problems/` directory:

```
coderx-assets/
├── README.md                  # Overview & platform introduction
├── CONTRIBUTING.md            # Technical contribution guide (this document)
├── CHANGELOG.md               # Version history for challenge sets
├── problems/                  # Community problem JSON files
│   ├── react-jsx-basics.json
│   ├── express-routing-basics.json
│   └── cinemax-seat-booking-concurrency-bug.json
└── templates/                 # Starter problem templates
    ├── react-problem-template.json
    ├── express-problem-template.json
    └── react-express-fullstack-template.json
```

---

## 🛠️ Problem JSON Schema Specification

Every problem in CoderX is defined as a single `.json` file containing metadata and a map of workspace files:

```json
{
  "title": "Express REST API Health Check",
  "description": "## Problem Statement\nBuild a GET /api/health endpoint in Express...",
  "difficulty": "easy",
  "tags": ["express", "routing", "rest-api"],
  "total_tests": 1,
  "stack_type": "express",
  "files": {
    "app.js": {
      "contents": "const express = require('express');\nconst app = express();\n\napp.use(express.json());\n\n// Write your route here...\n\nmodule.exports = app;\n",
      "readOnly": false,
      "hidden": false
    },
    "index.js": {
      "contents": "const app = require('./app');\nconst PORT = process.env.PORT || 3000;\n\napp.listen(PORT, () => {\n  console.log(`Server running on port ${PORT}`);\n});\n",
      "readOnly": true,
      "hidden": false
    },
    "tests/server.test.js": {
      "contents": "const request = require('supertest');\nconst { describe, test, expect } = require('vitest');\nconst app = require('../app');\n\ndescribe('GET /api/health', () => {\n  test('returns 200 OK with status ok', async () => {\n    const res = await request(app).get('/api/health');\n    if (res.status !== 200) throw new Error(`Expected status 200 but got ${res.status}`);\n    if (res.body.status !== 'ok') throw new Error(`Expected { status: 'ok' }`);\n  });\n});\n",
      "readOnly": true,
      "hidden": false
    }
  }
}
```

---

## 📦 Available WebContainer Stack Environments & Base Archives

CoderX mounts client-side WebContainer virtual filesystems using pre-packaged `.bin` binary archives.

> [!TIP]
> **Base Environment `.bin` Archives Storage**:
> Pre-packaged WebContainer binary archives (`react-base-env.bin`, `express-base-env.bin`, `react-express-base-env.bin`) are stored under the **[Releases section of the coderx-assets repository](https://github.com/Shushanth101/coderx-assets/releases)**.

| `stack_type` | Binary Archive Asset | Target Ecosystem | Available Pre-installed Packages |
| :--- | :--- | :--- | :--- |
| **`react`** | `react-base-env.bin` | Frontend React SPA | `react`, `react-dom`, `vitest`, `@testing-library/react`, `lucide-react`, `framer-motion`, `tailwindcss` |
| **`express`** | `express-base-env.bin` | Node.js Backend API | `express`, `cors`, `vitest`, `supertest` |
| **`react-express`** | `react-express-base-env.bin` | Full MERN Stack | Combined React frontend + Express backend dependencies |

---

## 🏗️ Workspace Folder Structures by Stack Type

### 1. `react` (Single-repo React)
```
files/
├── src/App.jsx              (Starter code / User editable) [readOnly: false]
├── src/main.jsx             (Vite entry point)             [readOnly: true]
└── tests/App.test.jsx       (Vitest test suite)            [readOnly: true]
```

> **IMPORTANT RULE FOR `src/App.jsx`**:
> Always include React & hook imports and `export default App;`:
> ```jsx
> import React, { useState, useEffect } from 'react';
> 
> function App() {
>   // Component code
> }
> 
> export default App;
> ```

---

### 2. `express` (Single-repo Express)

> [!IMPORTANT]
> **EXPRESS FOLDER STRUCTURE RULE**:
> In Express challenges, **there is NO `src/` directory!** Files are defined directly at the root level:
> - `index.js` (Root server listener entry point)
> - `app.js` (Root Express app instance & router definition)
> - `tests/server.test.js` (Supertest test suite)

```
files/
├── app.js                   (Express app instance & routes) [readOnly: false]
├── index.js                 (Root server listener port)    [readOnly: true]
└── tests/server.test.js     (Supertest test suite)         [readOnly: true]
```

---

### 3. `react-express` (Full Stack MERN Architecture)

For full-stack challenges, the workspace root is divided into **two top-level folders**: `frontend/` and `backend/`.

```
files/
├── frontend/
│   ├── index.html           (Vite HTML entry)              [readOnly: true]
│   ├── src/
│   │   ├── main.jsx         (React mounting script)        [readOnly: true]
│   │   ├── App.jsx          (Main frontend component)      [readOnly: false]
│   │   └── SeatBooking.jsx  (Frontend subcomponents)       [readOnly: false]
│   └── __tests__/
│       └── App.test.jsx     (Frontend Vitest test suite)   [readOnly: true]
│
└── backend/
    ├── app.js               (Express app instance)         [readOnly: false]
    ├── index.js             (Server listener port 4000)    [readOnly: true]
    ├── routes/
    │   └── seats.js         (API endpoint router)          [readOnly: false]
    ├── store/
    │   └── seatStore.js     (In-memory database / state)   [readOnly: false]
    └── __tests__/
        └── concurrency.test.js (Backend Supertest tests)   [readOnly: true]
```

---

## 🧪 Writing Test Suites (Vitest + Supertest)

Vitest test suites run directly inside the browser WebContainer sandbox. When writing tests:

### Frontend React Test Example:
```javascript
import { describe, test } from 'vitest';
import { render, fireEvent } from '@testing-library/react';
import React from 'react';
import App from '../src/App';

test('Renders correct heading', async () => {
  const { container: root } = render(<App />);
  const h1 = root.querySelector('h1');
  if (!h1) throw new Error('Could not find <h1> element.');
  if (!h1.textContent.includes('Welcome')) {
    throw new Error('Heading text must contain "Welcome"');
  }
});
```

### Backend Express Test Example (Supertest):
```javascript
const request = require('supertest');
const { describe, test, expect } = require('vitest');
const app = require('../app');

test('GET /api/health returns 200 OK', async () => {
  const res = await request(app).get('/api/health');
  if (res.status !== 200) {
    throw new Error(`Expected status 200 but got ${res.status}`);
  }
});
```

---

## 🤝 Step-by-Step Contribution Workflow

1. **Fork this repository (`coderx-assets`)** on GitHub.
2. **Create a new problem JSON file** in the `problems/` directory (e.g. `problems/express-routing-health.json`).
3. **Validate your JSON file**:
   - Ensure `title`, `description`, `difficulty`, `tags`, `total_tests`, `stack_type`, and `files` are present.
   - For Express: ensure `app.js` and `index.js` are at the root level without `src/`.
   - For React: ensure `src/App.jsx` has `export default App;`.
4. **Submit a Pull Request (PR)**:
   - Provide a description of the challenge you created and how many test cases it includes.
5. **Review & Live Deployment**:
   - Our team will review your PR, test the problem suite, and merge it.
   - Once merged, your challenge will be published live on CoderX!
