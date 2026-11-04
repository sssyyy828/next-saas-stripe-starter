<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

# Modules

Every module in `config/features.ts` can be deleted for good with `pnpm modules:prune <module...>`. Keep it working whenever you add code:

- Code used by a single module goes in a path listed in that module's `files`: a new page under its route folder, a component in its own folder. Add the path to `files` if it isn't covered yet.
- Code for a module inside a shared file gets a marker, removed with the module:
  - one line: a trailing `// module:<key>` comment;
  - a block: `// module:<key> start` and `// module:<key> end` on their own lines.
  - `module:<key>,<key>` is removed once all the listed modules are pruned.
  - Use `#` in `.env.example`, `{/* */}` in MDX.
- A package used only by modules goes in their `packages`.
- A new module declares its `files`, `packages` and `routes`.

Check with `pnpm modules:prune <module>`: without `--yes` it only prints what it would delete.

# Cloud Agent

Node.js 22 and pnpm 10 match `.github/workflows/prune.yml`. From the repo root:

```bash
pnpm install --frozen-lockfile
test -f .env.local || cp .env.example .env.local
pnpm dev
```

`pnpm dev` listens on port 3000. Docs, blog, changelog, and the marketing pages run with the example env file left empty. Authentication and billing stay off until `DATABASE_URL` (Neon) and the Stripe variables in `.env.example` are set; `/login`, `/dashboard`, `/pricing`, and `/admin` then respond 404. The database client is `@neondatabase/serverless`, which speaks WebSocket to the host's `/v2` proxy, so a generic local Postgres URL is not a substitute for Neon.

`pnpm typecheck` and `pnpm build` succeed without those secrets. `pnpm lint` currently fails on existing code in `components/marketing/panel-demo.tsx` (`react-hooks/set-state-in-effect`).
