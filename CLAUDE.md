# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

TypeScript-based e-commerce data utilities providing query functions for a SQLite database. The project uses async/await patterns with strong typing throughout.

## Key Technologies

- **Database**: SQLite with sqlite/sqlite3 packages
- **Language**: TypeScript (ES2022 target, strict mode)
- **Runtime**: Node.js with tsx for TypeScript execution

## Development Commands

```bash
# Install dependencies and initialize
npm run setup

# Run the SDK (TypeScript execution)
npm run sdk

# Build TypeScript (outputs to ./dist)
npx tsc

# Run main application
npx tsx src/main.ts
```

## Database Schema

SQLite database (`ecommerce.db`) with 12 interconnected tables:

- **Customer System**: customers, addresses, customer_segments, customer_activity_log
- **Product System**: products, categories, inventory, warehouses  
- **Order System**: orders, order_items
- **Engagement**: reviews, promotions

See `src/schema.ts` for complete schema definitions with foreign keys and constraints.

## Project Structure

```
src/
├── main.ts          # Database connection and schema initialization
├── schema.ts        # Table creation and relationships
└── queries/         # Query modules (ALL queries must go here)
    ├── customer_queries.ts   # Customer operations, segmentation
    ├── product_queries.ts    # Catalog, search, performance
    ├── order_queries.ts      # Order processing, tracking
    ├── analytics_queries.ts  # CLV, trends, business intelligence
    ├── inventory_queries.ts  # Stock, warehouses, transfers
    ├── promotion_queries.ts  # Campaigns, eligibility
    ├── review_queries.ts     # Feedback, verification
    └── shipping_queries.ts   # Delivery, costs, zones
```

## Query Pattern Standards

All queries follow this consistent pattern:

```typescript
import { Database } from 'sqlite';

export async function queryName(
  db: Database,
  param: type
): Promise<ReturnType> {
  const query = `SQL QUERY WITH ? PLACEHOLDERS`;
  return await db.get(query, [param]);  // or db.all() for multiple rows
}
```

**Requirements:**
- Use parameterized queries (? placeholders) to prevent SQL injection
- Return Promises with proper TypeScript typing
- Use `db.get()` for single records, `db.all()` for multiple
- Define TypeScript interfaces for complex return types
- Place all queries in appropriate files under `src/queries/`

## Complex Query Patterns

The codebase uses advanced SQL features:
- CTEs (WITH clauses) for multi-step calculations
- Window functions (ROW_NUMBER, RANK) for analytics
- Complex JOINs across multiple tables
- Aggregate functions with GROUP BY for summaries
- COALESCE and CASE statements for conditional logic

## Integration Points

**Slack Notifications** (per task.md):
- System designed for cron job execution
- Monitors orders pending >3 days
- Sends alerts to #order-alerts channel
- Includes customer name and phone in notifications

## Critical Guidelines

1. **ALL database queries MUST be written in `./src/queries/` directory** - never inline SQL in other files
2. **Use existing query patterns** - review similar queries before writing new ones
3. **Maintain TypeScript interfaces** - define return types for all complex queries
4. **Follow naming conventions**:
   - Query functions: `getXByY`, `findX`, `calculateX`, `updateX`
   - Use descriptive names matching the business domain
5. **Test with real data** - database includes sample data for all tables