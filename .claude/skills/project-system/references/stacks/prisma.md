# Prisma ORM Reference

## Schema Definition

```prisma
// prisma/schema.prisma
generator client { provider = "prisma-client-js" }
datasource db { provider = "postgresql"; url = env("DATABASE_URL") }

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  role      Role     @default(USER)
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  @@index([email])
}

model Post {
  id        String   @id @default(cuid())
  title     String
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  authorId  String
  tags      Tag[]
  createdAt DateTime @default(now())
  @@index([authorId])
}

model Tag {
  id    String @id @default(cuid())
  name  String @unique
  posts Post[]
}

enum Role { USER; ADMIN }
```

## Migrations

```bash
npx prisma migrate dev --name add_user_role   # Create + apply migration (dev)
npx prisma migrate deploy                      # Apply in production
npx prisma migrate reset                       # Reset database (dev only)
npx prisma generate                            # Regenerate client
```

## Client Singleton

```ts
import { PrismaClient } from '@prisma/client';
const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };
export const prisma = globalForPrisma.prisma ?? new PrismaClient();
if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

## Queries

```ts
// Find many: filter, sort, paginate, include relations
const posts = await prisma.post.findMany({
  where: { published: true, author: { email: { contains: '@example.com' } } },
  include: { author: { select: { name: true } }, tags: true },
  orderBy: { createdAt: 'desc' },
  skip: 0, take: 20,
});

// Find unique (or throw)
const user = await prisma.user.findUnique({ where: { email: 'alice@example.com' } });
const userOrFail = await prisma.user.findUniqueOrThrow({ where: { id: userId } });

// Create with nested relations
const post = await prisma.post.create({
  data: {
    title: 'New Post', content: 'Body',
    author: { connect: { id: userId } },
    tags: { connectOrCreate: [{ where: { name: 'prisma' }, create: { name: 'prisma' } }] },
  },
});

// Update
await prisma.user.update({ where: { id: userId }, data: { name: 'New Name' } });

// Upsert
await prisma.user.upsert({
  where: { email: 'a@b.com' },
  update: { name: 'Updated' },
  create: { email: 'a@b.com', name: 'New' },
});

// Delete
await prisma.post.delete({ where: { id: postId } });
await prisma.post.deleteMany({ where: { published: false, createdAt: { lt: cutoff } } });
```

## Transactions

```ts
// Interactive: dependent operations with rollback
const result = await prisma.$transaction(async (tx) => {
  const sender = await tx.user.update({ where: { id: senderId }, data: { balance: { decrement: amount } } });
  if (sender.balance < 0) throw new Error('Insufficient balance');
  return tx.user.update({ where: { id: recipientId }, data: { balance: { increment: amount } } });
});

// Batch: independent operations, all-or-nothing
const [users, posts] = await prisma.$transaction([
  prisma.user.findMany(),
  prisma.post.findMany({ where: { published: true } }),
]);
```

## Middleware

```ts
// Soft delete example
prisma.$use(async (params, next) => {
  if (params.model === 'Post' && params.action === 'delete') {
    params.action = 'update';
    params.args.data = { deletedAt: new Date() };
  }
  return next(params);
});
```

## Raw Queries

```ts
const users = await prisma.$queryRaw<User[]>`SELECT * FROM "User" WHERE email LIKE ${pattern}`;
await prisma.$executeRaw`UPDATE "User" SET "lastLoginAt" = NOW() WHERE id = ${userId}`;
```

## Seeding

```ts
// prisma/seed.ts
async function main() {
  await prisma.user.upsert({
    where: { email: 'admin@example.com' },
    update: {},
    create: { email: 'admin@example.com', name: 'Admin', role: 'ADMIN' },
  });
}
main().finally(() => prisma.$disconnect());
// package.json: { "prisma": { "seed": "tsx prisma/seed.ts" } }
```

## Key Conventions

- Use `@@index` on foreign keys and frequently filtered columns
- Use `select` over `include` when you only need specific fields
- Use `cuid()` or `uuid()` for IDs; avoid auto-increment in distributed systems
- Singleton client pattern prevents connection leaks in hot-reload dev servers
- For serverless, use Prisma Accelerate or PgBouncer for connection pooling
- Name relation fields clearly: `author`/`authorId` not `user`/`userId`
