# Calendario de Comida con Misioneros — Barrio Horizon West

A Spanish-only sign-up calendar where ward members invite the two missionary companionships for
a meal. Served as a **BlueStep Git Site** under `/spa/` at `https://comidamisioneros.bluestep.net`.

- `index.html` — the whole app (inline CSS and JS, no build step). Ported from
  `docs/prototype/missionary-meals-calendar.html` without redesign.
- `fonts/` — self-hosted Atkinson Hyperlegible (`/spa/fonts/...`); no third-party requests.
- `docs/prototype/` — the original prototype, for reference only.

## How it works

- **Data:** every read and write goes to the BlueStep endpoint
  `https://configassistedsandbox.bluestep.net/b/invitaciones` (component `CalendarioMisionerosAPI`
  in org U142133). It enforces one invitation per companionship per day, re-checks every input,
  and never sends phone numbers or notes to members. It answers this site's origin only (CORS).
- **Members** need no login. Cancelling asks for the last 4 digits of the phone they gave.
- **Leaders** sign in to BlueStep on the sandbox; the endpoint checks their user id against the
  Líderes list.
- **Missionaries** open "Para los misioneros" (`#/misioneros`) and type a 4-digit PIN to see
  every upcoming meal with phone and notes, read-only. The leader sets, changes or removes the
  PIN on the Líderes page; it is stored on the platform (Configuración) and checked by the
  endpoint (`missionaryList`), never kept in this repo or the page source. The page keeps it in
  `sessionStorage` for the tab only; "Salir" or a refused PIN forgets it. No PIN set means the
  view is closed. A 4-digit PIN can be guessed (no lockout, accepted): change it after transfers.
- **Routes** are hash-only, so Back and reload work: `#/`, `#/c/A`, `#/c/A/2026-12`,
  `#/c/A/2026-12-05`, `#/mis-invitaciones`, `#/misioneros`, `#/lideres`.
- **Demo mode:** add `?demo` to the address to save in the browser only (testing the page without
  the platform; the live endpoint refuses other origins).

## Git Site settings (BlueStep UI, Admin → Sites → New Git Site)

| Field | Value |
|---|---|
| Domain | `comidamisioneros.bluestep.net` |
| Repository URL | `https://github.com/camadorbs/calendario-misioneros` |
| Git Ref | `main` |
| Index Path | `index.html` |
| GitHub token | blank (public repo) |
| Webhook Secret | optional; with it, add a GitHub webhook to `https://comidamisioneros.bluestep.net/spa/webhook` (JSON, push events) |

Saving the site record is the deploy. Without the webhook, re-save the site (or use **Pull**)
after each push. Allow about 5 minutes for caches. Never force-push `main`: its history is the
rollback path (set Git Ref to an older commit and re-save).

If the domain ever changes, the endpoint's `SITE_ORIGIN` must change with it, or every call from
the site will be refused.

## Rules

- No real names, phones or addresses in this repo. Test with placeholders (Jane Doe, 555-01xx).
- Every asset URL stays absolute under `/spa/`; a relative URL renders a blank page at the
  domain root.
