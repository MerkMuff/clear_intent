# Decisions

A running log of why Clear Intent is built the way it is. Add a line whenever something is decided. Newest at the bottom.

| Date | Decision | Why |
|---|---|---|
| 2026-09-26 | Build in Lovable with two-way GitHub sync from day one | Founder is most comfortable in Lovable; the synced repo with full history is the handoff |
| 2026-09-26 | React/Vite + Supabase Edge Functions + Claude API | Lovable's native stack; Edge Functions hold all server-side and AI logic |
| 2026-09-26 | Founder's own Supabase project, not only Lovable Cloud | Data and backend are owned outright; no migration out of Lovable at handoff |
| 2026-09-26 | Call Claude directly, not Lovable's built-in AI | Keeps the AI layer portable when the project leaves Lovable |
| 2026-09-26 | AI calls happen only in Edge Functions; API key in Secrets, not `.env` | In Lovable, `.env` is committed and `VITE_` values reach the browser (lesson from DearAI's CORS problem) |
| 2026-09-26 | Code name `clear-intent`, not `clarity` | "Clarity" is already heavily used by other software |
| 2026-09-26 | AI suggests, user decides | Users approve, edit or reject every step; the AI never acts on a goal without consent |
| 2026-09-26 | Motivation stored as its own field, with history | Pattern-finding compares motivations directly instead of re-reading chats; changes in motivation are meaningful |
| 2026-09-26 | Rejected and edited steps are kept | What users reject or rewrite is signal about their motivation |
| 2026-09-26 | Start with 3 steps; always keep at least 1 open approved step | Enough choice without overwhelming; the goal never stalls with nothing to do |
| 2026-09-26 | Archive, never delete by default | Archived goals are part of the pattern history |
| 2026-09-26 | AI asks before marking a goal complete | Completion is the user's call |
| 2026-09-26 | Responsive web app, no native app | Founder prefers laptop; most users will be on phones; one web app serves both |
| 2026-09-26 | Users can only read their own data, enforced in the database | Motivations and reflections are highly personal |

## Open questions

1. **Can users see what the AI has concluded about them?** The AI builds a picture of the user's motivations and chooses some suggestions partly to learn more. Recommendation: an "What I've noticed" view where the user can confirm, correct or dismiss each observation. This keeps the AI's inferences transparent and correctable, which fits Clarity's aim of removing hidden drivers rather than adding new ones.
2. **What is sent to the AI provider, and what is kept?** Needs a written privacy policy before real users.
3. **Can a user delete everything?** Archiving keeps data; users will still need a way to permanently delete their account and data.
