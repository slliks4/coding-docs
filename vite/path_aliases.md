# 📦 Vite + TypeScript + Path Aliases Setup

This guide explains how to properly configure **path aliases** like `@app`, `@auth`, `@components`, etc. in a Vite + React + TypeScript project.

## 🧠 Why Use Path Aliases?

Path aliases:
- 🔍 Improve code readability (`@app/layout` instead of `../../../app/layout`)
- 🛠 Reduce import errors in deep folder structures
- 💡 Make refactoring and navigation easier in large projects

---

## ✅ Step 1: Install Node Types (Required for `path` and `__dirname`)

> Needed to use Node’s built-in `path` module and support Vite config in ES Modules.

```bash
npm install --save-dev @types/node
````

---

## ✅ Step 2: Setup `vite.config.ts`

Vite uses **ESM**, so `__dirname` and `__filename` must be derived like this:

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';
import { fileURLToPath } from 'url';

// Support __dirname in ESM
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@app': path.resolve(__dirname, 'src/app'),
      '@auth': path.resolve(__dirname, 'src/auth'),
      '@landing': path.resolve(__dirname, 'src/landing'),
      '@components': path.resolve(__dirname, 'src/components'),
      '@shared': path.resolve(__dirname, 'src/shared'),
    }
  }
});
```

---

## ✅ Step 3: Configure `tsconfig.json`

TypeScript needs to know about your aliases too:

```jsonc
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@app/*": ["src/app/*"],
      "@auth/*": ["src/auth/*"],
      "@landing/*": ["src/landing/*"],
      "@components/*": ["src/components/*"],
      "@shared/*": ["src/shared/*"]
    }
  },
  "exclude": ["node_modules", "dist"]
}
```

### 🧱 If You Use a `tsconfig.apps.json` (Optional):

Extend from the base config:

```jsonc
// tsconfig.apps.json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "noEmit": true
  },
  "include": ["src"]
}
```

---

## 🔁 Step 4: Restart TypeScript Server

In **VSCode**, press:

```
Ctrl + Shift + P → TypeScript: Restart TS Server
```

Or simply restart your editor to apply the changes.

---

## 🧪 Step 5: Use It in Code

Try importing from your alias:

```ts
import LoginForm from '@auth/components/LoginForm';
import Dashboard from '@app/pages/Dashboard';
```

---

## ❗ Common Errors

| Problem                               | Solution                                             |
| ------------------------------------- | ---------------------------------------------------- |
| `Cannot find module '@app/...`        | TS server not restarted or paths misconfigured       |
| `__dirname is not defined`            | Use `fileURLToPath` method (see vite.config.ts)      |
| Aliases work in TS but not in runtime | You forgot to update `vite.config.ts`                |
| Wrong casing                          | Remember: paths are **case-sensitive** in Unix/Linux |

---

## ✅ Summary

| File                | Purpose                                    |
| ------------------- | ------------------------------------------ |
| `vite.config.ts`    | Runtime alias resolution                   |
| `tsconfig.json`     | Compile-time alias resolution              |
| `@types/node`       | Support `path`, `__dirname` in Vite config |
| `Restart TS server` | Required after config changes              |

---

## 📁 Recommended Folder Structure

```plaintext
src/
├── app/         # main app logic (student/teacher/admin)
├── auth/        # auth pages + logic
├── landing/     # public routes
├── global/      # global components and providers 
├── shared/      # hooks, utils, constants, etc.
```

Use aliases like `@app`, `@auth`, `@shared` for better imports.
