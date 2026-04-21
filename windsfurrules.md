# ============================================================================
# IQ Knowledge Base Kenai — Windsurf Rules
# Secure, production-grade development guardrails
# ============================================================================

# ── Project Overview ─────────────────────────────────────────────────────────
# Full-stack RAG knowledge base:
#   frontend/  → Next.js 15 (React 19, TailwindCSS 4, shadcn/ui, TypeScript)
#   backend/   → Azure Functions (Python 3.11) with Qdrant vector search
#   database/  → PocketBase (auth, chat history, metadata)
#   qdrant/    → Vector DB deployed as Azure App Service container
# Deployed on Azure: Function Apps, App Services, Blob Storage

# ── Security — Non-Negotiable ────────────────────────────────────────────────
# 1. NEVER read, log, print, commit, or embed the contents of:
#      .env, .env.local, .env.prod, local.settings.json, or any file in .gitignore
# 2. NEVER hardcode secrets, API keys, connection strings, or tokens in source files.
#    Always use environment variables and reference .env.example for the schema.
# 3. NEVER expose AZURE_OPENAI_API_KEY, QDRANT_API_KEY, CONNECTION_STRING,
#    BLOB_FUNCTION_API_KEY, or any *_KEY / *_SECRET / *_TOKEN variable in:
#      - Client-side code (anything under frontend/src/ that runs in-browser)
#      - Log output (logging.info/debug/warning/error)
#      - Error messages returned in HTTP responses
#      - Commit messages or PR descriptions
# 4. All backend HTTP endpoints MUST validate Authorization headers before
#    processing. Never skip auth checks, even for "internal" routes.
# 5. All user input (question text, blob names, file content) MUST be treated
#    as untrusted. Sanitize before using in queries, file paths, or shell commands.
# 6. NEVER construct file paths from user input without validation against
#    path traversal (../, encoded slashes, null bytes).
# 7. NEVER run shell commands with user-supplied input. If unavoidable, use
#    subprocess with explicit argument lists, never string interpolation.
# 8. When creating .env.example files for new variables, use placeholder values
#    (your_xxx_here), never real credentials.

# ── Code Quality ─────────────────────────────────────────────────────────────
# 9.  Follow existing code style exactly. Do not add, remove, or modify comments
#     or documentation unless explicitly asked.
# 10. Frontend: PascalCase for components, camelCase for hooks/utilities.
#     Prettier: 100-char width, 2-space indent. ESLint: next config.
# 11. Backend: Python 3.11 compatible. Type hints on all new function signatures.
#     Follow existing import ordering (stdlib → third-party → local).
# 12. NEVER introduce new dependencies without explicit user approval.
#     If a dependency is needed, state the package name, version, and reason.
# 13. Keep edits minimal and focused. Prefer single-purpose changes.
#     Do not refactor surrounding code unless the task requires it.
# 14. All new backend functions must include docstrings.
# 15. All new frontend components must be typed with TypeScript interfaces.

# ── Error Handling & Resilience ──────────────────────────────────────────────
# 16. Backend: NEVER return raw Python tracebacks in HTTP responses.
#     Use _problem() helper with sanitized error messages.
# 17. Backend: All external calls (Azure OpenAI, Qdrant, Blob Storage, PocketBase)
#     MUST have explicit timeouts and try/except blocks with logging.
# 18. Frontend: All fetch/API calls MUST use AbortController with timeout.
#     Display user-friendly error messages, never raw stack traces.
# 19. NEVER swallow exceptions silently. At minimum, log them with
#     logging.exception() or console.error().

# ── Testing & Validation ─────────────────────────────────────────────────────
# 20. Run `npm run lint` in frontend/ before considering frontend work complete.
# 21. Run `pytest` in backend/ before considering backend work complete.
# 22. NEVER delete or weaken existing tests without explicit user approval.
# 23. When fixing a bug, add or describe a regression test that covers it.
# 24. For backend changes: verify the function app starts locally with
#     `func start` or confirm deploy succeeds before marking done.

# ── Deployment & Infrastructure ──────────────────────────────────────────────
# 25. Backend deploys via backend/deploy.sh to Azure Function App.
#     Frontend deploys via frontend/deploy.sh to Azure App Service.
#     Database deploys via database/deploy.sh. Qdrant via qdrant/deploy.sh.
# 26. NEVER modify deploy.sh scripts without explicit user approval.
#     These scripts manage production infrastructure.
# 27. NEVER run deploy scripts automatically. Always ask for user approval first.
# 28. When adding new environment variables, update BOTH:
#     - The relevant .env.example file
#     - The deploy.sh script's az functionapp config section (if backend)
# 29. PocketBase migrations in database/pb_migrations/ MUST be committed.
#     Never modify pb_data/ — it is gitignored and environment-specific.

# ── Git & Workflow ───────────────────────────────────────────────────────────
# 30. Conventional commits: type(scope): description
#     Types: feat, fix, docs, refactor, test, chore
#     Scope: component or work-item (e.g., rag-agent, file-manager, W7)
# 31. NEVER force-push to main. NEVER rewrite shared history.
# 32. Significant changes: show commit message and ask for approval first.
#     Minor changes (typos, formatting): commit and notify.
# 33. Update planning/ artifacts when scope, architecture, or milestones change.

# ── Data & Privacy ───────────────────────────────────────────────────────────
# 34. Document content in Blob Storage and Qdrant is confidential business data.
#     NEVER log document content, chunk text, or embedding vectors.
# 35. Chat messages are user-private. NEVER log full chat history or question text
#     beyond the first 80 characters (for debugging).
# 36. PocketBase user records contain PII. NEVER expose user emails, passwords,
#     or auth tokens in logs or responses beyond what's needed for auth validation.

# ── AI Agent Boundaries ──────────────────────────────────────────────────────
# 37. NEVER create random files that clutter the workspace. Only create files
#     that are directly required by the task.
# 38. NEVER modify files outside the current task scope without asking.
# 39. If unsure about intent, ask. Do not guess at business logic.
# 40. When making changes to retrieval logic (rag_agent.py, function_app.py),
#     always verify with local tests that normal knowledge questions still
#     route through Qdrant RAG and listing queries still route through blob search.
