# Expo Skills — Meticulous Research (real, installed-file data)

- **Plugin:** `expo` from marketplace `claude-plugins-official`
- **Version:** `1.12.0` (from `.claude-plugin/plugin.json` and `.codex-plugin/plugin.json`, both agree)
- **Author:** Expo Team (`support@expo.dev`), homepage `https://docs.expo.dev/skills/`, repo `https://github.com/expo/skills/tree/main/plugins/expo`, license MIT
- **Install command used:** `claude plugin install expo@claude-plugins-official`
- **Verification method:** every fact below was read directly from the plugin's cached files on disk at
  `C:\Users\User\.claude\plugins\cache\claude-plugins-official\expo\1.12.0\` (24 `SKILL.md` bodies read in full, plus `plugin.json`, `hooks/hooks.json`, `.mcp.json`, and the top-level `agents/openai.yaml`) — not from expo.dev's web docs, and not paraphrased from memory.

## Summary table

| Skill | Real purpose (from its own SKILL.md) | Trigger phrases (verbatim from frontmatter `description`) | Ships |
|---|---|---|---|
| `expo-overview` | Router/entry point: classifies the goal and routes to the right leaf skill; owns shared setup rules (SDK detection, `npx expo install`, EAS login) | "Expo, EAS, Expo Go", "implement a mobile app", "make my app look native", "add navigation", "fetch some data", "upgrade my SDK", "I'm new to Expo, where do I start"; explicitly **skips** a bare RN project with no `expo` dep | no references/agents beyond `openai.yaml` |
| `expo-project-structure` | New-app folder skeleton (`src/app`, `components/`, `screens/`, `server/`) | "scaffolding or laying out a new Expo project"; explicitly new-projects-only | none |
| `expo-router` | File-based navigation: routes, tabs, stacks, sheets, `Link`, headers | "navigation and routing", "tabs / stacks / modals / sheets, links, headers" | 6 reference files: `route-structure`, `tabs`, `toolbar-and-headers`, `form-sheet`, `search`, `zoom-transitions` |
| `expo-native-ui` | HIG-compliant screens: semantic colors, controls, SF Symbols, media, shadows, layout | "Apple HIG styling, semantic colors, native controls, SF Symbols, media, animations, visual effects, gradients, storage" | 8 references: `animations`, `controls`, `gradients`, `icons`, `media`, `storage`, `visual-effects`, `webgpu-three` |
| `expo-ui` | `@expo/ui` native components (SwiftUI/Compose): sheets, pickers, sliders, menus, grouped forms | "sheets (BottomSheet), pickers, sliders, toggles, menus, and grouped-form sections"; explicit anti-pattern list (Reanimated/@gorhom/RN Picker) | 4 references + `scripts/list-components.js`; `allowed-tools` restricts Bash to that script |
| `expo-animation` | Motion/gesture decision framework (Reanimated 4, Gesture Handler, haptics) | "animating anything in an Expo app, adding gestures, sheets, screen transitions, press feedback or haptics, or fixing motion that stutters" | co-authored with Emil Kowalski; references a `RECIPES.md` (not fetched — see note below) |
| `expo-design-system` | Design tokens (color/spacing/type/radius/shadow/motion) + component contract (variant/size/state) | "theme.ts / theme/", "extending an existing theme", "auditing an app for design-system drift" | 1 reference: `audit.md` |
| `expo-tailwind-setup` | Tailwind v4 + NativeWind v5 + react-native-css setup | "Set up Tailwind CSS v4 in Expo with react-native-css and NativeWind v5" | no references (all inline) |
| `expo-data-fetching` | fetch/React Query/SWR, SecureStore auth, env vars, offline | "ANY network request, API call, or data fetching" (capitalized MUST in body) | 2 references: `expo-router-loaders`, `offline-and-cancellation` |
| `expo-dom` | Run web-only React code (recharts, syntax highlighters) in a webview via `'use dom'` | "run web code in a webview on native and as-is on web" | no references |
| `expo-web-to-native` | End-to-end migration playbook (strangler-fig pattern) for porting a web React app to native | "turn a website into a mobile app", "port a Next.js/Vite/CRA React codebase" | 4 references: `false-friends`, `native-patterns`, `run-as-goal`, `verify-on-device` |
| `expo-module` | Native modules/views via Expo Modules API (Swift/Kotlin/TS DSL) | "building or modifying native modules for Expo"; explicitly hands off to a *different plugin* (`expo-migrate-module` in `expo-experiments`) for DSL→macro migration | 6 references incl. `create-expo-module`, `native-module`, `native-view`, `lifecycle`, `config-plugin`, `module-config` |
| `expo-brownfield` | Embed RN/Expo into an existing native iOS/Android app (isolated vs integrated) | "brownfield, embedding React Native in a native app, AAR/XCFramework" | 4 references incl. `comparison.md` decision matrix |
| `expo-app-clip` | iOS App Clip target scaffold + AASA hosting + TestFlight | "App Clip, AASA, apple-app-site-association, appclips, smart app banner" | 1 reference: `native-module.md` (App Clip detection module) |
| `expo-examples` | Mine `expo/examples` (~70 `with-*` integration repos) for canonical, version-matched patterns | "integrating a third-party library or service", "scaffolding a new project from one with `npx create-expo --example`" | 1 reference: `catalog.md`; `allowed-tools` scoped to `gh api`, `git clone`, `npx create-expo`, `npx degit`, `bun create` |
| `expo-upgrade` | SDK upgrade steps, breaking-change checklist, deprecated-package table | "upgrading Expo SDK versions and fixing dependency issues" | 7 references: `react-19`, `new-architecture`, `react-compiler`, `native-tabs`, `expo-av-to-audio`, `expo-av-to-video`, `react-navigation-to-expo-router` |
| `expo-dev-client` | Custom dev-client builds (local or TestFlight) for testing native code | "Build and distribute Expo development clients locally or via TestFlight" | no references |
| `expo-skill-feedback` | Submit skill/product feedback + eval candidates + control anonymous telemetry (off by default) | "a skill was useful, confusing, broken", "enable or disable telemetry" | `scripts/`: `skill-event.cjs`, `telemetry.cjs`, `telemetry_common.cjs` — **this is the skill the plugin's own hooks call on every Skill invocation** |
| **`eas-app-stores`** | Build/submit to App Store, Play Store, TestFlight via EAS CLI | "deploy, release, or ship an app to production or the app stores" | 5 references: `workflows`, `testflight`, `app-store-metadata`, `play-store`, `ios-app-store` |
| **`eas-hosting`** | Deploy web bundle + Expo Router API routes (`+api.ts`) to EAS Hosting (Cloudflare Workers) | "deploying an Expo web app or API routes, setting up EAS Hosting" | no references (431-line SKILL.md is self-contained) |
| **`eas-observe`** | `expo-observe` SDK integration + `eas observe:*` CLI queries for cold/warm launch, TTI | "AppMetricsRoot/ObserveRoot HOC", "eas observe:metrics-summary" | 4 references: `metrics`, `queries`, `setup`, `third-party` |
| **`eas-simulator`** | Run/drive the app on a remote iOS/Android sim on EAS cloud infra via `agent-device`/`argent` | "run my app on a cloud simulator", "I'm on Linux/Cursor and need an iOS device" | 3 references: `controllers`, `run-your-app`, `troubleshooting`; `allowed-tools` scoped to specific Bash prefixes |
| **`eas-update-insights`** | `eas update:insights` / `channel:insights` CLI queries for OTA crash rate, adoption | "how an update is performing, whether a rollout is healthy" | 2 references: `channel-insights-schema`, `update-insights-schema` |
| **`eas-workflows`** | Author/validate `.eas/workflows/*.yml` CI/CD against the live JSON Schema | "CI/CD or workflows in an Expo or EAS context", ".eas/workflows/" | `scripts/fetch.js` + `package.json` — fetches the live schema, doesn't hardcode it |

## Expo framework (OSS) skills — concrete mechanics

**expo-overview** (the router — load first, gates everything else)
- Hard gate: "a bare React Native project with no `expo` dependency is not Expo work" — stated twice, verbatim, as a stop condition.
- Shared rules it owns for *every* other skill: install packages with `npx expo install <pkg>` (never raw npm/yarn/pnpm), detect SDK version from `package.json`/`app.json` before giving advice, use version-pinned docs URLs (`docs.expo.dev/versions/v56.0.0/...`, not `/latest/`), and treat "moving to a newer SDK" as its own task (→ `expo-upgrade`).
- Component-selection rule baked into the router itself: check `expo-ui` before reaching for any RN built-in or community UI library.
- Ships a feedback command every skill repeats verbatim: `npx --yes submit-expo-feedback@latest --category skills --subject "<skill-name>" "<feedback>"`.

**expo-project-structure**
- Concrete tree: `src/app` (routes only, nothing else), `src/components`, `src/screens`, `src/server`, `src/utils`, `src/hooks`. Explicitly new-projects-only — "never restructure an existing app to match."
- Platform split files use `.ios`/`.android`/`.native`/`.web` suffixes; a default (no-suffix) file is *always* required even if it's a no-op.
- Styles live at the bottom of the component file, not in separate `.styles` files; tests are colocated (`format-date.test.ts` beside `format-date.ts`).

**expo-router**
- Hard rule: never import from `@react-navigation/*` directly on SDK 56+ — use `expo-router/react-navigation` instead.
- `Color` from `expo-router` (not raw `PlatformColor`) for semantic colors.
- Concrete API surface demoed: `<Link.Trigger>`/`<Link.Menu>`/`<Link.MenuAction>` for context menus, `<Link.Preview>` for peek, `presentation: "formSheet"` with `sheetAllowedDetents`, `NativeTabs` from `expo-router/unstable-native-tabs`.
- Anti-pattern called out explicitly: co-locating components/types/utilities inside `app/` is "an anti-pattern."

**expo-native-ui**
- "CRITICAL: Always try Expo Go first" before `expo run:ios`/`run:android` — custom builds are gated to 4 specific cases (local native modules, Apple targets, 3rd-party native modules, custom native config).
- A long library-preference table with hard swaps: `expo-audio`/`expo-video` not `expo-av`; `expo-image` with `source="sf:name"` not `expo-symbols`/`@expo/vector-icons`; `process.env.EXPO_OS` not `Platform.OS`; `React.use` not `React.useContext`; CSS `boxShadow` string, never legacy shadow/elevation props.
- Full worked `theme/colors.ts` pattern using `Color.ios.*` / `Color.android.dynamic.*` wrapped in `Platform.select`.
- iOS auto-re-resolves semantic colors on theme change; Android needs `useColorScheme()` called inside the component to force a re-render (called out as required "when React Compiler memoizes the component").

**expo-ui** (`@expo/ui` — SwiftUI/Compose)
- Requires **SDK 56+** for the universal layer to run in Expo Go; drop-in replacements and platform-specific layers work back to SDK 55.
- `allowed-tools` frontmatter scopes Bash to exactly `node *expo-ui/scripts/list-components.js *` — the only skill observed with a script-scoped Bash allowlist for component discovery.
- Concrete gotcha: `BottomSheet` props are `isPresented`/`onDismiss` — using `@gorhom/bottom-sheet`-style props (`isOpened`, `onIsOpenedChange`) "will silently do nothing," no error thrown.
- `List` is explicitly **not virtualized** — real native grouped rows (Settings-screen style), each `ListItem` a live JS-thread node; large/unknown-length lists must use `FlatList`/`FlashList` instead.
- Mixing `@expo/ui/swift-ui` and `@expo/ui/jetpack-compose` imports on the wrong platform crashes at runtime with "Unable to get view config" — must isolate in `.ios.tsx`/`.android.tsx` under `components/` (Expo Router route files don't support platform extensions).

**expo-animation** (co-authored with Emil Kowalski, cross-published to `emilkowalski/skills`)
- Explicit two-runtime mental model (RN runtime vs UI runtime) and a numbered "Build Sequence" (should it animate → purpose → tool → properties → timing/spring → JS-thread discipline → press → haptics → reduced motion).
- Concrete spring config table using Apple's two-parameter form: `{ duration, dampingRatio }`, e.g. sheet/drawer = `{ duration: 300, dampingRatio: 0.8, velocity }`.
- Hard "Never Ship" table: `PanResponder`→`Gesture.Pan()`, `runOnJS` (deprecated in Reanimated 4)→`scheduleOnRN`, animating `height`/`width`/`margin`/`flex`→`transform`+`opacity`, `Easing.in(...)`→named bezier constants.
- Dependency install table pins the *mechanism*, not a version: always `npx expo install <pkg>` so versions resolve against the project's SDK.
- Notes a Reanimated 4 hard requirement: **New Architecture**. Also flags that recent Expo SDKs default-enable `CADisableMinimumFrameDurationOnPhone` for ProMotion 120fps — confirm it's present, add if not.
- References a `RECIPES.md` file for ready-made implementations (drag-to-dismiss sheet, swipe-to-delete, etc.) — this file exists alongside `SKILL.md` at the skill root, not under `references/`, and wasn't independently opened for this report; treat its exact contents as unverified.

**expo-design-system**
- Explicit adoption order: detect an existing styling system (NativeWind/Tamagui/Restyle/Unistyles/styled-components) first — "never introduce a second system beside an existing one."
- Concrete default scales given as real numbers: spacing on a 4-pt grid (`xs:4, sm:8, md:16, lg:24, xl:32, xxl:48`), radius (`sm:8, md:12, lg:16, full:9999`), motion durations (`fast:150, base:250, slow:400`).
- Component promotion rule requires **all three** conditions (used in 2+ screens, nameable role, API smaller than implementation) before extracting to `src/components/`.
- Explicitly refuses to wrap already-native-feeling platform components (`Switch`, `DateTimePicker`, stack headers, `@expo/ui` views) "just to route them through the system."

**expo-tailwind-setup**
- Pins exact (and unusual) versions: `nativewind@5.0.0-preview.2`, `react-native-css@0.0.0-nightly.5ce6396`, plus a forced `resolutions.lightningcss = 1.30.1` in `package.json` for compatibility.
- States plainly: **no `babel.config.js` needed** with this stack — delete any NativeWind babel preset if present (a real regression trap from the v4 setup).
- Requires manual CSS-element wrapping via `useCssElement` (react-native-css) since raw RN primitives don't understand `className` — ships full wrapper source for `View`/`Text`/`Image`/`ScrollView`/etc.

**expo-data-fetching**
- Opens with a capitalized mandate: "You **MUST** use this skill for ANY networking work."
- Concrete preference: avoid axios, prefer `expo/fetch`.
- Security rule with a wrong/right pair: never `AsyncStorage.setItem("token", ...)`, always `expo-secure-store`.
- `EXPO_PUBLIC_` prefix rule spelled out precisely: only those vars reach the client bundle, inlined at **build time** not runtime, dev server restart required after `.env` changes.

**expo-dom**
- Hard rule list: file needs the literal `'use dom';` directive, single default export, own file (never inline/combined with native components), serializable props only.
- Platform behavior table: iOS → WKWebView, Android → WebView, Web → rendered as-is (the `dom` prop is ignored on web).
- Explicit list of hooks that **don't** work directly inside a DOM component (`useLocalSearchParams`, `useGlobalSearchParams`, `usePathname`, `useSegments`, `useRootNavigation`, `useRootNavigationState`) — must be read in the native parent and passed as props.

**expo-web-to-native**
- Frames the migration as a strangler-fig pattern with a 6-step mermaid flow (assess → scaffold shell → DOM-shell shippable on day one → strangle screens to native by value → wire data/auth/storage → ship).
- Concrete business-model callout: browser payment SDKs don't carry over — in-app digital goods must go through store IAP (e.g. RevenueCat, ~30% cut), "a business-model call to make now, not at App Store review."
- Verification philosophy: "verify by running, not compiling" — a green `expo export` only proves a screen *bundles*, not that it renders; names two specific external tools for parity-checking (`agent-browser` for the web original, `argent` for the native sim) and says to ask the user to install them rather than fall back to manual screenshots.
- Explicitly designed to be run as an unattended `/goal` loop that re-reads its own `SKILL.md` each iteration (`references/run-as-goal.md`).

**expo-module**
- Explicitly redirects one specific task elsewhere: migrating an existing Swift module from the definition DSL to Expo Modules API 2.0 macros (`@ExpoModule`, `@JS`, `@Event`) is **not** this skill — it's `expo-migrate-module` from a *different, separate plugin* (`expo-experiments`), which is not installed here.
- Shows matching Swift/Kotlin/TypeScript module-definition DSL side by side; notes iOS `expo-module.config.json` uses just the class name while Android needs the fully-qualified package+class name.
- Practical scaffolding notes: local modules don't generate an `index.ts` barrel by default (opt-in via `--barrel`); `ViewEvent` implies `View`; use `add-platform-support` instead of manually copying native directories when adding a platform later.

**expo-brownfield**
- Two named approaches with a real decision matrix: **isolated** (prebuilt AAR/XCFramework, no Node/RN tooling needed by the native team) vs **integrated** (RN sources added directly to existing Gradle/CocoaPods build, single team/single build).
- Hard version floor: **Expo SDK 55 is the minimum** for brownfield — earlier SDKs lack `expo-brownfield` itself and the `ExpoReactHostFactory`/`ExpoReactNativeFactory` entry points. Must pin explicitly: `npx create-expo-app@latest my-project --template default@sdk-55`.
- Integrated approach additionally requires CocoaPods on iOS (`sudo gem install cocoapods`); isolated approach needs neither CocoaPods nor RN tooling in the consuming app.

**expo-app-clip**
- Ships a fully worked, numbered 10-step walkthrough with real example bundle IDs (`com.bacon.may20`, Clip = `com.bacon.may20.clip`) and a real Apple Team ID format (`XX57RJ5UTD`).
- Names exact tools: `bun create target clip` (installs `@bacon/apple-targets`), `bunx setup-safari` (registers bundle IDs, prints starter AASA JSON), `bunx testflight` (builds/submits both targets, shares one Distribution Certificate but separate provisioning profiles per target).
- Concrete AASA gotcha: `setup-safari`'s printed JSON only covers the parent app — you must hand-add the `appclips` block yourself for the Clip's full app ID (`<TeamID>.<ClipBundleID>`).
- Notes a device-detail requirement: `deploymentTarget: "17.6"` on the Clip's target config because App Clips have a higher minimum size limit on iOS 17.6.

**expo-upgrade**
- Names a specific, dated regression: SDK 55 with Hermes V1, all of SDK 56, and SDK 57 builds before `expo@57.0.9` have "a Hermes V1 memory regression" with `react-native-worklets`/`react-native-reanimated" — explicit guidance is "skip SDK 56 and upgrade directly to SDK 57," and never opt in/out of Hermes V1 manually.
- Concrete deprecated-package table: `expo-av`→`expo-audio`/`expo-video`, `expo-permissions`→individual per-package permission APIs, `@expo/vector-icons`→`expo-symbols`, `AsyncStorage`→`expo-sqlite/localStorage/install`, `expo-linear-gradient`→`experimental_backgroundImage` CSS gradients.
- Housekeeping instructs deleting `sdkVersion` from `app.json` (let Expo manage it), removing `@babel/core`/`babel-preset-expo`/`expo-constants` as implicit packages, and enabling `experiments.reactCompiler: true` in SDK 54+.
- New Architecture is now the unconditional default — `newArchEnabled: true` is no longer needed in `app.json`.

**expo-dev-client**
- Clear boundary: dev clients needed only for local Expo modules, Apple targets (widgets/clips/extensions), third-party native modules absent from Expo Go, or config plugins/remote push/App-Universal-Links testing.
- Gives the exact `eas.json` `development` build profile shape (`developmentClient: true`, `autoIncrement: true`, `appVersionSource: "remote"`).
- Distinguishes itself from `eas-app-stores`: this skill is for internal/dev distribution; production TestFlight/store submission is the other skill's job.

**expo-examples**
- `allowed-tools` frontmatter scopes Bash to exactly: `gh api`, `git clone`, `npx create-expo`, `npx degit`, `bun create` — a narrow, deliberate command allowlist.
- Two explicit modes: "inspiration/adapt" (read-only study, apply pattern by hand, *never* scaffold on top of the user's existing project) vs "scaffold" (`npx create-expo --example with-stripe` for a fresh greenfield project).
- Hard rule for adapting into an existing app: version-align via `npx expo install <pkg>` rather than copying the example's pinned versions (examples always track *latest* SDK).
- Names the exact source of truth for renamed/dead examples: `meta.json` in the `expo/examples` repo (has `deprecated` and `aliases` maps) — checked live via `gh api`, not hardcoded in the skill.
- Notes the repo's default branch is `master`, not `main` — matters for raw-URL fetches and sparse checkouts.

**expo-skill-feedback**
- This is the skill the plugin's own `hooks/hooks.json` invokes automatically (see Plugin-level agents & hooks below) — every `Skill` tool call and every user prompt triggers its `skill-event.cjs` script in the background (`--detach --quiet`, 5s timeout).
- Feedback submission command is uniform across every other skill's closing section: `npx --yes submit-expo-feedback@latest --category skills --subject "<skill-name>" "<feedback>"`.
- Telemetry is explicitly **off by default / opt-in**, controlled via `EXPO_SKILLS_TELEMETRY=1|0` or `DO_NOT_TRACK=1`, and "CI never sends telemetry" regardless of the flag.
- Has a distinct "eval candidate" flow for reporting tasks that broke the agent — requires explicit user approval before submitting (never auto-submitted in headless/CI runs), and a strict `Task/Expected/Actual/Wrong approach/Evidence` template.

## EAS (paid service) skills — concrete mechanics

Every EAS skill's `SKILL.md` opens with an explicit cost disclaimer pointing to `https://expo.dev/pricing` — this is a repeated, deliberate pattern across all 5, not just a formality in one.

**eas-app-stores**
- Real `eas.json` example with `appVersionSource: "remote"` (EAS manages build numbers) and `ios.resourceClass: "m-medium"`.
- `npx testflight` is called out as a purpose-built shortcut for iOS TestFlight submissions, distinct from the general `eas build ... --submit` flow.
- Explicitly hands off CI/CD authoring to `eas-workflows` ("it works from the live workflow schema") rather than duplicating that logic.

**eas-hosting**
- Concrete boundary table for when *not* to use API routes: data already public, no secrets needed, real-time needs (→ WebSockets/Supabase Realtime), simple CRUD (suggests **Firebase, Supabase, or Convex** by name), file uploads (→ direct-to-storage/S3 presigned URLs), auth-only (→ Clerk/Auth0/Firebase Auth).
- Runtime is **Cloudflare Workers** — explicitly no Node.js `fs`, no native Node modules, 30s CPU timeout, no persistent connections (WebSockets need Durable Objects) — must use Web Crypto/`fetch`/`Response` instead of Node equivalents.
- Recommends edge-compatible DBs since there's no filesystem: Cloudflare D1, Turso, PlanetScale, Supabase, Neon — with a worked Turso `createClient` example.
- One `eas deploy` call ships both the web bundle and any bundled `+api.ts` routes together; `--prod` for production vs a PR-preview URL by default.

**eas-observe**
- Version-pinned "known gaps between docs and shipped code" section, explicitly verified against `eas-cli 21.8.0` and `expo-observe 57.0.9` — flags that older doc builds omit `observe:routes`/`observe:session` (all six commands exist), that navigation aliases are `nav_cold_ttr`/`nav_warm_ttr`/`nav_tti` (no bare `cold_ttr`), and that `ObserveErrorBoundary`/`Observe.reportError` are exported but undocumented (still no crash reporting — use Sentry/BugSnag).
- SDK-gated API surface: `AppMetricsRoot` + global `markInteractive()` on SDK 55, vs `ObserveRoot` + `useObserve()`/`<ObserveInteractiveMarker />` on SDK 56+.
- Requires a dev or production build — "the native library is not in Expo Go."

**eas-simulator**
- Marked experimental/hidden API requiring `eas-cli >= 20.3.0`; always invoked via `npx --yes eas-cli@latest` rather than a possibly-stale global install.
- Concrete platform gate: on non-macOS (Linux/CI/cloud sandbox), it's the only path and should proceed once availability is confirmed; on macOS, must **ask first** unless the user explicitly said cloud/remote/shareable, since local sims exist and cost/latency apply.
- `simulator:availability --json` must be checked before starting any session — it's a read-only, no-billing check; if `available: false`, must not call `simulator:start` and should fall back to local tooling instead of dead-ending.
- Three build modes with a real trap called out twice: Mode A (local release build, no live reload), Mode B (EAS sim build, explicit-only), Mode C (local dev build + tunnel, the only one supporting live Fast Refresh) — reusing *any* existing/static build for a "live edits" request is explicitly wrong, even if a build happens to already be installed on the sim.
- Controller verb table: tap is literally named `press`, not `tap` (called out as a common mistake); `webPreviewUrl` must never be opened *on* the simulated device itself (a documented past failure: "renders a browser-in-a-browser").
- Session hygiene is heavily emphasized: always `--name` the session (3–6 words, sentence case, no ids/dates), always `simulator:stop` + reset `.env.eas-simulator` on every exit path since billing continues until stopped.

**eas-update-insights**
- Four commands: `update:list`, `update:insights <groupId>`, `update:view --insights`, `channel:insights`.
- Crash rate formula given explicitly: `failedInstalls / (installs + failedInstalls) * 100`.
- Real limitation called out: "installs" means downloads, not confirmed launches — a launch only registers on the *next* update check, "typically up to 24h later" — so metrics lag real-world state.
- `otaTotalUniqueUsers` in channel insights is a sum over only the top-N `mostPopularUpdates` the server returns, so it "may undercount total OTA reach" if more than N updates are active.

**eas-workflows**
- Deliberately fetches live sources rather than hardcoding: JSON Schema at `https://api.expo.dev/v2/workflows/schema`, syntax docs and pre-packaged-job docs pulled from the `expo/expo` GitHub raw content — via a bundled `scripts/fetch.js` (Node, ETag-cached).
- Validation must go through `eas-cli`'s own `workflow:validate` (requires a logged-in session + linked project) — explicitly: "Do not replace this command with a local YAML or JSON Schema validator."
- Workflow files capped at 16 KiB, live under `.eas/workflows/*.yml`.

## Top-level plugin agents & hooks

- **`agents/openai.yaml`** (plugin root, distinct from each skill's own `agents/openai.yaml`): registers the plugin's Codex/OpenAI-app identity — display name "Expo," a default prompt describing when to reach for it (Expo Router UI, API routes, native modules, dev clients, data fetching, styling, Codex Run actions, EAS workflows, deployments, SDK upgrades). This is metadata for the Codex "app" surface, not a Claude Code subagent.
- **`hooks/hooks.json`**: wires two hooks, both invoking the same script (`skills/expo-skill-feedback/scripts/skill-event.cjs`), run detached/quiet with a 5s timeout:
  - `PostToolUse` (matcher: `Skill`) — fires with `--initiator ai` every time any skill tool call completes.
  - `UserPromptExpansion` — fires with `--initiator user` on user prompts.
  This is the plumbing behind the plugin's opt-in anonymous usage telemetry described in `expo-skill-feedback`; the hook always runs, but the script itself is gated by the `EXPO_SKILLS_TELEMETRY` env var / `DO_NOT_TRACK` — it does not send data unless telemetry was explicitly turned on.
- **Expo MCP server registration**: `.mcp.json` at the plugin root registers `expo` as an **HTTP** MCP server at `https://mcp.expo.dev/mcp` — this is the "Expo MCP" mentioned in the docs page ("registers the Expo MCP server" for Claude Code and Codex). It was not independently queried for its tool list as part of this research pass (out of scope — this report covers the skills, not a live MCP tool-schema dump).
- Cross-tool manifests confirm the same version/metadata are mirrored for Cursor (`.cursor-plugin/plugin.json`) and Grok (`.grok-plugin/plugin.json`), and Codex's manifest additionally carries a richer marketing `interface` block (`longDescription`, `defaultPrompt` array, `brandColor: "#000020"`) not present in the Claude-facing `plugin.json`.

## How this applies to this repo (`next-crafter-team-workspace`)

This workspace's `package.json` declares exactly one dependency, `convex`, and there is no `expo` package, no `app.json`/`app.config.*`, and no mobile/native app code anywhere in the tree — only a Convex backend under `convex/`. Per `expo-overview`'s own stated gate ("a bare React Native project with no `expo` dependency is not Expo work"), **none of these 24 skills will auto-trigger in this repo today** — including `expo-overview` itself, since neither an Expo mention nor an `expo` dependency is present.

The moment this repo gains an `expo` dependency (e.g. `npx create-expo-app@latest` scaffolding a companion mobile client against the existing Convex backend) or a request explicitly names Expo/EAS, `expo-overview` becomes the correct entry point — it will classify the goal and route to the matching leaf skill (most likely `expo-project-structure` for the initial scaffold, then `expo-data-fetching`/a Convex-specific pattern for wiring the mobile client to this project's Convex functions).
