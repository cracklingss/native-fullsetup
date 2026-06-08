# Backend setup

## Initializing node project

Type in terminal:

```bash
npm init -y
```

## Install dependencies and devDependencies

dependencies:

```bash
npm install express cors pg @prisma/client @prisma/adapter-pg
```

devDependencies:

```bash
npm install -D tsx prisma typescript ts-node tsconfig-paths @types/node @types/express @types/pg @types/cors
```

## Setup essential folders

```bash
src/
├── controllers/
├── repositories/
├── lib/
├── routes/
├── services/  
├── app.ts
└── server.ts
```

Create these folders at once using the command below(this will create the src and the folders inside it):

```bash
mkdir src
cd src
mkdir controllers, repositories, routes, services, lib
cd ..
```

Then manually create the files(app.ts and server.ts) inside the src folder.

## Setting up Typescript

1. Create the typescript config file: `npx tsx --init`
2. Update `tsconfig.json` with these settings:

```json
{
  "compilerOptions": {
    "target": "ES2023",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ES2023"],
    "rootDir": "src",
    "outDir": "dist",
    "strict": true,
    "noImplicitAny": true,
    "allowJs": false,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "sourceMap": true,
    "ignoreDeprecations": "6.0",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },
    "types": ["node"],
    "moduleDetection": "force"
  },

  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## Finalize package.json

```json
{
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev"
  }
}
```

## Setup Prisma ORM

We use Prisma with a custom output location to keep our `src` folder as the single source of truth.

1. **Initialize Prisma**:

   ```bash
   npx prisma init --datasource-provider postgresql
   ```

2. **Configure `schema.prisma`**:
   Update the generator to output inside `src`:

   ```prisma
   generator client {
     provider = "prisma-client-js"
     output   = "../src/generated/prisma"
   }
   ```

3. **Database Client (`src/lib/prisma.ts`)**:

   ```typescript
   import "dotenv/config";
   import { PrismaPg } from "@prisma/adapter-pg";
   import { PrismaClient } from "@/generated/prisma/client";

   const connectionString = `${process.env.DATABASE_URL}`;

   const adapter = new PrismaPg({ connectionString });
   const prisma = new PrismaClient({ adapter });

   export { prisma };
   ```
---

## Setup app.ts and server.ts

1. Set Environment variable
```bash
PORT=8000
```

2. server.ts code: 
```typescript
import app from "./app";

const PORT = process.env.PORT || 8000;


app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
```

3. app.ts code:
```typescript
import express, { Application, Request, Response } from "express";
import cors from "cors";

const app: Application = express();

// Middlewares
app.use(cors());
app.use(express.json());


// Test Endpoint
app.get("/api/test", (req: Request, res: Response) => {
    res.status(200).json({
        success: true,
        message: "Express TypeScript backend is working!",
        timestamp: new Date().toISOString()
    });
});

export default app;
```

4. Run and Test
Run:
```bash
npm run dev
```

Test in thunderclient/postman, paste the api and click send:
`http://localhost:8000/api/test`

Output:
```bash
