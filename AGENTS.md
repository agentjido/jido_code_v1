# AGENTS.md

<!-- covers: package.jido_code.version_controlled_quality_surfaces -->

## Mission

Preserve `jido_code_v1` as a historical and unsupported repository.

<!-- covers: package.jido_code.primary_implementation_repo -->

There is no successor project. Do not add features, publish releases, restore
deployments, or present this repository as an active product. Only make
retirement, legal, or critical security changes that have explicit owner
approval.

## First Read

1. Read the retirement notice in `README.md`.
2. Preserve source history and user safety.
3. Use a branch and pull request for every approved change.

## Work Management

<!-- covers: collaboration.workflow.github_prs -->

- Do not open feature or routine maintenance work.
- Do not land work directly on `main`. Use a branch and pull request for each
  approved retirement, legal, or critical security change.

## Engineering Guardrails

### Elixir and Phoenix

- Use `Req` for HTTP calls. Do not introduce `HTTPoison`, `Tesla`, or `:httpc`.
- Do not use `String.to_atom/1` on user input.
- Do not use map-access syntax on structs (`record[:field]`); use struct fields or product APIs.
- Keep one module per file.
- Prefer `Task.async_stream/3` with back-pressure for concurrent enumeration.

### LiveView and HEEx

- Start LiveView templates with `<Layouts.app flash={@flash} ...>`.
- Pass `current_scope` to `<Layouts.app>` where authenticated scope is needed.
- Keep the routed shell, area button menu, shell status strip, and route-owned area state in LiveView.
- Add or change product areas through `JidoCodeWeb.Areas`, router/live-action ownership, and `JidoCodeWeb.AreaPanels` instead of introducing route-local global chrome.
- Never call `<.flash_group>` outside the layouts module.
- Use `<.input>` from core components for forms when available.
- Use `<.icon>` for hero icons.
- Use `JidoCodeWeb.Components.UI` as the app-owned SaladUI boundary; add a wrapper there before depending on a new SaladUI primitive from product LiveViews.
- Use HEEx-compatible interpolation and class list syntax.
- Do not use deprecated `live_redirect`/`live_patch`; use `<.link navigate={...}>`, `<.link patch={...}>`, `push_navigate`, `push_patch`.
- Prefer LiveView streams for collection rendering and updates.

### LiveVue

- Keep the routed page shell in LiveView. Use Vue only for bounded richer regions.
- Mount Vue-backed regions through `<.vue_surface ...>` instead of raw `<.vue ...>` calls.
- Keep server-authored state bounded in `props:` or `streams:` and route Vue emits back into LiveView events.
- Keep generated shadcn-vue primitives in `assets/vue/components/ui/` and import them only from bounded Vue islands.
- Register production-mounted islands explicitly in `assets/vue/index.ts`; do not restore broad Vue auto-registration.
- If a hybrid surface degrades, keep the operator experience in product-oriented server-rendered fallback mode rather than surfacing raw Vite, SSR, or manifest failures.
- When touching `live_vue`, Vite, SSR entrypoints, or shared browser helpers, run `mix frontend.verify`.

### Source Code Graph

- The semantic graph capability is repository-scoped. Treat `.jido_code/source_code_graph/triple_store` as repository-local runtime state, not product truth.
- Use the semantic graph for repository-wide structural questions like module discovery, function discovery, runtime-pattern lookup, bounded impact tracing, or repeated SPARQL-backed semantic questions.
- Prefer ordinary file/code tools when you need exact latest source text, line-level context, or one-off single-file inspection.
- Keep the lifecycle explicit: analyze, load or refresh, then query. Do not assume the `source_code` graph is ambiently fresh.
- When touching repository runtime lifecycle, pod ownership, runtime snapshots, or `AgentWorkspace` runtime routing, run `mix runtime.verify`.
- When touching the semantic graph boundary, actions, pod agents, helper queries, or workspace entrypoints, run `mix source_graph.verify`.
- When touching memory graph boundaries, capture envelopes, memory writers, memory actions, memory workspace entrypoints, workflow provenance capture, or durable-memory adoption, run `mix memory.verify`.
- When touching conversation-derived recall, keep transcript browsing on repo-detail conversation surfaces, use bounded workflow-provenance projections for origin recall, and only classify durable memory through the explicit adoption boundary.
- When touching product-facing semantic services, semantic LiveView or LiveVue surfaces, semantic workflow entrypoints, or governed semantic-finding adoption, run `mix semantic.verify`.
- Keep semantic behavior a bounded enhancement rather than a hidden dependency: operator paths should remain legible when the graph is stale, degraded, or unavailable, and semantic findings must rejoin governed product records before they influence product behavior.

### JS and CSS

- Tailwind v4 import style in `assets/css/app.css` must stay:
  - `@import "tailwindcss" source(none);`
  - `@source "../css";`
  - `@source "../js";`
  - `@source "../../lib/jido_code_web";`
- Do not reintroduce DaisyUI dependencies, DaisyUI component classes, or DaisyUI theme blocks.
- Do not use inline `<script>` tags in HEEx.
- For LiveView hooks, use colocated hooks (`<script :type={Phoenix.LiveView.ColocatedHook}>`) or registered external hooks.

### Testing

- Use `start_supervised!/1` for supervised processes in tests.
- Avoid `Process.sleep/1`; use monitor/assert patterns or `:sys.get_state/1` synchronization.
- For LiveView tests, use `Phoenix.LiveViewTest` helpers (`element/2`, `has_element?/2`, `render_submit/2`, `render_change/2`) and stable DOM IDs.
- Do not assert raw full HTML when selector-based assertions are possible.
- When touching the UI reset boundary, run `mix ui_reset.verify`; when touching LiveVue, Vite, SSR, or generated Vue primitives, run `mix frontend.verify`.

## Dependency Usage Rules

When touching these packages, consult usage rules first:

- `deps/req_llm/usage-rules.md`
- `deps/jido_action/usage-rules.md`
- `deps/jido_ai/usage-rules.md`
- `deps/jido_memory/usage-rules.md`
- `deps/jido/usage-rules.md`
- `deps/phoenix/usage-rules/`
