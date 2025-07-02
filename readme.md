## 📦 1. Create Workspace

npx create-nx-workspace@latest my-workspace

cd my-workspace

## ⚙️ 2. Generate Node Application

nx g @nx/node:app api


## 🧩 3. Create Shared Libraries

nx g @nx/node:lib config
nx g @nx/node:lib shared/utils
nx g @nx/node:lib shared/errors
nx g @nx/node:lib features/auth
nx g @nx/node:lib features/user


## 🌱 4. Environment Variables
npm install dotenv


PORT=3000
DATABASE_URL=mongodb://localhost:27017/mydb
JWT_SECRET=supersecret

### ✅ Load in `main.ts`

import * as dotenv from 'dotenv';
dotenv.config();


## 🛠 5. Centralized Config

### ✅ `libs/config/src/lib/config.ts`

```ts
export const config = {
  port: process.env.PORT || 3000,
  jwtSecret: process.env.JWT_SECRET || '',
  dbUrl: process.env.DATABASE_URL || '',
};
```

---

## 🧨 6. Custom Error Class

### ✅ `libs/errors/src/lib/CustomError.ts`

```ts
export class CustomError extends Error {
  constructor(public statusCode: number, public message: string) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}
```

---

## 🚨 7. Global Error Middleware

### ✅ `apps/api/src/middleware/error-handler.ts`

```ts
import { Request, Response, NextFunction } from 'express';
import { CustomError } from '@my-workspace/errors';

export const errorHandler = (
  err: Error | CustomError,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const status = err instanceof CustomError ? err.statusCode : 500;
  res.status(status).json({ status: 'error', message: err.message });
};
```

### ✅ Use in `main.ts`

```ts
app.use(errorHandler);
```

---

## 🧪 8. Setup DI (Manual or `tsyringe`)

```bash
npm install tsyringe reflect-metadata
```

### ✅ `main.ts`

```ts
import 'reflect-metadata';
```

### ✅ Sample Service with DI

```ts
// libs/services/src/lib/UserService.ts
import { injectable, inject } from 'tsyringe';

@injectable()
export class UserService {
  constructor(@inject('DbClient') private db: any) {}
}
```

### ✅ Register in Startup

```ts
import { container } from 'tsyringe';
container.register('DbClient', { useValue: dbInstance });
```

---

## 🧾 9. Express App Structure

```
apps/api/src/
├── main.ts
├── app.ts
├── routes/
│   └── user.routes.ts
├── controllers/
│   └── user.controller.ts
├── middleware/
│   └── error-handler.ts
├── services/
│   └── ...
└── interfaces/
```

---

## 🧬 10. Database Setup

### ✅ `libs/db/src/lib/mongoose.ts`

```ts
import mongoose from 'mongoose';
import { config } from '@my-workspace/config';

export const connectToDb = async () => {
  await mongoose.connect(config.dbUrl);
  console.log('DB connected');
};
```

### ✅ Use in `main.ts`

```ts
await connectToDb();
```

---

## 🧹 11. ESLint, Prettier, Husky

```bash
npm i -D eslint prettier husky lint-staged
npx husky install
```

### ✅ `package.json`

```json
"lint-staged": {
  "*.{ts,js,json}": "eslint --fix"
}
```

---

## ✨ 12. Clean Code Practices

* Controller = thin, just handles req/res
* Service = business logic
* Error class = standard errors
* Config = centralized via `libs/config`
* Shared logic = `libs/shared-utils`
* Interfaces = defined in `libs/interfaces`

---

## 🚀 13. Run the App

```bash
nx serve api
```

---

## ✅ Optional Features

### ✅ Swagger

```bash
npm i swagger-ui-express
```

### ✅ Jest Testing

```bash
nx generate @nx/jest:jest-project api
```

---

## ✅ Summary

| Feature                 | Status |
| ----------------------- | ------ |
| Nx Monorepo             | ✅      |
| Modular Folders         | ✅      |
| Env Management          | ✅      |
| Central Config          | ✅      |
| Custom Errors           | ✅      |
| Global Error Middleware | ✅      |
| DI (`tsyringe`)         | ✅      |
| Clean Architecture      | ✅      |

```

---

Would you like me to generate a zip or GitHub repo with this structure pre-created?
```
