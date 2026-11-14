# Highlight 06 — Testing and CI

A verification culture measured in code: 1,110 test files carrying 203,000 lines of test code —
about a quarter of the ~795,000-line TypeScript codebase — spanning unit, database security
rules, browser e2e, and AI evaluation harnesses.

## The numbers

| Layer | Scope |
|---|---|
| Unit tests (`tsx --test`) | 1,110 test files / 203,000 lines across the repo |
| Database rules | Firestore + Storage rules tests under the Firebase emulator |
| Browser e2e | 26 Playwright specs |
| AI evaluations | Three dedicated harnesses (RAG, agents, scoring) |
| Static | `tsc --noEmit` on every change; ESLint over `app`, `components`, `lib` |

## The scripts

Source: `package.json` (abridged)

```json
"typecheck": "tsc --noEmit",
"test:e2e": "playwright test",
"test:e2e:catalog": "playwright test --grep @catalog-phase3",
"test:e2e:present": "playwright test e2e/present.spec.ts",
```

```json
"eval:rag": "tsx lib/rag/__evals__/runner.ts",
"eval:rag:ci": "tsx lib/rag/__evals__/runner.ts --ci",
"eval:agents": "tsx lib/ai/agents/__evals__/runner.ts",
"eval:agents:ci": "RAG_STUB_GEMINI=1 tsx lib/ai/agents/__evals__/runner.ts --ci",
"eval:consequence:live": "tsx scripts/consequence/eval-live.ts",
"eval:scoring": "tsx lib/scoring/__evals__/runner.ts",
"eval:scoring:ci": "tsx lib/scoring/__evals__/runner.ts --ci",
```

```json
"test:unit": "bash -c 'shopt -s globstar nullglob; exec tsx --test lib/**/*.test.ts'",
"test:rules": "firebase emulators:exec --only firestore,storage --project demo-agripay 'tsx --test --test-concurrency=1 firestore.rules.test.ts storage.rules.test.ts'",
```

## What each layer proves

- **Unit suite** — pure domain logic and repository behaviour, including the money invariants:
  the settlement engine's institution-ambiguity refusal, platform-direct routing, and a
  client-fee-stripping test that proves the action boundary drops client-supplied fee fields.
- **Rules tests under the emulator** — Firestore and Storage security rules are executable tests,
  not vibes; the emulator project is the documented Firebase demo convention (`demo-` prefix,
  no real credentials involved).
- **Playwright e2e** — 26 specs drive the real app, including a full cockpit continuity flow with
  extended timeouts for cold compiles.
- **AI eval harnesses** — the RAG layer, the agent runner, and the scoring models each have a
  runner with a `--ci` mode (the agents harness stubs the model provider in CI, so evals are
  deterministic and free). There is also a live consequence-eval script for against-real-model runs.
- **Citation rot-gate** — a checker verifies that citations in knowledge artifacts still resolve to
  the code, and CI re-runs it so documentation cannot silently drift from reality.

## CI

CI runs on every push and pull request to the main branch: it installs a pinned toolchain,
rebuilds the repository's code graph, and executes the RAG layer plus typecheck — with the rot
gate verifying that file:line citations still point where they claim. Test and typecheck
commands are pinned to a single Node version per run, bumped in reviewable, isolated changes.
