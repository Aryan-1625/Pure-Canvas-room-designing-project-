<!-- LOVABLE:BEGIN -->
> [!IMPORTANT]
> This project is connected to [Lovable](https://lovable.dev). Avoid rewriting
> published git history — force pushing, or rebasing/amending/squashing commits
> that are already pushed — as it rewrites history on Lovable's side and the
> user will likely lose their project history.
>
> Commits you push to the connected branch sync back to Lovable and show up in
> the editor, so keep the branch in a working state.

## What Lovable manages

Rules outside this block are the project's own. Change the pieces below in
Lovable, not by hand. With the [Lovable MCP](https://mcp.lovable.dev)
connected, `send_message` asks Lovable to do it; otherwise ask the user to
use the editor.

- **Database.** Do not add or edit `supabase/migrations/` or `drizzle/`.
  Ask Lovable to change the schema.
- **Generated files.** Do not edit a file whose header says it is automatically
  generated.
- **AI gateway.** Keep model calls on Lovable's gateway, and keep
  `LOVABLE_API_KEY` out of browser code.
- **Connectors.** Lovable provides native connectors. `list_connectors` shows
  which are enabled; `add_connector` only returns the dashboard link. When a
  connector is enabled, calls go through the connector gateway and need
  `LOVABLE_API_KEY`. When Lovable does not provide one, users can bring their
  own key and store it in Secrets.
- **Secrets.** Lovable manages them. Do not put secret values in the repo.
<!-- LOVABLE:END -->

## Project rules

- The studio lives in `src/studio/`: one zustand store (`store.ts`) is the single source of truth for plan items; the 2D SVG plan and the R3F scene both render from it, so never keep per-canvas copies of item data.
- Plan coordinates are meters; plan (x, y) maps to scene (x, z) and plan clockwise degrees map to `rotation.y = -deg`; keep this mapping in `Scene3D.tsx` only.
- Pure plan math stays in `geometry.ts` with no React or three.js imports, so it can be unit-tested.
- The `/` route uses `ssr: false` because the editor and WebGL canvas are browser-only.
- Bundled GLB models are CC0 and hosted via `.asset.json` pointers in `src/assets/models/`; log each one in `CREDITS.md` there.
- Furniture height on the scene comes from `supportHeight()` in `geometry.ts` (low shapes act as floors); the cutaway view only changes rendering in `Scene3D.tsx`, never stored item heights, so the plan data stays true to size.
- Wall-mounted pieces (TV, frames) use `Item.elevation` (meters above their support), seeded from `AssetDef.mount`; Scene3D adds it to the support height so plan data stays the single source of truth.
- Furniture models face local +z (plan +y) before rotation; seed layout tests in `__tests__/layout.test.ts` check facing with `rotate(0, 1, deg)`.
