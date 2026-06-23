---
name: ornold-browser
description: >
  Ornold MCP browser automation for antidetect browsers. Vision-first interaction
  (screenshot + AI element detection + coordinate clicks), flow recording & replay,
  CAPTCHA solving, and antidetect profile management across Linken Sphere, Dolphin Anty,
  Wade X/Wadex, Vision Browser, AdsPower, Octo Browser, Multilogin, MoreLogin, 0DETECT,
  GoLogin, Undetectable, Incogniton, and Indigo.
  Use this skill whenever ornold-browser MCP tools are connected, or when the user asks about
  browser automation, antidetect browsers, flow recording, running recorded flows, CAPTCHA solving
  in browser profiles, multi-browser parallel execution, or managing antidetect browser profiles.
  Also trigger when user mentions "record a flow", "replay flow", "run flow", "vision mode",
  "browser profiles", "warmup profiles", "mass registration", or "parallel browsers".
  Proactively use when ornold-browser1 or ornold-browser MCP tools appear in available tools.
---

# Ornold MCP — Browser Automation for Antidetect Browsers

You are working with Ornold MCP, a platform that gives you full control over antidetect browser
profiles. The MCP client runs locally (connects to browsers via CDP), while the server handles
auth, billing, CAPTCHA solving, vision analysis, and human-like behavior generation.

## Architecture

```
AI Agent (you) → stdio MCP → ornold-mcp client (local) → WebSocket → ornold-mcp server (remote)
                                    ↓                                        ↓
                              CDP / local APIs                   Captcha solving, Vision analysis,
                              (all enabled providers)            Human-like ops, Flow storage
```

Everything you do goes through MCP tools. The server generates human-like mouse movements
(Bezier curves), typing delays (gaussian distribution), and per-browser behavioral profiles
automatically — you don't need to add artificial delays or randomization yourself.

## Knowledge Base — ALWAYS Check First

Before starting ANY non-trivial task on a platform (Facebook, Google, TikTok, Instagram,
ad accounts, registration, warmup, mass operations, etc.), you MUST call `knowledge_search`
FIRST to find a proven playbook.

```
knowledge_search({query: "create facebook fan page"})
knowledge_search({query: "warmup tiktok profile"})
knowledge_search({query: "register google account"})
```

The response contains:
- `=== UNIVERSAL RULES ===` — global behavior rules (always read first)
- `=== MATCHED PLAYBOOK ===` — step-by-step guide for the requested task
- `=== ALSO FOUND ===` — related patterns

### Mandatory protocol when a playbook is matched

1. Read `Agent protocol → Before start` — ALWAYS perform these checks first
2. Ask the user for any missing required inputs (profile selection, geo, data) — DO NOT assume
3. Verify prerequisites (profiles connected, geo matches, warmup status)
4. Execute the steps from the playbook
5. Handle `Failure modes` proactively — if you hit one, stop and report, don't retry blindly
6. After completion, follow `Agent protocol → After complete`

### When to skip knowledge_search

- Pure read-only data extraction (no logins, no account creation)
- User explicitly says "just navigate to URL X" with no further task
- Trivial single-step actions ("take a screenshot of google.com")

When in doubt — search. Inter plan: 100 searches/month, Pro: 1000. Free has no access.

If `knowledge_search` returns "No matching playbook found" — proceed cautiously, ask the user
to confirm each major step, and never assume platform quirks.

## Contributing private playbooks (Pro plan)

Pro users can save their own playbooks visible only to them across sessions.

When user says "save this as playbook", "запиши флоу", "запомни процесс":

```
1. knowledge_contribute_start                   → returns interview protocol
2. Walk user through the questions one by one   → collect title, domain, intent, steps, failure modes
3. Format as Markdown content                   → sections required (Prerequisites, Steps, Failure modes, ...)
4. knowledge_contribute_submit({title, domain, intent, content, tags})
```

Manage entries:
```
knowledge_list_mine          → list your private entries
knowledge_update({id, ...})  → edit one
knowledge_delete({id})       → remove
```

Limit: 100 contributions/month. Entries are private — never visible to other users.

## Vision-First Interaction

This is the primary interaction mode. Instead of CSS selectors (which break and trigger bot
detection), you see the page through screenshots and click by normalized coordinates.

### The Loop

```
1. browser_parallel_vision_analyze_grouped    → screenshot + AI element detection
2. Read the detected elements with labels and [x1, y1, x2, y2] boxes (normalized 0-1)
3. browser_parallel_click_normalized_box      → click buttons/links by target box
4. browser_parallel_type_at_normalized_box    → click a field box, verify focus, type full text
5. Repeat from step 1 to see what changed
```

### Critical Rules

- ALL clicks go through `browser_parallel_click_normalized_box` with coordinates from vision analysis
- NEVER use `browser_parallel_evaluate` or `browser_parallel_run_code` to click, scroll, focus, or interact with elements — this bypasses antidetect protection
- `browser_parallel_evaluate` is ONLY for reading data (extracting text, checking URLs, page state)
- `browser_parallel_snapshot` is NOT available in vision mode
- To type into a field from vision analysis, use `browser_parallel_type_at_normalized_box`
- Use `browser_parallel_type_focused` only after a field is already focused
- Use `browser_parallel_press_key` only for special keys such as Enter, Tab, Escape, arrows, or shortcuts

### For typing text

Send the target field box and full text in one call:
```
browser_parallel_type_at_normalized_box({
  box: [0.3, 0.25, 0.7, 0.30],
  text: "hello",
  clearFirst: true
})
```

The server clicks with human-like mouse movement, verifies the focused editable element, then types with human-like keyboard timing and value readback.

## Flow Recording & Replay

Flows let you record a sequence of actions once, then replay them across many browsers
without AI involvement — saving tokens and enabling background execution.

### Recording a Flow

```
1. browser_start_recording          → begin capturing actions
2. Perform actions normally          → navigate, vision analyze, click, type, solve captcha
3. browser_stop_recording            → stop and preview recorded steps
4. browser_edit_flow                 → remove unwanted steps or change values to templates
5. browser_save_flow({name: "..."})  → save to server (encrypted, never exposed to client)
```

During recording, every tool call is automatically converted to a flow step:
- `navigate` → recorded with URL
- `click_normalized_box` → recorded with box coordinates + element label from last vision analysis (for verify)
- `press_key` → consecutive characters merged into a single `type` step
- `wait_for` → recorded with text/timeout
- `solve_captcha` → recorded as captcha step
- `vision_analyze` → NOT recorded (used for context only, labels saved for click verification)

### Editing Before Saving

After `stop_recording`, the steps are in memory but not saved yet. Use `browser_edit_flow` to clean up:

```
browser_edit_flow({
  removeSteps: [5, 6],              // remove steps by 1-based index
  editStep: 3,                       // modify a specific step
  editFields: {text: "{{row.email}}"}  // replace hardcoded value with template
})
```

Templates let you inject per-browser data during replay:
- `{{row.email}}` → value from dataset row mapped to each browser
- `{{row.password}}` → another dataset field
- `{{browser.index}}` → 0, 1, 2... (browser position)
- `{{browser.id}}` → runtime browser ID
- `{{random.email}}` → auto-generated email
- `{{random.string}}` → random alphanumeric string

### Running a Flow

```
browser_run_flow({
  flowId: "fl_abc123",
  dataset: [
    {"email": "user1@mail.com", "password": "pass1"},
    {"email": "user2@mail.com", "password": "pass2"}
  ],
  verifyMode: "never",           // or "always" or "auto"
  stagger: {"min": 2000, "max": 8000}  // ms delay between browsers
})
```

**verifyMode** controls element re-detection:
- `never` — click recorded coordinates (fast, free, use when layout is stable)
- `always` — re-detect elements via vision analysis before each click (reliable, costs vision credits)
- `auto` — re-detect only the first click after each navigation (compromise)

**Replay strategy — always start cheap, escalate if needed:**
1. First run with `verifyMode: "never"` — fast, free, works when page layout hasn't changed
2. If flow fails (clicks miss targets) → rerun with `verifyMode: "auto"` — re-detects after navigations only
3. If still failing → rerun with `verifyMode: "always"` — full re-detection every click

Never start with "always" — it wastes vision credits when coordinates are usually fine.

**stagger** spreads execution across browsers with random delays to avoid detection patterns.
Without stagger, all browsers act simultaneously — suspicious for anti-bot systems.

The response returns an `executionId`. Check progress with:
```
browser_flow_status({executionId: "exec_xyz"})
```

Cancel with:
```
browser_cancel_flow({executionId: "exec_xyz"})
```

### Managing Flows

```
browser_list_flows      → list all saved flows (id, name, step count, date)
browser_delete_flow     → delete by flowId
```

Flow steps are stored encrypted on the server. You never see the raw step data through MCP tools —
only metadata (name, step count). This is by design.

## CAPTCHA Solving

Automatic detection and solving across all browser profiles in parallel.

```
browser_detect_captcha           → detect captcha type on page
browser_solve_captcha            → detect + solve + inject token (all-in-one)
```

Supported types: reCAPTCHA v2/v3/Enterprise, hCaptcha, Cloudflare Turnstile, GeeTest v3/v4,
FunCaptcha (Arkose Labs), Amazon WAF, Yandex Smart Captcha, MTCaptcha, image CAPTCHA.

For press-and-hold (PerimeterX):
```
browser_detect_press_hold → browser_solve_press_hold
```

Usually just call `browser_solve_captcha` — it handles everything. Only use detect separately
if you need to check before solving.

## Antidetect Browser Management

Provider management tools are registered only for providers enabled in the MCP server config.
If the user asks for a provider and its tools are missing, do not claim the provider is unsupported.
Tell the user which flag/env is required and ask them to restart the MCP server.

Supported providers and tool prefixes:

| Provider | Tool prefix | Enable with |
|----------|-------------|-------------|
| Linken Sphere | `linken_*` | `--linken-port` or `LINKEN_PORT` |
| Wade X / Wadex | `wadex_*` | `--wadex-port` or `WADEX_PORT` |
| Dolphin Anty | `dolphin_*` | `--dolphin-port`, `--dolphin-token`, `DOLPHIN_PORT`, `DOLPHIN_API_TOKEN` |
| Vision Browser | `vision_*` | `--vision-token`, optional `--vision-port`, `VISION_TOKEN`, `VISION_PORT` |
| AdsPower | `adspower_*` | `--adspower-url`, optional `--adspower-key`, `ADSPOWER_URL`, `ADSPOWER_API_KEY` |
| Octo Browser | `octo_*` | `--octo-token`, `--octo-url`, `OCTO_API_TOKEN`, `OCTO_LOCAL_API_URL` |
| Multilogin | `multilogin_*` | `--multilogin-token`, `--multilogin-url`, `MULTILOGIN_API_TOKEN`, `MULTILOGIN_LOCAL_API_URL` |
| MoreLogin | `morelogin_*` | `--morelogin-url`, `--morelogin-port`, `MORELOGIN_LOCAL_API_URL`, `MORELOGIN_PORT` |
| 0DETECT | `detect0_*` | `--detect0-url`, `--detect0-port`, optional `--detect0-token` |
| GoLogin | `gologin_*` | `--gologin-token`, optional `--gologin-url` |
| Undetectable | `undetectable_*` | `--undetectable-url`, `--undetectable-port` |
| Incogniton | `incogniton_*` | `--incogniton-url`, `--incogniton-port` |
| Indigo Browser | `indigo_*` | `--indigo-token`, optional `--indigo-url` |

### Start and attach workflow

For every antidetect provider, the correct sequence is:

```
1. List profiles/sessions with the provider list tool
2. Start the chosen profile/session with the provider start tool
3. Read returned CDP/debug data (port, wsEndpoint, browserWSEndpoint, or similar)
4. If the provider did not auto-connect, call browser_attach with the returned port/ws/id
5. Call browser_list and confirm the browser is connected
6. Only then navigate, analyze, click, type, record flows, or solve CAPTCHA
```

Never stop after starting a provider profile if the user asked to use the browser. Starting a profile
opens the browser process; `browser_attach`/`browser_list` confirms Ornold can control it.

### Linken Sphere (linken_* tools)

```
linken_get_instances              → list all sessions
linken_create_quick_sessions      → create new sessions
linken_start_instances            → start sessions (auto-connects CDP)
linken_stop_instances             → stop sessions
linken_set_connection             → set proxy (type, ip, port, login, password)
linken_check_connection           → verify proxy works
linken_set_geo                    → set timezone, language, geolocation
linken_import_cookies             → import cookies from JSON or file
linken_export_cookies             → export cookies
linken_start_warmup               → warm up with random browsing
```

### Dolphin Anty (dolphin_* tools)

```
dolphin_get_profiles              → list profiles (cloud API)
dolphin_start_profile             → start profile (local, auto-connects CDP)
dolphin_stop_profile              → stop profile
```

### Wade X / Wadex (wadex_* tools)

Same session model as Linken Sphere:

```
wadex_get_instances               → list sessions
wadex_create_quick_sessions       → create sessions
wadex_start_instances             → start sessions
wadex_stop_instances              → stop sessions
```

### AdsPower (adspower_* tools)

```
adspower_list_profiles            → list browser profiles
adspower_open_browser             → start/open profile via local API
adspower_list_active_browsers     → list active local browsers
adspower_close_browser            → stop profile
```

### Octo Browser (octo_* tools)

```
octo_get_profiles                 → list profiles via cloud API
octo_start_profile                → start profile via local client API
octo_list_active_profiles         → list active local profiles
octo_stop_profile                 → stop profile
```

For Octo, cloud profile listing needs `--octo-token`; local start/stop needs `--octo-url`
(commonly `http://127.0.0.1:58888`).

### Multilogin (multilogin_* tools)

```
multilogin_profile_search                     → search profiles
multilogin_start_browser_profile_with_selenium → start profile and return automation data
multilogin_stop_browser_profile               → stop profile
```

### MoreLogin (morelogin_* tools)

```
morelogin_list_profiles          → list profiles
morelogin_start_profile          → start profile and return debug data
morelogin_stop_profile           → stop profile
morelogin_get_all_debug_info     → get debug info for opened profiles
```

### 0DETECT (detect0_* tools)

```
detect0_list_profiles            → list profiles
detect0_start_profile            → start profile
detect0_create_profile           → create profile
```

### GoLogin (gologin_* tools)

```
gologin_get_browser_list_new      → list profiles
gologin_add_browser               → create profile
gologin_get_browser_by_id         → get profile details
```

### Undetectable (undetectable_* tools)

```
undetectable_list_profiles        → list profiles
undetectable_start_profile        → start profile
undetectable_stop_profile         → stop profile
```

### Incogniton (incogniton_* tools)

```
incogniton_list_profiles          → list profiles
incogniton_start_profile          → start profile
incogniton_stop_profile           → stop profile
```

### Indigo Browser (indigo_* tools)

```
indigo_list_profiles              → list profiles
indigo_start_profile              → start profile
indigo_stop_profile               → stop profile
```

### Vision Browser (vision_* tools)

Full profile management: folders, profiles, proxies, fingerprints, cookies, tags, statuses.

### After starting profiles

Profiles auto-connect via CDP. Verify with:
```
browser_list                      → shows connected browsers with IDs
browser_status                    → sync status of all browsers
```

## Anti-Detection Best Practices

These matter — violating them can burn profiles:

1. **Navigate via Google search**, not direct URLs. Exception: same-domain links and navigating
   within a site you're already on.
2. **Dismiss cookie banners and popups** before interacting with page content.
3. **Never navigate to chrome:// URLs** or modify browser internals via JavaScript.
4. **Never modify fingerprints via JavaScript** — the antidetect browser handles this.
5. **Use `browser_parallel_type` for short inputs** — it types character by character with
   human-like delays. Don't use `fill` (which sets value instantly) for visible form fields.
6. **Don't add artificial delays** — the server generates gaussian-distributed delays,
   Bezier mouse curves, and per-browser behavioral profiles automatically.

## Parallel Execution

All `browser_parallel_*` tools run on all connected browsers simultaneously by default.
To target specific browsers, pass `browserIds: ["id1", "id2"]`.

```
browser_parallel_navigate({url: "https://google.com"})                    → all browsers
browser_parallel_navigate({url: "https://google.com", browserIds: ["a"]}) → only browser "a"
```

For per-browser data (different URLs, different form values):
```
browser_parallel_navigate_multi({urls: {"browser1": "url1", "browser2": "url2"}})
```

## Common Workflows

### Mass Registration
1. Start/create profiles → connect browsers
2. `browser_start_recording`
3. Navigate to registration page via Google search
4. Vision analyze → fill form fields → solve captcha → submit
5. `browser_stop_recording`
6. Edit flow: replace hardcoded values with `{{row.email}}`, `{{row.password}}` templates
7. Save flow
8. `browser_run_flow` with dataset of unique credentials per browser

### Profile Warmup
1. Start profiles
2. Navigate to popular sites (Google, YouTube, news)
3. Scroll, click random links, build browsing history
4. Or use `linken_start_warmup` for automated warmup

### Data Collection
1. Navigate to target pages across profiles
2. Use `browser_parallel_evaluate` to extract data (reading only!)
3. Aggregate results
