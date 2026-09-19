# CoderX Problem Templates

These are the exact schemas to follow for each `stack_type`. Do not deviate from field names, folder layout, or module conventions.

## Shared top-level schema

```json
{
  "title": "string",
  "description": "## Problem Statement\nMarkdown description of the task...",
  "difficulty": "easy | medium | hard",
  "tags": ["string", "..."],
  "total_tests": 1,
  "stack_type": "react | express | react-express",
  "files": { "...": "see per-stack sections below" }
}
```

- `total_tests` must equal the actual number of `test(...)` cases in the test file(s).
- Every entry in `files` has the shape `{ "contents": "...", "readOnly": bool, "hidden": bool }`.
- `contents` is a single string with `\n` escapes (this is raw file text, not a code block).

---

## 1. React (`stack_type: "react"`)

Base env: `react-base-env.bin.gz` → react, react-dom, react-router, vitest, @testing-library/react.
Module type: **ESM** (`import`/`export`).
Compulsory files, both under `src/`: `src/App.jsx`, `src/main.jsx`.

```json
{
  "title": "Sample React Challenge",
  "description": "## Problem Statement\nDescribe your React challenge here...",
  "difficulty": "easy",
  "tags": ["react", "components"],
  "total_tests": 1,
  "stack_type": "react",
  "files": {
    "src/App.jsx": {
      "contents": "import React from 'react';\n\nfunction App() {\n  return (\n    <div>\n      <h1>Hello CoderX</h1>\n    </div>\n  );\n}\n\nexport default App;\n",
      "readOnly": false,
      "hidden": false
    },
    "src/main.jsx": {
      "contents": "import React from 'react';\nimport ReactDOM from 'react-dom/client';\nimport App from './App';\n\nReactDOM.createRoot(document.getElementById('root')).render(\n  <React.StrictMode>\n    <App />\n  </React.StrictMode>\n);\n",
      "readOnly": true,
      "hidden": false
    },
    "tests/App.test.jsx": {
      "contents": "import { describe, test, expect } from 'vitest';\nimport { render } from '@testing-library/react';\nimport '@testing-library/jest-dom';\nimport React from 'react';\nimport App from '../src/App';\n\ndescribe('Sample React Challenge', () => {\n  test('Renders <h1> with \"Hello CoderX\"', async () => {\n    const { container: root } = render(<App />);\n    const h1 = root.querySelector('h1');\n    if (!h1) throw new Error('No <h1> element found');\n    if (!h1.textContent.includes('Hello CoderX')) {\n      throw new Error('Heading text must contain \"Hello CoderX\"');\n    }\n  });\n});\n",
      "readOnly": true,
      "hidden": false
    }
  }
}
```

**Rule:** the starter `App.jsx` given to the user must render something visible and error-free. Put the bug/incompleteness in logic the tests probe (state, event handlers, conditional rendering, a missing prop, etc.), not in something that crashes on mount.

---

## 2. Express (`stack_type: "express"`)

Base env: `express-base-env.bin.gz` → express, cors, jest, supertest, nodemon, multer, jsonwebtoken.
Module type: **CommonJS** (`require`/`module.exports`) — never `import`/`export`.
Compulsory files, at the **root** (no `src/` folder): `app.js`, `index.js`.

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
      "contents": "const express = require('express');\nconst app = express();\n\napp.use(express.json());\n\n// Write your GET /api/health route here...\n\nmodule.exports = app;\n",
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

**Note:** even though this is Express/CommonJS, the *test file* still imports `describe`/`test`/`expect` from `vitest` (as shown) — the test runner across the platform is Vitest, not Jest, despite Jest being present in the base env packages. Follow the sample's import style for tests unless told otherwise.

If the problem needs data persistence, add a `db.js` with an in-memory object/array (no MongoDB — WebContainers can't run it).

---

## 3. Full-stack / React-Express (`stack_type: "react-express"`)

Base env: `react-express-base-env.bin.gz` (union of both package sets).
Frontend module type: **ESM**. Backend module type: **ESM** in this template (uses `import`/`export` in `backend/app.js` — note this differs from the pure-Express template, which is CommonJS; follow whichever the existing fullstack sample uses).
Folder layout is strict:
- Frontend files under `frontend/src/...` (plus `frontend/index.html`), with frontend tests under `frontend/__tests__/`.
- Backend files under `backend/...` — **no** `src/` subfolder for backend — with backend tests under `backend/__tests__/`.
- Tests are **never shared** between the two sides: each side keeps its own `__tests__/` folder, testing only its own code.

```json
{
  "title": "Full-Stack Seat Booking Reservation Bug",
  "description": "## Problem Statement\nFix the concurrency bug in the Express backend seat reservation endpoint...",
  "difficulty": "hard",
  "tags": ["react", "express", "fullstack", "concurrency", "rest-api"],
  "total_tests": 2,
  "stack_type": "react-express",
  "files": {
    "frontend/index.html": {
      "contents": "<!doctype html>\n<html lang=\"en\">\n  <head>\n    <meta charset=\"UTF-8\" />\n    <title>CineSeat — Book Your Show</title>\n  </head>\n  <body>\n    <div id=\"root\"></div>\n    <script type=\"module\" src=\"/src/main.jsx\"></script>\n  </body>\n</html>\n",
      "readOnly": true,
      "hidden": false
    },
    "frontend/src/main.jsx": {
      "contents": "import { StrictMode } from 'react';\nimport { createRoot } from 'react-dom/client';\nimport App from './App.jsx';\n\ncreateRoot(document.getElementById('root')).render(\n  <StrictMode>\n    <App />\n  </StrictMode>\n);\n",
      "readOnly": true,
      "hidden": false
    },
    "frontend/src/App.jsx": {
      "contents": "import React, { useEffect, useState } from 'react';\n\nfunction App() {\n  const [seats, setSeats] = useState([]);\n\n  useEffect(() => {\n    fetch('/api/seats')\n      .then(res => res.json())\n      .then(data => setSeats(data));\n  }, []);\n\n  return (\n    <div>\n      <h1>🎬 CineSeat Booking</h1>\n      <div className=\"grid\">\n        {seats.map(seat => (\n          <div key={seat.id}>{seat.id} - {seat.status}</div>\n        ))}\n      </div>\n    </div>\n  );\n}\n\nexport default App;\n",
      "readOnly": false,
      "hidden": false
    },
    "backend/app.js": {
      "contents": "import express from 'express';\n\nconst app = express();\napp.use(express.json());\n\nconst seats = [{ id: 1, status: 'available' }];\n\napp.get('/api/seats', (req, res) => {\n  res.json(seats);\n});\n\napp.post('/api/seats/:id/book', (req, res) => {\n  const seat = seats.find(s => s.id === Number(req.params.id));\n  if (!seat) return res.status(404).json({ error: 'Not found' });\n  if (seat.status === 'booked') return res.status(409).json({ error: 'Already booked' });\n  seat.status = 'booked';\n  res.json(seat);\n});\n\nexport default app;\n",
      "readOnly": false,
      "hidden": false
    },
    "backend/index.js": {
      "contents": "import app from './app.js';\nconst PORT = process.env.PORT || 4000;\napp.listen(PORT, () => console.log(`Backend running on port ${PORT}`));\n",
      "readOnly": true,
      "hidden": false
    },
    "backend/__tests__/concurrency.test.js": {
      "contents": "import request from 'supertest';\nimport app from '../app.js';\nimport { describe, test, expect } from 'vitest';\n\ndescribe('Full-Stack Seat Booking Test Suite', () => {\n  test('prevents double booking under concurrent requests', async () => {\n    const [res1, res2] = await Promise.all([\n      request(app).post('/api/seats/1/book').send({ userId: 'user-A' }),\n      request(app).post('/api/seats/1/book').send({ userId: 'user-B' }),\n    ]);\n    const statuses = [res1.status, res2.status].sort();\n    expect(statuses).toEqual([200, 409]);\n  });\n});\n",
      "readOnly": true,
      "hidden": false
    },
    "frontend/__tests__/App.test.jsx": {
      "contents": "import { describe, test, expect } from 'vitest';\nimport { render } from '@testing-library/react';\nimport '@testing-library/jest-dom';\nimport React from 'react';\nimport App from '../src/App';\n\ndescribe('CineSeat Booking UI', () => {\n  test('renders the booking heading', () => {\n    const { container } = render(<App />);\n    const h1 = container.querySelector('h1');\n    if (!h1) throw new Error('No <h1> element found');\n    if (!h1.textContent.includes('CineSeat Booking')) {\n      throw new Error('Heading text must contain \"CineSeat Booking\"');\n    }\n  });\n});\n",
      "readOnly": true,
      "hidden": false
    }
  }
}
```

Note how each side's tests only import from and exercise files on their own side (`frontend/__tests__/` imports from `frontend/src/`, `backend/__tests__/` imports from `backend/`) — never cross-reference the other side's code from a test file.

**Rule:** the starter frontend must render a working preview immediately (seats list, page shell, etc.) — never ship a fullstack starter that throws on first paint. The bug should surface only when the user exercises the flawed logic (e.g. clicking "book" twice, a race condition, a missing validation), not on load.

You may add any number of extra files beyond the compulsory ones in any of the three stacks (e.g. CSS, extra components/routes, a `db.js` fake-data module).