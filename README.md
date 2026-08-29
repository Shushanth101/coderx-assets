# 🚀 CoderX Problem Assets & Community Contributions

Welcome to **`coderx-assets`**, the official community contribution repository for **CoderX** ([coderx.dev](https://coderx-two.vercel.app/)).

CoderX is an in-browser interactive coding platform powered by StackBlitz WebContainers. Learners practice real React, Express, and full-stack MERN development right in their browser without installing local dependencies or Docker containers.

> [!NOTE]
> The core CoderX platform repository is private. **This repository (`coderx-assets`) is the open community hub** where developers can create, test, and contribute new problem challenges. When your Pull Request is reviewed and merged here, your challenge will be published live on CoderX!

---

## 📁 Repository Structure

All problem challenges are stored as structured `.json` files inside the `problems/` directory:

```
coderx-assets/
├── README.md                  # Contribution guide & technical reference
├── CHANGELOG.md               # Version history for challenge sets
├── problems/                  # Community problem JSON files
│   ├── react-jsx-basics.json
│   ├── react-state-usestate.json
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
  "title": "Fix useState State Update Bug",
  "description": "## Problem Statement\nIn this challenge, you need to fix...",
  "difficulty": "easy",
  "tags": ["react", "usestate", "state-management"],
  "total_tests": 3,
  "stack_type": "react",
  "files": {
    "src/App.jsx": {
      "contents": "import React, { useState } from 'react';\n\nfunction App() {\n  const [count, setCount] = useState(0);\n  return (\n    <div>\n      <h1 id=\"count\">{count}</h1>\n      <button id=\"inc-btn\" onClick={() => setCount(count + 1)}>Increment</button>\n    </div>\n  );\n}\n\nexport default App;\n",
      "readOnly": false,
      "hidden": false
    },
    "src/main.jsx": {
      "contents": "import React from 'react';\nimport ReactDOM from 'react-dom/client';\nimport App from './App';\n\nReactDOM.createRoot(document.getElementById('root')).render(\n  <React.StrictMode>\n    <App />\n  </React.StrictMode>\n);\n",
      "readOnly": true,
      "hidden": false
    },
    "tests/App.test.jsx": {
      "contents": "import { describe, test, expect } from 'vitest';\nimport { render, fireEvent } from '@testing-library/react';\nimport '@testing-library/jest-dom';\nimport React from 'react';\nimport App from '../src/App';\n\ndescribe('Fix useState State Update Bug', () => {\n  test('Increments count when button is clicked', async () => {\n    const { container: root } = render(<App />);\n    const btn = root.querySelector('#inc-btn');\n    const countEl = root.querySelector('#count');\n    if (!btn) throw new Error('Button #inc-btn not found');\n    fireEvent.click(btn);\n    if (countEl.textContent.trim() !== '1') {\n      throw new Error('Count should be 1 after clicking increment button');\n    }\n  });\n});\n",
      "readOnly": true,
      "hidden": false
    }
  }
}
```

---

## 📦 Available WebContainer Stack Environments & Pre-packaged Archives

CoderX mounts client-side WebContainer virtual filesystems using pre-packaged `.bin` archives stored in the platform's `public/` directory:

| `stack_type` | Binary Archive | Target Ecosystem | Available Packages |
| :--- | :--- | :--- | :--- |
| **`react`** | `react-base-env.bin` | Frontend React SPA | `react`, `react-dom`, `vitest`, `@testing-library/react`, `lucide-react`, `framer-motion`, `tailwindcss` |
| **`express`** | `express-base-env.bin` | Node.js Backend API | `express`, `cors`, `vitest`, `supertest` |
| **`react-express`** | `react-express-base-env.bin` | Full MERN Stack | Combined React frontend + Express backend dependencies |

---

## 🏗️ Detailed Workspace Folder Structures by Stack Type

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
```
files/
├── src/app.js               (Express app definition)       [readOnly: false]
├── src/server.js            (Server listener)              [readOnly: true]
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
import request from 'supertest';
import app from '../app.js';

test('POST /api/seats/1/book succeeds once', async () => {
  const res = await request(app)
    .post('/api/seats/1/book')
    .send({ userId: 'user-1' });

  if (res.status !== 200) {
    throw new Error(`Expected status 200 but got ${res.status}`);
  }
});
```

---

## 🤝 How to Contribute (Step-by-Step)

1. **Fork this repository (`coderx-assets`)** on GitHub.
2. **Create a new problem JSON file** in the `problems/` directory (e.g. `problems/react-custom-hook-toggle.json`).
3. **Validate your JSON file**:
   - Ensure `title`, `description`, `difficulty`, `tags`, `total_tests`, `stack_type`, and `files` are present.
   - Ensure `src/App.jsx` has `export default App;`.
4. **Submit a Pull Request (PR)**:
   - Provide a brief description of the challenge you created and how many test cases it includes.
5. **Review & Merge**:
   - Our team will review your PR, test the problem suite, and merge it.
   - Once merged, your challenge will be published live on CoderX!

---

## 📜 License

By contributing to `coderx-assets`, you agree that your contributed problem sets are licensed under the MIT License and may be hosted publicly on [coderx.dev](https://coderx.dev).
