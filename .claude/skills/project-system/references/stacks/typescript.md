# TypeScript Reference

## Type vs Interface

Use `type` for unions, intersections, mapped types, and utility compositions. Use `interface` for object shapes that may be extended, especially for component props.

```ts
// Interface - extendable object shapes
interface User {
  id: string;
  name: string;
  email: string;
}

interface AdminUser extends User {
  role: "admin";
  permissions: string[];
}

// Type - unions, intersections, computed types
type Status = "active" | "inactive" | "pending";
type Result<T> = { data: T; error: null } | { data: null; error: string };
type UserWithStatus = User & { status: Status };
```

In practice, either works for most cases. Pick one convention per project and stay consistent.

## Generics

```ts
// Generic function
function getFirst<T>(items: T[]): T | undefined {
  return items[0];
}

// Generic with constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Generic type
type ApiResponse<T> = {
  data: T;
  meta: { page: number; total: number };
};

// Generic component (see React section below)
```

## Zod Integration

Zod provides runtime validation and TypeScript type inference from a single source.

```ts
import { z } from "zod";

// Define schema
const ProjectSchema = z.object({
  name: z.string().min(1, "Name is required").max(100),
  description: z.string().optional(),
  status: z.enum(["active", "archived", "draft"]),
  tags: z.array(z.string()).default([]),
  createdAt: z.coerce.date(),
});

// Infer TypeScript type from schema
type Project = z.infer<typeof ProjectSchema>;
// Result: { name: string; description?: string; status: "active" | "archived" | "draft"; tags: string[]; createdAt: Date }

// Validate data
const result = ProjectSchema.safeParse(unknownData);
if (result.success) {
  console.log(result.data); // typed as Project
} else {
  console.log(result.error.flatten()); // structured error messages
}

// Partial / Pick / Omit (mirrors TS utility types)
const UpdateSchema = ProjectSchema.partial().omit({ createdAt: true });
type ProjectUpdate = z.infer<typeof UpdateSchema>;
```

## React Component Typing

```tsx
// Props interface
interface ButtonProps {
  variant?: "primary" | "secondary";
  size?: "sm" | "md" | "lg";
  disabled?: boolean;
  onClick?: () => void;
  children: React.ReactNode;
}

function Button({ variant = "primary", size = "md", children, ...props }: ButtonProps) {
  return <button {...props}>{children}</button>;
}

// Extending native HTML elements
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
}

const Input = React.forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, className, ...props }, ref) => (
    <div>
      <label>{label}</label>
      <input ref={ref} className={className} {...props} />
      {error && <p className="text-sm text-destructive">{error}</p>}
    </div>
  )
);
Input.displayName = "Input";

// Event handlers
function handleChange(e: React.ChangeEvent<HTMLInputElement>) {}
function handleSubmit(e: React.FormEvent<HTMLFormElement>) {}
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {}
function handleKeyDown(e: React.KeyboardEvent<HTMLInputElement>) {}

// Children patterns
interface LayoutProps {
  children: React.ReactNode;          // Most common - accepts anything renderable
}
interface RenderProps {
  render: (data: User) => React.ReactElement; // Render prop pattern
}
```

## API Response Typing

```ts
// Type API responses explicitly
interface ApiError {
  message: string;
  code: string;
  status: number;
}

type ApiResult<T> =
  | { data: T; error: null }
  | { data: null; error: ApiError };

// Server action return types
async function createProject(formData: FormData): Promise<ApiResult<Project>> {
  try {
    const project = await db.insert(projects).values(parsed).returning();
    return { data: project[0], error: null };
  } catch (e) {
    return { data: null, error: { message: "Failed to create project", code: "CREATE_FAILED", status: 500 } };
  }
}

// Route handler typing
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
): Promise<NextResponse<Project | ApiError>> {
  // ...
}
```

## Utility Types

```ts
// Built-in utility types
Partial<User>             // All fields optional
Required<User>            // All fields required
Pick<User, "id" | "name"> // Only specified fields
Omit<User, "password">    // All fields except specified
Record<string, number>    // Object with string keys, number values
Readonly<User>            // All fields readonly
ReturnType<typeof fn>     // Infer return type of a function
Parameters<typeof fn>     // Infer parameter types as tuple
Awaited<Promise<User>>    // Unwrap promise type to User
NonNullable<T | null>     // Remove null and undefined

// Practical examples
type CreateUser = Omit<User, "id" | "createdAt">; // Fields for creation
type UserPreview = Pick<User, "id" | "name" | "avatar">; // Minimal user info
type UserMap = Record<string, User>; // Users indexed by ID
```

## Strict Mode Conventions

Enable strict mode in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

Key implications:
- `noUncheckedIndexedAccess`: Array/object index access returns `T | undefined`, forcing null checks.
- `strictNullChecks`: `null` and `undefined` are distinct types, must be handled explicitly.

## Common Pitfalls

```ts
// 1. Don't use `any` - use `unknown` for truly unknown types
function parse(input: unknown): User {
  // Must narrow before use
  if (typeof input === "object" && input !== null && "name" in input) {
    return input as User; // Safe assertion after narrowing
  }
  throw new Error("Invalid input");
}

// 2. Discriminated unions over optional fields
// Bad - unclear which fields exist together
interface Shape { type: string; radius?: number; width?: number; }

// Good - each variant is explicit
type Shape =
  | { type: "circle"; radius: number }
  | { type: "rectangle"; width: number; height: number };

// 3. Use `as const` for literal inference
const ROLES = ["admin", "editor", "viewer"] as const;
type Role = (typeof ROLES)[number]; // "admin" | "editor" | "viewer"

// 4. Narrow with `in` operator or type guards
function isApiError(value: unknown): value is ApiError {
  return typeof value === "object" && value !== null && "code" in value && "message" in value;
}

// 5. Avoid enums - use union types or const objects
// Instead of: enum Status { Active, Inactive }
const STATUS = { ACTIVE: "active", INACTIVE: "inactive" } as const;
type Status = (typeof STATUS)[keyof typeof STATUS];
```
