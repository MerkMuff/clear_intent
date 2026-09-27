# Clear Intent

An AI-assisted goal tracker. Users state a goal, explain what motivates it, and work through it one approved step at a time. Over time, the AI notices patterns across a user's goals and helps them see what is really driving their choices.

Clear Intent is the first application of **Clarity**, a Souper Corporation framework for filtering ego and fear out of decision-making.

> Note on naming: "Clarity" is already used by several unrelated software projects (Microsoft Clarity, the Clarity smart-contract language, the Clarity design system). Code, packages and repos in this project use the name `clear-intent` to avoid confusion.

---

## How it works

### Starting a goal
1. The user signs up and enters a goal.
2. A short chat begins. The AI first asks: **"What is your motivation for this?"**, then asks a few follow-up questions.
3. The AI suggests **3 next steps**, shaped by the stated motivation.
4. The user approves, edits, or rejects each step.
5. The goal is saved as its own item. The user can return to it at any time.

### Working a goal
- The user checks off steps as they complete them.
- **Step rule:** a goal always has at least one open, approved step until it is complete.
  - If the user rejects all 3 suggestions, the AI suggests 3 new ones.
  - If no open approved step remains (all done or all rejected), the AI suggests 3 new ones.
  - Otherwise, the user asks for more suggestions when they want them.
- Each goal has its own ongoing chat. The user can return to it at any time to update progress, change direction, or talk through the goal and its steps.

### Ending a goal
- The user can mark a goal **Complete** or **Archive** it at any time.
- If the conversation suggests a goal has been reached, the AI asks the user whether to mark it complete. It never marks a goal complete on its own.
- Archived goals are kept, not deleted. They remain part of the pattern history.

### Patterns across goals
- The AI looks for patterns across all of a user's goals: recurring motivations, the kinds of steps they accept or reject, how they react to suggestions, and how motivations shift when they change direction.
- The AI brings up a pattern in chat when it is relevant to the goal being discussed.
- Suggestions may also be chosen partly to learn about the user's motivation (what they accept, reject, and how they react).

---

## Core data

| Table | Purpose |
|---|---|
| `goals` | One row per goal. Status: `active`, `complete`, `archived`. |
| `motivations` | Motivation for each goal, **with history**. A new row is added when the motivation changes; old rows are never overwritten. |
| `steps` | Suggested steps and their lifecycle: `suggested` → `approved` / `rejected` → `done`. Keeps the AI's original wording and a flag when the user edited it. |
| `messages` | The chat history for each goal. |
| `observations` | Patterns the AI has noticed about the user, with the user's response to each (see DECISIONS.md, open question 1). |

Every row belongs to one user. Users can only ever read their own data (enforced in the database with Supabase row-level security, not only in app code).

---

## Tech stack

Built with **Lovable**, synced two-way to GitHub.

- **React + Vite + TypeScript**: screens
- **Supabase** (the founder's own project, connected to Lovable): sign-in, Postgres database, row-level security
- **Supabase Edge Functions**: all server-side logic, including every AI call
- **Tailwind CSS**: styling
- **Anthropic Claude API**: all AI features, called **only from Edge Functions**, never from the browser. Lovable's built-in AI is not used, so the AI layer stays portable.
- Responsive web app: works on laptops and phones; no native mobile app

## Project layout (planned)

```
/src                         screens and components
/supabase/migrations         database schema
/supabase/functions          Edge Functions (server-side code)
  /_shared/prompts           every AI prompt, one file per job
    intake.ts                  motivation + follow-up questions
    suggest-steps.ts           generating the 3 steps
    goal-chat.ts               ongoing conversation about a goal
    patterns.ts                finding patterns across goals
    completion-check.ts        asking whether a goal is done
```

The prompts folder is the heart of Clarity. Each prompt lives in its own file (exported as a plain text string), separate from app logic, so it can be read, reviewed and improved without touching anything else.

## Secrets and environment variables

- **Browser-safe values** (`VITE_SUPABASE_URL`, `VITE_SUPABASE_PUBLISHABLE_KEY`) live in `.env`. In a Lovable project `.env` is committed to the repo; it must contain **only** public values.
- **Secrets** live in the Supabase Edge Function secrets (via Lovable's Secrets panel or the Supabase dashboard), never in `.env` and never in code:
  - `ANTHROPIC_API_KEY`
  - `ANTHROPIC_MODEL`: which Claude model to use, so it can change without code edits
- Never prefix a secret with `VITE_`. Anything with that prefix is sent to users' browsers.

## Running locally (for engineers)

1. Clone the repo, then `npm install` and `npm run dev`.
2. Edge Functions run in Supabase; to run them locally, use the Supabase CLI with a local `supabase/functions/.env` (not committed) holding the secrets above.

## Build phases

1. **Foundation:** sign-in, create a goal, intake chat, 3 suggested steps, approve/edit/reject, list of goals
2. **Working a goal:** check off steps, step rule, ongoing per-goal chat
3. **Ending a goal:** complete/archive, AI completion check
4. **Patterns:** observations across goals, surfaced in chat
