# Meal Planner SaaS: Complete Development Curriculum

**Non-AI Meal Planner with Next.js, Supabase & Stripe**

A dependency-ordered curriculum for building a production-ready meal planning application with recipe import, pantry tracking, meal planning, grocery lists, and paid templates/export features.

---

## Module 0: App Scaffold & Foundations

### Concepts You'll Learn
- Next.js App Router mental model and server vs client execution boundaries
- Environment configuration and deployment-ready project structure
- Supabase project setup and integration with Next.js
- Modern React patterns in the App Router paradigm

### Epic: E0 — Foundations
**Project scaffold + deployment-ready baseline**

### User Stories
- As a developer, I have a Next.js app that connects to Supabase and can be deployed to Vercel.
- As a developer, environment variables are properly configured for local development and production.
- As a user, I see a basic navigation shell and landing page.

### Requirements
- Initialize Next.js 14+ with App Router enabled
- Set up Supabase project and obtain connection credentials
- Configure environment variables (`.env.local` for development, Vercel env vars for production)
- Implement basic app layout with navigation placeholder
- Ensure the app builds and deploys successfully to Vercel or similar platform

### Materials
- [Use Supabase with Next.js](https://supabase.com/docs/guides/getting-started/quickstarts/nextjs) — Official quickstart guide
- [Next.js Documentation](https://nextjs.org/docs) — App Router fundamentals
- [Vercel Deployment Guide](https://vercel.com/docs) — Deployment best practices

---

## Module 1: Authentication & Protected Shell

### Concepts You'll Learn
- Session management and cookie-based authentication
- Protected routes and middleware patterns in Next.js App Router
- User authentication flows (sign up, sign in, sign out)
- Client-side and server-side session handling with Supabase Auth

### Epic: E1 — Auth (Supabase)
**Secure authentication with protected app shell**

### User Stories
- As a user, I can sign up with email and password so my data is private and secure.
- As a user, I can sign in to access my meal planning data.
- As a user, unauthenticated requests to the app redirect me to the login page.
- As a user, I can sign out and my session is properly terminated.
- As a user, I stay logged in across browser sessions until I explicitly log out.

### Requirements
- Implement Supabase Auth using the Next.js quickstart patterns for App Router
- Create sign-up, sign-in, and sign-out flows
- Implement middleware or layout-level guards to protect authenticated routes
- Handle session refresh and expiration gracefully
- Store minimal user profile data (email, created_at) in a `profiles` table
- Ensure auth state is accessible in both server and client components

### Materials
- [Use Supabase Auth with Next.js](https://supabase.com/docs/guides/auth/quickstarts/nextjs) — Official auth integration guide
- [Supabase Auth Documentation](https://supabase.com/docs/guides/auth) — Complete auth features reference
- [Next.js Middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware) — Route protection patterns

---

## Module 2: Data Modeling & Row Level Security

### Concepts You'll Learn
- Relational data modeling for recipes, ingredients, meal plans, and pantry
- Database constraints, foreign keys, and data integrity
- Row Level Security (RLS) as defense-in-depth authorization
- PostgreSQL fundamentals: indexes, transactions, and query design
- Migration management and schema evolution

### Epic: E2 — Schema + RLS
**Database schema with user-scoped security policies**

### User Stories
- As a user, only I can read and write my recipes, pantry items, meal plans, and grocery lists (enforced at the database layer).
- As a developer, I have a normalized schema that supports recipes with ingredients, steps, tags, and metadata.
- As a developer, the database prevents unauthorized access even if application code has bugs.

### Requirements
- Design and implement core tables:
  - `profiles` (user metadata)
  - `recipes` (title, servings, prep_time, cook_time, source_url, user_id)
  - `ingredients` (name, category, common_units)
  - `recipe_ingredients` (recipe_id, ingredient_id, quantity, unit, optional)
  - `recipe_steps` (recipe_id, step_number, instruction)
  - `recipe_tags` (recipe_id, tag_type, tag_value) — for equipment, technique, cuisine, etc.
  - `pantry_items` (user_id, ingredient_id, quantity, unit, expiration_date)
  - `meal_plans` (user_id, name, start_date)
  - `meal_plan_items` (meal_plan_id, date, meal_type, recipe_id, servings)
  - `grocery_lists` (user_id, meal_plan_id, created_at)
  - `grocery_list_items` (list_id, ingredient_id, quantity, unit, checked)
- Enable RLS on all tables that store user data
- Write RLS policies: users can only access rows where `user_id = auth.uid()`
- Add appropriate indexes for common queries (user_id, recipe lookups, ingredient searches)
- Use foreign key constraints to maintain referential integrity
- Document migration scripts for schema changes

### Requirements (Security)
- Never expose tables without RLS policies enabled
- Test RLS policies by attempting unauthorized access
- Use `security definer` functions carefully and sparingly

### Materials
- [Supabase Row Level Security](https://supabase.com/docs/guides/database/postgres/row-level-security) — RLS implementation guide
- [Neon PostgreSQL Tutorial](https://neon.com/postgresql/tutorial) — Indexes, constraints, transactions
- [PostgreSQL Documentation](https://www.postgresql.org/docs/) — Official reference for advanced features
- [Database Normalization Guide](https://en.wikipedia.org/wiki/Database_normalization) — Design principles

---

## Module 3: Server Mutations & Request Handling

### Concepts You'll Learn
- Next.js Route Handlers for explicit HTTP endpoints
- Server Actions for form mutations and server-only logic
- Request validation and error handling contracts
- Cache revalidation and optimistic UI patterns
- Server/client boundary security (keeping secrets server-side)

### Epic: E3 — App Backend Surface
**Route Handlers and Server Actions for all mutations**

### User Stories
- As a user, I can submit forms (create recipe, add pantry item, generate list) and receive clear success or error feedback.
- As a developer, I have reusable server-side functions for all data mutations.
- As a developer, API keys and database queries never leak to the client.

### Requirements
- Use Route Handlers (`app/api/*/route.ts`) for:
  - Recipe import endpoint (accepts URL, returns parsed draft)
  - Export endpoints (meal plan CSV/JSON, grocery list CSV/JSON)
  - Any webhook receivers (e.g., Stripe webhooks)
- Use Server Actions for common mutations:
  - Create/update/delete recipes
  - Add/remove pantry items
  - Create/update meal plans
  - Generate grocery lists
  - Toggle grocery list item checked state
- Implement input validation using a library like Zod
- Return structured error responses with actionable messages
- Use `revalidatePath()` or `revalidateTag()` to refresh data after mutations
- Never expose database credentials or API keys to the client

### Requirements (Error Handling)
- Return consistent error shape: `{ success: false, error: string }`
- Log server-side errors but return user-friendly messages to client
- Handle database constraint violations gracefully

### Materials
- [Next.js Route Handlers](https://nextjs.org/docs/14/app/building-your-application/routing/route-handlers) — HTTP endpoint patterns
- [Next.js Server Actions](https://nextjs.org/docs/13/app/api-reference/functions/server-actions) — Form mutations reference
- [Next.js Forms with Server Actions](https://nextjs.org/docs/app/guides/forms) — Complete form handling guide
- [Zod Documentation](https://zod.dev/) — TypeScript-first schema validation

---

## Module 4: Core CRUD — Recipes

### Concepts You'll Learn
- Building structured forms with validation
- Idempotent write operations
- Optimistic UI updates and error recovery
- Complex form state management (ingredients list, steps array)
- Tag-based organization (equipment, techniques, cuisine)

### Epic: E4 — Recipes CRUD (Manual)
**Create, read, update, delete recipes with full metadata**

### User Stories
- As a user, I can create a recipe with title, servings, prep time, cook time, ingredients list, and step-by-step instructions.
- As a user, I can tag a recipe with equipment (e.g., "stand mixer"), techniques (e.g., "sauté"), and cuisine (e.g., "Italian").
- As a user, I can mark ingredients as optional.
- As a user, I can edit any recipe I've created.
- As a user, I can delete recipes I no longer want.
- As a user, I can view a recipe with all details formatted clearly.

### Requirements
- Implement recipe form with dynamic ingredient rows (add/remove)
- Implement recipe form with dynamic step rows (reorderable)
- Allow tagging recipes with multiple equipment and technique tags
- Support fractional quantities (e.g., "1/2 cup", "1.5 tbsp")
- Display recipes in a searchable list view
- Implement edit flow that pre-populates form with existing recipe data
- Implement soft delete or hard delete (your choice, document the decision)
- Validate required fields: title, at least one ingredient, at least one step

### Requirements (Data Integrity)
- Use transactions when creating recipes with ingredients/steps to ensure atomicity
- Prevent orphaned recipe_ingredients or recipe_steps records
- Handle concurrent edits gracefully (last write wins, or implement optimistic locking)

### Materials
- [OSSU Computer Science Curriculum](https://github.com/ossu/computer-science) — Free CS fundamentals (programming, algorithms, databases)
- [React Hook Form](https://react-hook-form.com/) — Form state management library
- [Next.js Data Fetching](https://nextjs.org/docs/app/building-your-application/data-fetching) — Loading and caching patterns

---

## Module 5: Recipe Import from URL

### Concepts You'll Learn
- Safe ingestion pipeline design for user-provided URLs
- Parsing structured metadata (Schema.org Recipe JSON-LD)
- Mapping external schemas to internal data models
- User confirmation flows for imported data
- Error handling for unreliable external content

### Epic: E5 — Import Recipe from URL
**Schema.org-first import with review flow**

### User Stories
- As a user, I can paste a recipe URL and the app attempts to import the recipe details automatically.
- As a user, I see a preview of the imported recipe and can edit it before saving.
- As a user, the app tells me clearly when import fails and suggests manual entry.
- As a user, imported recipes include the source URL for my reference.
- As a user, I can trust that the app won't execute malicious code from URLs I provide.

### Requirements
- Create a Route Handler that accepts a URL and fetches the page server-side
- Parse Schema.org `Recipe` JSON-LD when present in the HTML
- Extract: name, description, recipeYield, prepTime, cookTime, recipeIngredient, recipeInstructions
- Map external data to your schema (handle different instruction formats: text, HowToStep, etc.)
- Return parsed data as a draft recipe object
- Display draft recipe in a review form (pre-populated but editable)
- Store `source_url` in the recipes table
- Handle errors gracefully:
  - Network failures (timeout after 10s)
  - No Schema.org data found → suggest manual entry
  - Invalid JSON-LD → suggest manual entry
- Never execute JavaScript from fetched pages (use a safe HTML parser)

### Requirements (Security)
- Fetch URLs server-side only (never from client)
- Set a reasonable timeout (10 seconds)
- Validate URL format before fetching
- Don't follow excessive redirects (limit to 3-5)
- Sanitize any HTML content before displaying in preview

### Materials
- [Schema.org Recipe](https://schema.org/Recipe) — Official Recipe structured data specification
- [html-recipe-parser](https://github.com/mschunke/html-recipe-parser) — Optional Node.js helper for parsing Recipe JSON-LD
- [Next.js Route Handlers](https://nextjs.org/docs/14/app/building-your-application/routing/route-handlers) — Server-side fetch patterns
- [Cheerio](https://cheerio.js.org/) — Server-side HTML parsing library

---

## Module 6: Pantry & "What Can I Cook?"

### Concepts You'll Learn
- Deterministic scoring and ranking algorithms
- Explainability in recommendation systems (why this recipe?)
- Query optimization for ingredient matching
- Aggregation and set operations in SQL
- Handling optional ingredients and substitutions

### Epic: E6 — Pantry + "What Can I Cook?" Engine
**Pantry tracking and smart recipe recommendations**

### User Stories
- As a user, I can add ingredients to my pantry with optional quantity and expiration date.
- As a user, I can view my pantry and remove items I've used up.
- As a user, I can browse recipes ranked by pantry coverage percentage (how many ingredients I already have).
- As a user, I can see which ingredients are missing for each recipe.
- As a user, recipes that use expiring ingredients are ranked higher.
- As a user, I can filter recommendations by prep time, cook time, or cuisine.
- As a user, I can see an explanation of why each recipe is recommended.

### Requirements
- Implement pantry CRUD: add, update quantity, remove items
- Build a "What Can I Cook?" scoring algorithm:
  - Calculate coverage: `(ingredients I have) / (required ingredients)` × 100
  - Boost score for recipes using ingredients expiring within 3 days
  - Penalize recipes with many missing ingredients
  - Consider optional ingredients separately (don't penalize for missing optional items)
- Display ranked recipe list with:
  - Coverage percentage
  - Missing ingredients count and names
  - "You have N/M ingredients" badge
  - Expiring ingredient alert if applicable
- Support filtering by time (< 30 min, < 1 hour, etc.) and tags
- Optimize queries:
  - Use indexed lookups on user_id and ingredient_id
  - Aggregate ingredient matches efficiently
  - Cache or materialize pantry snapshots if performance becomes an issue

### Requirements (Explainability)
- For each recommended recipe, show:
  - Which ingredients the user has
  - Which ingredients are missing
  - Which optional ingredients could enhance the dish
  - Whether any pantry items are expiring soon

### Materials
- [Neon PostgreSQL Tutorial](https://neon.com/postgresql/tutorial) — Indexes, aggregation, query optimization
- [PostgreSQL Window Functions](https://www.postgresql.org/docs/current/tutorial-window.html) — Advanced ranking queries
- [SQL Performance Explained](https://sql-performance-explained.com/) — Query optimization guide

---

## Module 7: Meal Plan & Grocery List Generator

### Concepts You'll Learn
- Data transformation and aggregation
- Repeatable generation with conflict resolution
- Transaction-based updates for consistency
- Unit normalization and quantity arithmetic
- State management for editable generated content

### Epic: E7 — Meal Plan + Grocery List
**Weekly meal planning and automated grocery list generation**

### User Stories
- As a user, I can create a meal plan for a specific week.
- As a user, I can assign recipes to specific days and meal types (breakfast, lunch, dinner, snack).
- As a user, I can adjust servings for each meal plan item.
- As a user, I can generate a grocery list from my meal plan that:
  - Aggregates all ingredients across all meals
  - Subtracts ingredients I already have in my pantry
  - Groups ingredients by category (produce, dairy, meat, pantry staples, etc.)
- As a user, I can manually add items to my grocery list.
- As a user, I can check items off my grocery list as I shop.
- As a user, regenerating my grocery list doesn't lose my manual edits or checked items.

### Requirements
- Implement meal plan CRUD:
  - Create meal plan with name and start date
  - Add/remove meal plan items (recipe + date + meal type + servings)
  - Edit servings for individual meals
  - Delete entire meal plan
- Implement grocery list generator:
  - Query all recipes in the meal plan
  - Scale ingredient quantities by servings ratio
  - Aggregate identical ingredients across recipes (handle unit conversion when possible)
  - Subtract pantry items (match by ingredient_id, subtract quantities)
  - Categorize ingredients (produce, dairy, meat, grains, spices, etc.)
  - Generate `grocery_lists` record and `grocery_list_items` rows
- Implement grocery list UX:
  - Display grouped by category
  - Allow checking items off (toggle `checked` field)
  - Allow manual addition of non-recipe items
  - Show total item count and checked count
- Handle regeneration:
  - Option 1: Create new list version, preserve old one
  - Option 2: Update only unchecked items, preserve manual edits
  - Document chosen approach clearly

### Requirements (Unit Conversion)
- Implement basic unit conversion for common cases:
  - Volume: tsp ↔ tbsp ↔ cup ↔ oz ↔ ml ↔ L
  - Weight: oz ↔ lb ↔ g ↔ kg
  - Count: individual items don't convert
- For incompatible units (e.g., "1 cup diced" + "200g whole"), list separately and let user decide

### Materials
- [PostgreSQL Aggregation Functions](https://www.postgresql.org/docs/current/functions-aggregate.html) — SUM, GROUP BY, HAVING
- [Recipe Unit Conversion](https://www.thespruceeats.com/recipe-conversions-486768) — Common cooking unit conversions
- [Next.js Data Fetching](https://nextjs.org/docs/app/building-your-application/data-fetching) — Loading related data efficiently

---

## Module 8: Cooking Fundamentals (Education Layer)

### Concepts You'll Learn
- Progressive disclosure UX patterns
- Structured educational content design
- Building user trust with safety references
- Linking to authoritative external sources
- Context-aware help systems

### Epic: E8 — Cooking Lessons + Safety Links
**Technique primers, equipment guides, and food safety references**

### User Stories
- As a user, when a recipe uses a cooking technique (e.g., "sauté", "braise", "julienne"), I can view a short primer on that technique.
- As a user, when a recipe requires specific equipment (e.g., "stand mixer", "Dutch oven"), I can see tips on how to use it or suitable alternatives.
- As a user, when a recipe includes meat, poultry, or seafood, I can view safe minimum internal temperature guidelines from a trusted source.
- As a user, I can access a knife skills guide covering basic cuts and safety practices.
- As a user, educational content is optional and doesn't interrupt my cooking flow.

### Requirements
- Create a `technique_primers` table or JSON file with:
  - technique name (e.g., "sauté")
  - short description (1-2 sentences)
  - detailed explanation (2-3 paragraphs)
  - common mistakes to avoid
  - related techniques
- Create an `equipment_guides` table or JSON file with:
  - equipment name (e.g., "stand mixer")
  - description and common uses
  - alternatives (e.g., "hand mixer or wooden spoon for mixing")
  - care and maintenance tips
- Link recipe tags to primers:
  - When displaying a recipe, show expandable sections for tagged techniques/equipment
  - Use progressive disclosure (collapsed by default, expand on click)
- Add a food safety reference page that links to:
  - [Foodsafety.gov safe minimum internal temperature chart](https://www.foodsafety.gov/print/pdf/node/10?id=foo-bar-baz)
  - Proper handwashing guidelines
  - Cross-contamination prevention
- Add a knife skills guide page with:
  - [Basic knife cuts visual guide](https://www.forksoverknives.com/how-tos/knife-skills-cutting-technique/)
  - Knife safety tips
  - How to hold a knife properly
  - How to use a cutting board safely

### Requirements (User Experience)
- Educational content should enhance, not obstruct, the cooking experience
- Use icons or badges to indicate when primers are available
- Keep primers concise (100-300 words each)
- Link to external authoritative sources rather than inventing safety guidance

### Materials
- [Foodsafety.gov Safe Minimum Internal Temperatures](https://www.foodsafety.gov/print/pdf/node/10?id=foo-bar-baz) — Official USDA guidance
- [Knife Skills Visual Guide](https://www.forksoverknives.com/how-tos/knife-skills-cutting-technique/) — Comprehensive knife techniques
- [HarvardX Science & Cooking](https://www.edx.org/learn/food-science/harvard-university-science-cooking-from-haute-cuisine-to-soft-matter-science-chemistry) — Optional enrichment course (free to audit)
- [The Food Lab](https://www.seriouseats.com/the-food-lab) — Science-based cooking techniques

---

## Module 9: Pro Features — Templates & Export

### Concepts You'll Learn
- Feature gating and entitlement checks
- File generation and download from Route Handlers
- CSV and JSON export formats
- Template design and reuse patterns
- Server-side authorization for paid features

### Epic: E9 — Templates + Export
**Meal plan templates and CSV/JSON export for Pro users**

### User Stories
- As a Pro user, I can save my current meal plan as a reusable template.
- As a Pro user, I can apply a saved template to a new week (copies all meals to the selected date range).
- As a Pro user, I can export my meal plan to CSV or JSON format.
- As a Pro user, I can export my grocery list to CSV or JSON format.
- As a free user, I see upgrade prompts when I try to access templates or export features.
- As a Pro user, if I downgrade, my templates are preserved but I can't apply them until I re-subscribe.

### Requirements (Templates)
- Add `meal_plan_templates` table:
  - template_id, user_id, name, description, created_at
- Add `template_meals` table:
  - template_id, day_offset (0-6 for a week), meal_type, recipe_id, servings
- Implement "Save as Template" action:
  - Copy current meal plan structure to template tables
  - Store day_offset relative to start (Monday = 0, Tuesday = 1, etc.)
- Implement "Apply Template" action:
  - User selects template and target start date
  - Copy template_meals to meal_plan_items with adjusted dates
  - Check Pro entitlement before allowing action
- Display templates in a library with preview

### Requirements (Export)
- Implement meal plan export Route Handler (`/api/export/meal-plan`):
  - Accept format parameter: `csv` or `json`
  - Query meal plan items with recipe details
  - Generate CSV with columns: Date, Meal Type, Recipe Name, Servings, Ingredients
  - Generate JSON with full structured data
  - Return file response with appropriate headers:
    - `Content-Type: text/csv` or `application/json`
    - `Content-Disposition: attachment; filename="meal-plan-YYYY-MM-DD.csv"`
- Implement grocery list export Route Handler (`/api/export/grocery-list`):
  - Accept format parameter: `csv` or `json`
  - Query grocery list items with ingredient details
  - Generate CSV with columns: Category, Ingredient, Quantity, Unit, Checked
  - Generate JSON with full structured data
  - Return file response with download headers
- Check Pro entitlement in export handlers:
  - Query user's subscription status from database
  - Return 403 Forbidden if user is not Pro
  - Log export events for analytics

### Requirements (Security)
- Verify entitlement on the server, not just in UI
- Rate-limit export endpoints (e.g., max 10 exports per hour per user)
- Validate that user owns the meal plan or grocery list being exported
- Don't expose other users' data in exports

### Materials
- [Next.js Route Handlers](https://nextjs.org/docs/14/app/building-your-application/routing/route-handlers) — File download patterns
- [Streaming Files from Route Handlers](https://www.ericburel.tech/blog/nextjs-stream-files) — Practical download headers example
- [CSV Format Specification](https://datatracker.ietf.org/doc/html/rfc4180) — Proper CSV encoding
- [Papa Parse](https://www.papaparse.com/) — CSV parsing/generation library for JavaScript

---

## Module 10: Payments & Subscriptions (Stripe)

### Concepts You'll Learn
- Subscription lifecycle management
- Webhook-driven state synchronization
- Mapping payment provider state to application entitlements
- Handling subscription events (created, updated, canceled, payment failed)
- Downgrade behavior and data preservation

### Epic: E10 — Billing + Entitlements
**Stripe subscriptions with Pro feature gating**

### User Stories
- As a user, I can subscribe to Pro and immediately unlock templates and export features.
- As a user, I can manage my subscription (upgrade, downgrade, cancel) from my account settings.
- As a user, if my payment fails, I receive clear notification and my Pro access is suspended after a grace period.
- As a user, if I cancel my subscription, I retain access until the end of my paid period, then downgrade to free.
- As a user, if I downgrade, my data (including templates) is preserved but Pro features are locked.
- As a user, if I re-subscribe, my templates and exports are immediately available again.

### Requirements (Stripe Setup)
- Create Stripe account and obtain API keys
- Create a Pro subscription product in Stripe with monthly pricing
- Set up Stripe Checkout for subscription start
- Implement webhook endpoint to receive Stripe events
- Store webhook signing secret securely

### Requirements (Database)
- Add `subscription_status` to `profiles` table:
  - Values: `free`, `pro`, `past_due`, `canceled`
- Add `subscriptions` table:
  - user_id, stripe_customer_id, stripe_subscription_id, status, current_period_end, created_at, updated_at
- Index on user_id and stripe_subscription_id for fast lookups

### Requirements (Checkout Flow)
- Create "Upgrade to Pro" button that redirects to Stripe Checkout
- Pass user_id in Checkout session metadata
- Set success_url to redirect back to app after payment
- Set cancel_url to redirect back to pricing page

### Requirements (Webhooks)
- Implement webhook handler at `/api/webhooks/stripe`
- Verify webhook signature using Stripe library
- Handle events:
  - `checkout.session.completed`: Create subscription record, update user to `pro`
  - `customer.subscription.updated`: Update subscription status, period_end
  - `customer.subscription.deleted`: Update user to `free`, preserve data
  - `invoice.payment_failed`: Update status to `past_due`, send notification
  - `invoice.payment_succeeded`: Update status to `pro` if recovering from past_due
- Use idempotency keys to prevent duplicate processing
- Log all webhook events for debugging

### Requirements (Entitlement Checks)
- Create server-side helper function `isProUser(userId)`:
  - Query subscription status
  - Return true if status is `pro`, false otherwise
- Gate Pro features:
  - Template save/apply: check `isProUser()` before action
  - Export endpoints: check `isProUser()` before generating file
  - Template library: show upgrade prompt if not Pro
- Display Pro badge in UI for subscribed users
- Show subscription status and manage link in account settings

### Requirements (Downgrade Behavior)
- When user downgrades or cancels:
  - Do NOT delete templates or meal plans
  - Set templates to "archived" state or simply hide in UI
  - Allow user to view but not apply templates
  - Prevent export downloads
- When user re-subscribes:
  - Restore all Pro features immediately
  - Show previously created templates

### Requirements (Testing)
- Use Stripe test mode for development
- Test webhook locally using Stripe CLI
- Test scenarios:
  - Successful subscription creation
  - Subscription renewal
  - Subscription cancellation
  - Payment failure and retry
  - Immediate downgrade vs end-of-period access

### Materials
- [Stripe Billing Quickstart](https://docs.stripe.com/billing/quickstart) — Get started with subscriptions
- [Stripe Subscriptions Documentation](https://docs.stripe.com/subscriptions) — Complete subscription lifecycle guide
- [Stripe Webhooks Guide](https://docs.stripe.com/webhooks) — Webhook best practices and security
- [Stripe CLI](https://stripe.com/docs/stripe-cli) — Local webhook testing tool

---

## Appendix A: Complete Epic Dependency Order

E0 → E1 → E2 → E3 → E4 → E5 → E6 → E7 → E8 → E9 → E10

**Rationale:**
- E0 (Foundations) must come first: you need a working Next.js + Supabase app before anything else.
- E1 (Auth) comes next: all subsequent features require user identity.
- E2 (Schema + RLS) comes after auth: you need user context to design user-scoped tables and policies.
- E3 (Route Handlers/Actions) comes after schema: you need tables to mutate.
- E4 (Recipes CRUD) is the first domain feature: it uses auth, schema, and mutation patterns.
- E5 (Import) builds on E4: you're importing into the recipes you can now create manually.
- E6 (Pantry + What Can I Cook) requires recipes to exist: you're matching pantry against recipe ingredients.
- E7 (Meal Plan + Grocery List) requires recipes: you're planning meals using the recipes you have.
- E8 (Cooking Education) can be built anytime after E4 (it enhances recipe display), but placing it here means you have full recipe context.
- E9 (Templates + Export) requires E7: you're templating and exporting meal plans.
- E10 (Billing) comes last: you're gating E9's paid features behind subscriptions.

---

## Appendix B: Additional Learning Resources

### General Full-Stack Development
- [OSSU Computer Science Curriculum](https://github.com/ossu/computer-science) — Free, self-paced CS degree covering programming, algorithms, databases, software engineering
- [Full Stack Open](https://fullstackopen.com/en/) — Free course covering React, Node.js, GraphQL, TypeScript, testing

### Next.js & React
- [Next.js Learn Course](https://nextjs.org/learn) — Official interactive tutorial
- [React Documentation](https://react.dev/) — Modern React with hooks and concurrent features
- [Next.js Examples](https://github.com/vercel/next.js/tree/canary/examples) — Official example apps and patterns

### PostgreSQL & Databases
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) — Comprehensive SQL tutorial
- [Use The Index, Luke](https://use-the-index-luke.com/) — Database indexing and performance
- [Designing Data-Intensive Applications](https://dataintensive.net/) — Book on distributed data systems (optional deep dive)

### TypeScript
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) — Official language guide
- [Total TypeScript](https://www.totaltypescript.com/) — Advanced TypeScript patterns and workshops

### Deployment & DevOps
- [Vercel Documentation](https://vercel.com/docs) — Deployment platform for Next.js
- [Docker Getting Started](https://docs.docker.com/get-started/) — Containerization basics
- [GitHub Actions](https://docs.github.com/en/actions) — CI/CD automation

---

## Appendix C: Pro Feature Spec — Export Formats

### Meal Plan CSV Export
**Filename:** `meal-plan-YYYY-MM-DD.csv`

**Columns:**
- Date (YYYY-MM-DD)
- Day (Monday, Tuesday, etc.)
- Meal Type (Breakfast, Lunch, Dinner, Snack)
- Recipe Name
- Servings
- Prep Time (minutes)
- Cook Time (minutes)

**Example:**
```
Date,Day,Meal Type,Recipe Name,Servings,Prep Time,Cook Time
2026-02-03,Tuesday,Dinner,Chicken Stir Fry,4,15,20
2026-02-04,Wednesday,Lunch,Caesar Salad,2,10,0
```

### Meal Plan JSON Export
**Filename:** `meal-plan-YYYY-MM-DD.json`

**Structure:**
```json
{
  "meal_plan": {
    "name": "Week of Feb 3, 2026",
    "start_date": "2026-02-03",
    "end_date": "2026-02-09",
    "meals": [
      {
        "date": "2026-02-03",
        "day": "Tuesday",
        "meal_type": "Dinner",
        "recipe": {
          "name": "Chicken Stir Fry",
          "servings": 4,
          "prep_time": 15,
          "cook_time": 20,
          "ingredients": [...]
        }
      }
    ]
  },
  "exported_at": "2026-02-03T21:46:00Z"
}
```

### Grocery List CSV Export
**Filename:** `grocery-list-YYYY-MM-DD.csv`

**Columns:**
- Category (Produce, Dairy, Meat, Grains, Spices, Other)
- Ingredient
- Quantity
- Unit
- Checked (Yes/No)

**Example:**
```
Category,Ingredient,Quantity,Unit,Checked
Produce,Carrots,2,lbs,No
Dairy,Milk,1,gallon,No
Meat,Chicken breast,3,lbs,Yes
```

### Grocery List JSON Export
**Filename:** `grocery-list-YYYY-MM-DD.json`

**Structure:**
```json
{
  "grocery_list": {
    "meal_plan_name": "Week of Feb 3, 2026",
    "created_at": "2026-02-03T21:45:00Z",
    "items_by_category": {
      "Produce": [
        {"ingredient": "Carrots", "quantity": 2, "unit": "lbs", "checked": false}
      ],
      "Dairy": [...],
      "Meat": [...]
    },
    "total_items": 15,
    "checked_items": 3
  },
  "exported_at": "2026-02-03T21:46:00Z"
}
```

---

**End of Curriculum Document**

*This curriculum is designed to be completed sequentially. Each module builds on the previous ones, ensuring you develop a solid foundation before tackling more complex features.*

*Estimated total time: 60-80 hours for a developer with React/TypeScript experience.*

*For questions or clarifications, refer to the linked materials or consult the official documentation for Next.js, Supabase, and Stripe.*