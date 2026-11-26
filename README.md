# poc-nestjs-typeorm-migrations

> An enterprise NestJS 10 REST microservice demonstrating TypeORM DataSource configuration, standalone SQL migration versioning, entity lifecycle subscribers, and PostgreSQL persistence.

**Inception (First Commit):** `2023-11-28` • **Batch / Horizon:** `batch-2023` • **Architecture Domain:** `Database Migrations / TypeORM DataSource`

---

## 📌 Architecture & Overview

`nestjs-typeorm` demonstrates production-ready relational database management using NestJS 10 and TypeORM 0.3+. It highlights the separation between dynamic application runtime configuration (`DatabaseModule`) and standalone CLI migration compilation (`db/migrations/`), ensuring migrations execute reliably in container deployment pipelines.

The data model showcases complex enterprise entity associations: abstract inheritance (`AbstractEntity`), one-to-one listings, one-to-many comments, many-to-many tags, and event-driven database lifecycle interception via `ItemSubscriber`.

```
                              +-----------------------------------------------+
                              |                nestjs-typeorm                 |
                              |                 (Port: 3000)                  |
                              |                                               |
[ HTTP Client ]               |  +--------------------+                       |
       |                      |  | ValidationPipe     |                       |
       | HTTP (REST)          |  | (whitelist: true)  |                       |
       v                      |  +---------+----------+                       |
+---------------------------+ |            |                                  |
| ItemsController           +-+->+---------v----------+                       |
| (/items, /items/:id)      | |  | ItemsService       |                       |
+---------------------------+ |  +---------+----------+                       |
                              |            |                                  |
                              |            v                                  |
                              |  +--------------------+                       |
                              |  | TypeORM Repository |                       |
                              |  | (EntityManager)    |                       |
                              |  +----+---------+-----+                       |
                              |       |         |                             |
                              |       |         +------------+                |
                              |       v                      v                |
                              |  +--------------------+ +-------------------+ |
                              |  | ItemSubscriber     | | Domain Entities   | |
                              |  | (afterInsert / upd)| | (Item, Listing,   | |
                              |  +--------------------+ |  Comment, Tag)    | |
                              |                         +---------+---------+ |
                              +-----------------------------------|-----------+
                                                                  v
                                                        +-------------------+
                                                        | PostgreSQL DB     |
                                                        | (Docker Compose)  |
                                                        +-------------------+
```

* **Context:** Database architecture reference implementation evaluating TypeORM 0.3 `DataSource` initialization, schema migration workflows, and lifecycle subscribers within NestJS.
* **Key Patterns:**
  * **Abstract Entity Inheritance:** Reusable base entity (`AbstractEntity`) providing automated primary key generation (`id`) and timestamp auditing (`createdDate`, `updatedDate`).
  * **Discrete CLI Migrations:** Versioned TypeScript migrations (`INIT`, `PublicItems`) executed via TypeORM CLI independently of the web server runtime.
  * **Entity Lifecycle Event Subscribers:** Intercepting entity mutations (`ItemSubscriber`) to execute audit logging and side-effects.
  * **Relational Association Modeling:** Composing 1:1 (`Listing`), 1:N (`Comment`), and M:N (`Tag`) relationships with cascading rules.

---

## 🛠 Tech Stack

* **Framework & Core:** NestJS 10.x (`@nestjs/core`, `@nestjs/common`, `@nestjs/platform-express`)
* **ORM & Database:** TypeORM 0.3.x, `@nestjs/typeorm`, PostgreSQL (`pg`), Docker Compose
* **Configuration & Validation:** `@nestjs/config`, `class-validator`, `class-transformer`
* **Testing & Tooling:** Jest, Supertest, TypeScript 5.x

---

## ✨ Features & Endpoints

* **Item Catalog Management:** Creation, querying, and updating of structured commercial items.
* **Nested Entity Relations:** Managing associated pricing listings, user comments, and descriptive tags.
* **Audit Trail Subscribers:** Automated console logging of entity insertions and updates via `ItemSubscriber`.
* **Automated Migration Engine:** Preconfigured TypeORM CLI scripts for generating and running schema migrations.

### Key API Routes / Interfaces

| Method | Route | Description |
|---|---|---|
| `GET` | `/items` | Retrieve items with populated listings, comments, and tags. |
| `GET` | `/items/:id` | Fetch specific item entity by its unique ID. |
| `POST` | `/items` | Create a new item record with optional nested listing data. |
| `PATCH` | `/items/:id` | Update item properties and tag associations. |
| `DELETE` | `/items/:id` | Remove an item and cascade related entity cleanup. |

---

## 🚀 Getting Started

### Prerequisites

* Node.js `>= 18.0.0`
* Docker & Docker Compose
* npm `>= 8.0.0`

### Installation

```bash
# Clone the repository
git clone https://github.com/fabogit/poc-nestjs-typeorm-migrations.git
cd poc-nestjs-typeorm-migrations

# Install dependencies
npm install
```

### Environment Configuration

Copy the environment template file:

```bash
cp .env.template .env
```

Ensure `.env` matches your local database credentials:

```env
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=items_db
PORT=3000
```

### Starting PostgreSQL via Docker

```bash
docker compose up -d
```

### Running Migrations

```bash
# Run pending TypeORM migrations
npm run migration:run
```

### Running the Application

```bash
# Development mode with hot-reload
npm run start:dev

# Production build and execution
npm run build
npm run start:prod
```

### Running Tests

```bash
npm run test
npm run test:e2e
```

---

## 📄 License & Author

Developed by [@fabogit](https://github.com/fabogit).
