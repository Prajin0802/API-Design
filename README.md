# API-design

A RESTful API server built with Node.js, Express, TypeScript, and Prisma ORM, designed for managing users, products, product updates, and update points. The backend uses PostgreSQL as its database.

## Features

- User registration and authentication (JWT-based)
- CRUD operations for Products
- CRUD operations for Product Updates
- CRUD operations for Update Points
- Input validation and error handling
- Modular code structure with TypeScript
- Prisma ORM for database access

## Tech Stack

- **Node.js** / **Express**: Server and routing
- **TypeScript**: Type safety
- **Prisma**: ORM for PostgreSQL
- **JWT**: Authentication
- **bcrypt**: Password hashing
- **express-validator**: Input validation
- **dotenv**: Environment variable management
- **morgan**: HTTP request logging
- **CORS**: Cross-origin resource sharing

## Getting Started

### Prerequisites

- Node.js (v16+ recommended)
- PostgreSQL database
- Yarn or npm

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/API-design.git
   cd API-design
   ```

2. **Install dependencies:**
   ```bash
   npm install
   # or
   yarn install
   ```

3. **Set up environment variables:**

   Create a `.env` file in the root directory and add your PostgreSQL connection string:
   ```
   DATABASE_URL=postgresql://user:password@localhost:5432/yourdb
   JWT_SECRET=your_jwt_secret
   ```

4. **Run database migrations:**
   ```bash
   npx prisma migrate deploy
   # or for development
   npx prisma migrate dev
   ```

5. **Start the development server:**
   ```bash
   npm run dev
   # or
   yarn dev
   ```

   The server will run at [http://localhost:3001](http://localhost:3001).

## API Endpoints

All authenticated endpoints are prefixed with `/api` and require a valid JWT token.

### Auth

- `POST /user` — Register a new user
- `POST /signin` — Login and receive a JWT

### Products

- `GET /api/product` — List all products
- `GET /api/product/:id` — Get a single product
- `POST /api/product` — Create a product (`{ name: string }`)
- `PUT /api/product/:id` — Update a product (`{ name: string }`)
- `DELETE /api/product/:id` — Delete a product

### Updates

- `GET /api/update` — List all updates
- `GET /api/update/:id` — Get a single update
- `POST /api/update` — Create an update (`{ title, body, productId }`)
- `PUT /api/update/:id` — Update an update (`{ title?, body?, status?, version? }`)
- `DELETE /api/update/:id` — Delete an update

### Update Points

- `GET /api/updatepoint` — List all update points
- `GET /api/updatepoint/:id` — Get a single update point
- `POST /api/updatepoint` — Create an update point (`{ name, description, updateId }`)
- `PUT /api/updatepoint/:id` — Update an update point (`{ name?, description? }`)
- `DELETE /api/updatepoint/:id` — Delete an update point

> **Note:** Some update point routes are currently placeholders and may need implementation.

## Database Schema

The project uses Prisma ORM. The main models are:

- **User**: Has many Products
- **Product**: Belongs to a User, has many Updates
- **Update**: Belongs to a Product, has many UpdatePoints
- **UpdatePoint**: Belongs to an Update

Example Prisma schema (see `prisma/schema.prisma`):

```prisma
model User {
  id        String    @id @default(uuid())
  createdAt DateTime  @default(now())
  username  String    @unique
  password  String
  products  Product[]
}

model Product {
  id        String   @id @default(uuid())
  createdAt DateTime @default(now())
  name        String   @db.VarChar(255)
  belongsToId String
  belongsTo   User     @relation(fields: [belongsToId], references: [id])
  updates     Update[]
  @@unique([id, belongsToId])
}

enum UPDATE_STATUS {
  IN_PROGRESS
  SHIPPED
  DEPRECATED
}

model Update {
  id        String   @id @default(uuid())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  title   String
  body    String
  status  UPDATE_STATUS @default(IN_PROGRESS)
  version String?
  asset   String?
  productId   String
  product     Product       @relation(fields: [productId], references: [id])
  updatePoints UpdatePoint[]
}

model UpdatePoint {
  id        String   @id @default(uuid())
  createdAt DateTime @default(now())
  updatedAt DateTime
  name        String @db.VarChar(255)
  description String
  updateId String
  update   Update @relation(fields: [updateId], references: [id])
}
```

## Development

- **Run in dev mode:** `npm run dev`
- **Linting/formatting:** Add your preferred tools (e.g., ESLint, Prettier)
- **Testing:** No tests are currently set up; contributions welcome!

## License

ISC (see `package.json`)
