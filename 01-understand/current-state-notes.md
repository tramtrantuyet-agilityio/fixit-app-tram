# FixIt: current-state notes

Phase: Shaping, before Gate 1. Step 1: understand the current state.
Walked on 18 Nov 2026 by Claude, as a first-time user of the prototype.
This document only describes. It does not propose solutions.

> **Template note:** `templates/01-current-state-notes.md` was not in this repo (or in `tramtrantuyet-agilityio`), so it could not be used. The sections below follow the five parts of the brief. Add the template and these notes can be moved into it.

## Sources

| Source | What it is | How it was used |
|---|---|---|
| `CLAUDE.md` | Context pack: vision, users, key journey, constraints, criteria | The frame for everything below |
| [Prototype](https://claude.ai/artifact/8kTc1MDf8wnb3NoepUd6oG) | Called "Good example" in `CLAUDE.md`. The page is titled "FixIt Prototype (Copy)". It is a clickable prototype with six surfaces: Landing, Web console, Desktop app, SDKs, Claude, Tazko | Opened in headless Chromium at 1440×900 and walked by clicking real controls. Hash routes were used only to reach a surface's start screen |
| Other context files | None found. There are no research notes, interviews, data or template | Shows up as Unknowns below |

How to read the labels:
- **Fact**: seen in the prototype or written in `CLAUDE.md`.
- **Inference**: my reading of the facts.
- **Unknown**: the sources don't answer it.

All issue IDs, names and numbers below come from the prototype's demo data (Acme, Mia Tran, FIX-142 and so on). None of it is real usage data.

---

## 1. Walkthrough with screenshots

The screenshots are in [`screenshots/`](screenshots/), numbered in the order they were taken.

| # | Journey stage | Screen | What I did and saw |
|---|---|---|---|
| 01–02 | Get the app | Landing: hero and full page | "Report the bug in two seconds. Hand over the whole story." There are download buttons for Windows and macOS, a "files straight into" list (Tazko, GitHub, Trello, Jira, Linear, Slack, webhook), a 3-step "How it works", a feature grid, SDK snippets and a self-host section |
| 03 | Get the app | Download clicked | Only a toast appears: "download starts here in the real site". Installation isn't prototyped |
| 04 | Get the app | Desktop: First run | "Nothing to allow on Windows". It lists Screen Recording, Accessibility and Microphone (later), with "Start FixIt when I sign in" |
| 05 | Get the app | Desktop: Sign in | You enter your server, get a device code (KQ7M-4T2P) and approve the device in the browser. You can also choose "Use without an account" |
| 06 | Get the app | Desktop: Home | "Hi Mia", with capture tiles and their hotkeys. The Instant Replay buffer is on (142 MB). A "Fixed: Export CSV… Is it fixed for you?" banner and recent captures are shown |
| 07 | Capture | Tray menu | 15 actions across Screenshot, Record and Tools |
| 08–10 | Capture | Capture bar → element picker | Hovering highlights the widget. Clicking selects it as "'Pay now' button · 459 × 39". An on-screen toolbar then offers annotation tools and a **Report** button |
| 11–12 | Capture | Image editor | It knows the window, app, URL, version (4.12.0) and OS (Windows 11). Auto-redact says "2 items blurred: card number and account name" |
| 13 | Report | New report | The title is suggested and 4 steps are recorded from the last 60 s. The only prompt is "What happened? One sentence is enough". Expected, Actual, How often and Severity are also on the form. Send to: FixIt (always), Tazko (on), GitHub. "7 of 8" context items are attached and "3 items blurred: 2 email addresses and 1 card number" |
| 15 | Report | Sent | "Reported as **FIX-143**. Linh Pham was assigned from code owners. You will be told when it is fixed." It also says "Tazko card created" |
| 16 | Triage | Console sign-in | Google, Microsoft or SSO. "Access is by invite. Unknown accounts are refused and recorded in the audit log" |
| 17 | Triage | Overview | Shows the KPIs (47 new reports this week, 7 open, median fix time 1d 6h, 99.62 % crash-free sessions), a chart, "Needs attention" and a channel mix (Desktop 46 %, Web SDK 27 %, iOS 19 %, Claude 8 %) |
| 18, 30 | Triage | Issues | Grouped "by stack, screen and screenshot similarity". After my desktop report the list holds both FIX-142 and the new FIX-143, which is the same bug, and the Open count goes from 7 to 8 |
| 19 | Triage | Issue FIX-142 | Tabs for Replay, timeline, network (504 after 30 s), console and stack. The assignee is suggested from CODEOWNERS. "Possible duplicates: FIX-134 · 92 % similar" |
| 20 | Triage | Merge | One click: "Merged FIX-134 into FIX-142" |
| 21 | Fix | Create Tazko card dialog | Board and list, plus attachments, a replay link and "AI-written repro steps as a checklist". It says "When the card is marked done, FixIt resolves this issue" |
| 31 | Fix | Fix with a coding agent | `fixit pull FIX-142 --format markdown \| claude`, or reading the issue over MCP. Suspected files are listed |
| 23–24, 32 | Fix | Tazko board and card | Cards with a FixIt badge. After my report, Triage holds **both** [FIX-142] and [FIX-143] |
| 25–26 | Close the loop | Card marked done → console | "Webhook delivered. FIX-138 is now Resolved in FixIt." The issue shows Resolved, with "Fix verification: Queued, runs when the next build is uploaded" |
| 33 | Close the loop | Claude verifies a fix | A scripted Claude Code session replays the steps on staging: confirm → 200 in 412 ms. "Marked resolved, and the reporter was asked to confirm" |
| 28–29 | Close the loop | Reporter confirms | Home banner for **FIX-137** ("Export CSV…"). Clicking "Yes, fixed" shows "Thanks. **FIX-142** is closed." |

---

## 2. The product as I understand it

**What it does.** FixIt is a self-hosted bug-capture and bug-routing system. A reporter presses a hotkey, points at the broken widget and writes one sentence. FixIt then attaches everything else: the screenshot, the named widget path, the last 60 s of replay, console, network, build, device and the recorded steps. Secrets are blurred before upload. On the server, reports are grouped into issues, assigned from CODEOWNERS and routed by rules to the team's tracker (Tazko first, then GitHub and Slack). A developer or a coding agent gets the whole story, through the console or `fixit pull` / MCP. When the card is done, the issue resolves, the fix is re-checked on the next build and the reporter is asked to confirm.

**Who it's for** (from `CLAUDE.md`, with where each role shows up in the prototype):

| Role | Pain in `CLAUDE.md` | Where the prototype answers it |
|---|---|---|
| Buyer: an engineering org that self-hosts | Wants bug reporting on its own server | `fixit.acme.dev`, "Stored on your own FixIt server", self-host section, free to self-host |
| Reporter: QA, support, PM | The moment is lost; nobody tells them about the fix | Instant Replay, element picker, one-sentence form; "You will be told when it is fixed"; "Is it fixed for you?" banner |
| Triager | Merges duplicates by hand | Similarity grouping, "Possible duplicates", one-click merge, routing rules |
| Fixer: a developer or a coding agent | Reports arrive without context | Replay, timeline, network, console, stack, suspected files, `fixit pull`, MCP |
| Admin: org or instance admin | Screens and logs leak secrets; security blocks cloud tools | Auto-redact, masked replay, hashed user ids, invite-only sign-in, audit log, per-project feature switches, instance-level limits |

**Why Trung Ngo wants it (Inference).** No source says who Trung Ngo is outside the prototype. Inside it, Trung Ngo is the signed-in **Owner of Acme Engineering** (the "TN" avatar). The audit log shows them inviting members, changing roles and setting "Require verified email". That makes Trung Ngo the Buyer and Admin persona. From that, they want FixIt for three reasons:
1. **To keep bug data in-house.** Screens and logs leak secrets, and the security team blocks cloud tools, so the org has nothing usable today.
2. **To stop paying the context tax.** The issue page claims that reports with a replay are fixed 2.1× faster than screenshots alone (an Overview insight), and the overview tracks median time to fix.
3. **To get one place that feeds the tools the team already uses**, mainly Tazko, without hand triage.

If Trung Ngo is instead the real client or sponsor of this project, that is an **Unknown** to confirm.

---

## 3. Today's key journey, screen by screen

Journey from `CLAUDE.md`: **Get the app → Capture → Report → Triage → Fix → Close the loop.**

| Stage | Actor | Screens (shot #) | Actions the user takes | What the system does |
|---|---|---|---|---|
| **Get the app** | Reporter, Admin | Landing (01–03) → First run (04) → Sign in (05) → Home (06). Console sign-in (16) for staff | Download → install (not shown) → allow → enter server → approve device code in the browser | Device-code sign-in; Replay buffer starts. The console is invite-only |
| **Capture** | Reporter | Hotkey or tray (07) → capture bar (08) → hover and click element (09–10). Optional: editor (11–12) or Instant Replay | Ctrl+Shift+B → hover → click | Names the widget, freezes the screen, offers annotation, keeps the last 60 s |
| **Report** | Reporter | New report (13) → Sent (15) | Type one sentence → (optional fields) → Send | Suggests a title, records steps, attaches 7 of 8 context items, blurs PII, assigns from CODEOWNERS, creates a Tazko card |
| **Triage** | Triager | Overview (17) → Issues (18/30) → Issue (19) → Merge (20) | Open the issue → review the suggested duplicate → Merge → set status, assignee and severity | Groups by stack, screen and screenshot. Rules notify Slack and set severity |
| **Fix** | Fixer, coding agent | Create Tazko card (21) / Fix with agent (31) → Tazko board and card (23–24, 32) | Create card or `fixit pull` → work in Tazko | Card gets title, sanitized description, label, screenshot and replay link |
| **Close the loop** | Fixer → system → Reporter | Card done (25) → Issue Resolved and verification queued (26) → Claude verify (33) → Reporter banner (28–29) | Mark card done → (reporter) "Yes, fixed" or "Still broken" | Signed webhook resolves the issue, a replay check is queued for the next build, the reporter is asked to confirm |

Fastest Reporter path seen: **hotkey → click widget → Report → one sentence → Send**. That is 4 interactions plus typing, and it works without opening the editor.

---

## 4. Friction points

### Get the app
2. **Fact:** The desktop app needs a server URL plus a device code approved in the browser, and the console is invite-only ("Unknown accounts are refused"). **Inference:** a QA, support or PM person who hasn't been invited is stuck at the very first step, and the prototype doesn't show how an invite reaches them.
3. **Fact:** First run and Sign in are separate screens in the prototype's nav. **Unknown:** the real order on first launch, and what a reporter sees if they pick "Use without an account" and then try to report.

### Capture
5. **Fact:** The tray has 15 actions. Home shows 6 capture tiles and 4 tools. The prototype also has video and GIF recording, a video editor, present mode, pins, color picker, measure and workflows. **Inference:** the bug-report path ("two seconds") shares space with a full screenshot and recording suite, so a first-time reporter has to find the one path that matters.
6. **Fact:** Two different routes lead to a report: the on-screen toolbar's **Report** button after picking, and the editor's **Report bug**. **Inference:** first-time users may not know which one to use or what they lose by skipping the editor.
7. **Fact:** Instant Replay is on by default on Home ("142 MB in memory"). **Unknown:** whether an Admin can switch it off or limit it, and whether always-on recording is acceptable to the security teams described in `CLAUDE.md`.

### Report
8. **Fact:** Reporting the checkout bug from the desktop created a **new issue (FIX-143) and a second Tazko card**, although FIX-142 (the same bug, 17 events) was open. The report form showed no "this looks like FIX-142" hint. In contrast, the Claude path ("/fixit:report") said it matched an open issue and added an occurrence instead. FIX-142's "Possible duplicates" afterwards still listed only FIX-134, not FIX-143. **Inference:** in today's journey the Triager's core pain (five people, five reports) still happens on the main reporter channel.
11. **Fact:** Besides the one sentence, the form also shows Expected, Actual, How often (Always, Sometimes or Once) and Severity. **Unknown:** which fields are required. **Inference:** if reporters feel they must fill them all, the "one sentence" promise weakens.

### Triage
12. **Fact:** Duplicates are suggested with a similarity score (92 %), but merging is a manual click per pair. **Inference:** this is better than today's merging by hand, but a triager still does the work.
13. **Fact:** Tazko cards can be created in three ways: "Send to Tazko" on the desktop report (on by default), "Create cards automatically for severity High and above" in the connector, and "Create Tazko card" on the issue. **Inference:** the same issue can produce several cards, and FIX-142 and FIX-143 both landed in Triage.
14. **Fact:** The console sidebar has 21 items, including Health, Speed, Traces and logs, Monitors, Tests and Usage. **Inference:** the console is shaped more like a full observability product than a bug inbox. That raises both the learning cost for a triager and the build scope for the 31 Mar 2027 date.

### Fix
15. **Fact:** The Tazko card for **FIX-138** (a TypeError on /orders) shows the repro steps of the **3DS checkout bug** (FIX-142) and a "Replay 0:47" link. The console calls the same card "Orders list crash" while Tazko calls it "[FIX-138] TypeError in OrdersList". **Inference:** if AI-written repro steps can attach to the wrong issue, the fixer is misled. This goes straight to the "AI output verified" criterion.

### Close the loop
17. **Fact:** The reporter banner asks about **FIX-137** ("Export CSV downloads an empty file"), but "Yes, fixed" answers "Thanks. **FIX-142** is closed." **Inference:** the most visible part of "close the loop" for the reporter points at the wrong issue.
18. **Fact:** "Card done" in Tazko sets the issue to **Resolved** right away, while the fix check is "Queued, runs when the next build is uploaded". **Inference:** issues show as resolved before the fix has shipped or been verified, so the reporter may be told it's fixed too early.
19. **Fact:** Because FIX-143 wasn't merged, its reporter isn't connected to FIX-142's resolution. **Inference:** this is the "nobody tells the reporter" pain again, caused by item 8.
20. **Unknown:** what "Still broken" does (reopen, new occurrence, or notify the assignee?). Also unknown: how reporters who use the SDK, Claude or the mobile app are told, given that the confirm prompt was only seen on Desktop Home.

### Admin and security
21. **Fact:** "Written summaries and steps… Uses your own AI key", and the Claude plugin runs "on each person's own Claude plan". **Inference:** the AI features send data to an outside model, which conflicts with "Security teams block cloud tools". **Unknown:** whether a fully on-premises install can use the AI features at all.
22. **Fact:** Share links offer "Anyone with the link", with a 29-day expiry. **Unknown:** whether an Admin can forbid public links.

### Scope against the constraint
23. **Fact:** `CLAUDE.md` names three surfaces for 31 Mar 2027: Web Landing Page, Web Console and Desktop App. The prototype also includes SDKs (web and iOS), Claude in Chrome and a Tazko mock. The Feature map says "The order is a proposal, not a decision". **Inference:** the scope for the deadline isn't settled yet.

---

## 5. Questions answered from the documents

| Question | Answer | Reasoning | Confidence |
|---|---|---|---|
| Is the "Good example" link the prototype to evaluate? | Yes, I treated it as the current prototype | Its title is "FixIt Prototype (Copy)", it labels itself "clickable prototype", and it covers the whole key journey | Medium. The label "Good example" could instead mean a model of the expected output |
| Who is Trung Ngo? | The Owner of the Acme Engineering org, which makes them the Buyer and Admin persona | The console avatar "TN" with the role "Owner · acme-eng", plus audit log entries for invites, roles and org settings | Medium inside the prototype. Unknown outside it |
| What is Tazko? | The team's own task tracker, and FixIt's first-class destination | It has its own surface and design tokens, and the prototype's styling "follows Tazko's palette family". It also says "No Tazko changes needed. The connector uses the public API and webhooks" | Medium. Likely an in-house sibling product |
| Which platforms come first? | Windows and macOS desktop (Tauri 2). Web and iOS SDKs. Linux and Android later | Text on the landing page and the SDK pages | High |
| What does "two seconds" mean in practice? | Capture takes about two seconds; filing a report takes 4 interactions plus one sentence | The hotkey selects the widget instantly, while the report form still needs a sentence and Send | Medium |
| Is the data kept on the org's server? | Yes, for captures, replays and issues. AI features and the Claude plugin are the exception | "Stored on your own FixIt server". "Nothing leaves this computer until you share or report". "Uses your own AI key" | High for storage. Open for AI |
| Does the loop close automatically? | Partly: Tazko done → Resolved → re-check on next build → reporter asked | Seen in the t-flow, issue and Claude verify screens | High, with friction items 17–20 |
| What is the build scope for 31 Mar 2027? | Landing, Console and Desktop | The constraint lists exactly these three | Medium. The extra surfaces in the prototype make this uncertain |
| Which step of Shaping is this? | Step 1, understand | The folder is called `01-understand` | Medium. `CLAUDE.md` still says `[1–5]` |

### Open unknowns to take to the team
- Is there research, data or interviews behind the personas' pains? None are in the repo.
- What are the real role and goals of Trung Ngo?
- Are SDKs, the Claude plugin and observability (Health, Speed, Traces, Monitors) in scope for 31 Mar 2027?
- How are reporters with no FixIt account, or with only an SDK, told about fixes?
- Is any AI processing allowed for orgs that block cloud tools?
- Where is `templates/01-current-state-notes.md`?
