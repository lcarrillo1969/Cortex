# Cortex: LLM External Brain

**Cortex** turns a raw, stateless language model into a **structured, persistent work environment** — an "external brain" that handles memory, context, behavior configuration, and saving, leaving the AI free to focus on reasoning.

Unlike a traditional chat, Cortex is built for **long and complex sessions**: it keeps the conversation context alive through automatic summaries, saves everything locally in your browser, and adapts to any purpose — storyteller, prompt generator, chatbot, technical discussion, image-prompt engineer, and more.

> 🔗 **Generator:** https://perchance.org/seven-cortex-gem-v5
> 📂 **Repository / Documentation:** https://github.com/lcarrillo1969/Cortex

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [The Interface](#2-the-interface)
3. [Agents & Absolute Premises (the heart of Cortex)](#3-agents--absolute-premises)
4. [IMAGINER: image generation & visual prompts](#4-imaginer-image-generation--visual-prompts)
5. [Memory management: the Thermometer & the Executive Summary](#5-memory-management)
6. [Sessions: saving, recovering & sharing](#6-sessions)
7. [Session Archive: the conversation exactly as it happened](#7-session-archive)
8. [Scratchpad (private)](#8-scratchpad-private)
9. [Settings (⚙️)](#9-settings)
10. [FAQ](#10-faq)
11. [Technical architecture (for developers)](#11-technical-architecture)

---

## 1. Getting Started

Open the generator. **You don't need to configure anything** to start: Cortex ships with a default agent — a direct, helpful general-purpose assistant.

1. Type your message in the input box at the bottom.
2. Hit **Send** (or press `Enter`). Use `Shift + Enter` for line breaks.
3. The moment you send your first message, a **session** is created automatically (there's no Save button: everything autosaves).

Everything you type, every AI response, and every memory summary is saved automatically in your browser (IndexedDB). You can close the tab and pick up right where you left off.

---

## 2. The Interface

Cortex splits the screen into two functional areas:

### 🖥️ Main canvas (left)
The chat: message history, real-time streaming of responses, and the input box.

### 🧠 External Brain (right)
The modular control panel. From top to bottom:

| Module | Purpose |
|---|---|
| **Sessions** | New, import, conversation list, rename, export, share, delete |
| **1. Absolute Premises** | The "law" that defines who the AI is and how it responds |
| **1.5 IMAGINER** | The visual role that turns context into image prompts |
| **2. Executive Summary** | The compressed memory of the conversation (editable) |
| **Session Archive** | A faithful, numbered copy of every response, exactly as generated |
| **3. Scratchpad (Private)** | Personal notes that are **never** sent to the AI |

### 📐 Retractable panel
The **▸ / ◂** button on the top-right edge collapses or expands the External Brain so the main canvas takes the full width. The state is remembered between visits.

### Top bar
- **Memory Thermometer** — real-time context usage.
- **📖 Docs** — opens this very documentation (fetched live from GitHub).
- **⚙️ Settings** — system configuration.

---

## 3. Agents & Absolute Premises

### What is an Agent?

An **Agent** is a saved behavior configuration: a set of rules (the *Absolute Premise*) that tells the AI who it is, how it thinks, and in what format it responds. When you save a premise as a template, you're creating a **reusable agent**.

In Cortex the correct term is **Agent**: each saved template is a "specialist" you can summon with one click. The app shows the active agent's name next to the module titles *1. Absolute Premises* and *1.5 IMAGINER* (by default: `Cortex` and `IMAGINER`; if you edit the text without saving it as a template, it shows as `Custom`).

### The Absolute Premise is unbreakable law

Whatever you write in the *1. Absolute Premises* box is the model's highest-priority directive: it's read **first, on every turn**. Define roles, strict formatting, tone, absolute constraints, etc.

Changes are **immediate**: on every `Send`, Cortex reads the box at that exact moment. You can switch personalities mid-conversation without losing the session's memory — the AI remembers everything discussed but responds under the new rules.

### The Template Manager (🗂️) — your agent library

The **🗂️** button next to the module title opens the manager:

- **💾 Save current** — saves the current premise as a named agent.
- **Use** — loads a saved agent into the box instantly.
- **🔎 Search** and **All / Absolute Premises / IMAGINER** filters.
- **⬇️ Export** — downloads all your agents as JSON (backup / migration).
- **📂 Import** — imports a JSON file of agents (auto-detects whether the file contains sessions or templates).
- **✏️ Edit / 🗑️ Delete** — edit or delete a saved agent.

> **Workflow tip:** craft a premise, test it, and once you get the ideal behavior, save it. Over time you build an on-demand team of specialists: your strict translator, your prompt engineer, your novelist, your code reviewer… all one click away.

### New *while keeping* the agent

The **🔄** button next to the *Absolute Premises* title creates a new blank session that **keeps exactly** the current premise. Great for starting a new topic with the same agent without losing its personality. (The **✨ New** button in the Sessions module, in contrast, restores the default premise.)

---

## 4. IMAGINER: image generation & visual prompts

Cortex includes an image module for **testing prompts**, **building prompts from the conversation context**, and **generating images** — all without leaving the chat.

### The IMAGINER role (module 1.5)

It's a **parallel visual role**: its text defines how the chat context is turned into an image prompt (style, lighting, composition, prompt language, etc.). By default it's *"You are IMAGINER, a visual prompt creator…"*, and you can edit it to change the image style. Key points:

- It's a **parallel path**: it is not sent to the model during normal replies.
- It **doesn't touch** the history, the summary, or the Session Archive.
- It has its own template manager (🗂️) for saving multiple "visual styles".

### The 🎨 Image button

Next to the Send button. It opens the **image modal** using as its scene whatever you have typed (or, if empty, the last conversation message). The modal offers:

- **✨ Create from context** — the IMAGINER role + recent context + executive summary produce a new visual prompt (also regenerates the current one).
- **📝 From user prompt** — loads your text verbatim, skipping the IMAGINER transformation.
- **📋 Copy** — copies the prompt.
- **🎨 Generate** — generates 1–4 images.
- **Aspect ratio** — `1:1` (square), `▭ Horizontal`, `▯ Vertical`.
- **🗑️ Clear** — clears the results.

### Every image carries its own recipe

Clicking an image opens a **full-size viewer** with its metadata (prompt, negative prompt, seed, resolution and timestamp) as selectable/copyable text. When you **download** the image, that same metadata is embedded inside the PNG file (a `tEXt` `parameters` chunk — the standard read by tools like PNG Info / Stable Diffusion): the recipe travels with the image.

---

## 5. Memory management

### The problem
Language models have a strict limit on how many tokens they can remember at once. In Cortex the default budget is **4000 tokens**.

### The Thermometer
The top bar shows in real time how much of that active memory is occupied (green → yellow → red).

### The Executive Summary (automatic compression)
When the un-summarized history exceeds the threshold (default **1500 tokens**), Cortex silently calls the LLM to read the older messages and turn them into an **executive summary**, freeing up space. You'll see a system notice in the chat:

```
--- Previous history compressed into Executive Summary ---
```

Summaries stack up in the *2. Executive Summary* module, separated by `== RESUMEN ==`. On every `Send`, Cortex assembles the master prompt like this:

1. **Absolute Premises** (rules, always first).
2. **Executive Summary** (compressed memory, newest blocks first).
3. **Recent history** (the un-summarized messages).

If space runs out, the oldest summaries are discarded — never split in half, keeping the prompt stable.

### Manual editing & Force Sync
The summary is **editable**: if the AI missed a detail or summarized poorly, fix it by hand — your edits are injected into the next prompt. The **Force Sync** button forces an immediate synthesis of the pending history.

### Fine-tuning
In **⚙️ Settings** you can adjust the auto-summarize threshold, the synthesis input budget, and the minimum summary length.

---

## 6. Sessions

### Invisible autosaving
There is no "Save" button: the moment you send your first message a session is created, and from then on **everything autosaves** in your browser (IndexedDB): history, premises, IMAGINER, summary, scratchpad, and archive.

### The session list (right panel)
- **Automatic title** — the session is named after the first words of your first message (`[N] …`).
- **✏️ Rename** — give it a recognizable name ("Debate on AIs", "Weekend ideas").
- **✨ New** — a blank canvas for a new topic (your previous session stays saved in the list).
- **🔄** — a new blank session *keeping* the current agent (see section 3).
- **Click a session** — recover it instantly, with full context, as if no time had passed.
- **🗑️ Delete** — permanently deletes the selected session.

### 💾 Export a session
Downloads a full JSON: history, premises, IMAGINER, summary, scratchpad, and archive. Ideal for backups or migration.

### 📂 Import
Accepts both **sessions** and **agent templates** (auto-detected). Imported sessions are added as new — nothing is ever overwritten.

### 🔗 Share across devices
The **🔗 Share** button publishes the current session to a permanent link:

```
https://perchance.org/seven-cortex-gem-v5?import=<shareName>
```

Open that link on **any device** and the session is imported into that device's local database. The link is copied to your clipboard. Shared sessions never overwrite existing ones.

### Reopen last session
By default, reopening the generator reopens the last active session (can be disabled in ⚙️ Settings).

---

## 7. Session Archive

It's the **faithful historical record** of the conversation, distinct from the dynamic history:

- Stores a **numbered, sequential copy** of every AI response, tagged with the user question (`USER`) that triggered it — **exactly as it happened**, with no summaries or compression.
- **Not sent to the model** — it's a pure record.
- Use it to copy or export the full output of the session.

From the *Session Archive* module (**Show** button) you can:

- **📋 Copy all** — copy the entire conversation.
- **⬇️ Export all** — download the full conversation as plain text.
- **⬇️ Export responses only** — download **only the AI responses** (without the questions). Useful, for example, if you're a storyteller and want just the final narration, clean, with the original unsummarized text.

---

## 8. Scratchpad (Private)

Your private notebook inside the session. The text here is **never sent to the AI** nor does it enter the context. Use it for links, temporary ideas, or working notes. It autosaves with the session.

---

## 9. Settings

### General tab
- **Auto-summarize trigger (tokens)** — the threshold that triggers history compression (default 1500).
- **Synthesis input budget (tokens)** — the token budget for the synthesis call (default 5000).
- **Minimum summary length (chars)** — minimum length for accepting a new summary (default 20).
- **Reopen last session** — reopen the last session when opening the generator (default on).
- **Restore defaults** — back to default values.
- Local database usage statistics.

### Danger zone
- **Delete all sessions** — deletes all sessions (it first automatically downloads a backup of everything).
- **Full reset** — wipes the entire database and settings. You must type `RESET` to confirm.

---

## 10. FAQ

**Where is my data stored?**
Locally, in your browser (IndexedDB). It doesn't depend on external servers. The only server usage is the **Share** link (to transfer a session between devices) and, of course, the LLM calls and image generation.

**Does Cortex lose the thread in long conversations?**
No. The executive summary compresses the past and keeps things coherent. All systems have their token limit, but Cortex stretches it as far as it can.

**Can I change personality without losing context?**
Yes. Change the Absolute Premise and the next `Send` already uses the new rules; the session's memory is preserved.

**Is it good for stories?**
Yes. The Session Archive's *Export responses only* gives you the clean final narration, and IMAGINER generates the visual prompts for your scenes.

**Can I take my setup to another device?**
Export your **agents** (🗂️ → Export) and/or the **session** (💾 Export) as JSON and import them anywhere. Or use the **🔗 Share** link to move a whole session.

---

## 11. Technical architecture

Cortex is a Perchance generator. The code lives in `index.html` (a single IIFE application) and `main.pjs` (plugin imports and metadata).

### Persistence
- **IndexedDB** (`CortexDB`) — local database: `sessions` and `premiseTemplates`.
- **localStorage** — settings (`cortexSettings`), last active session, retractable-panel state.
- **Editable uploads** (upload-plugin) — publishing the **Share** link; each link is read via `?import=<name>` and imported as a new session.

### Perchance plugins used
- `ai-text-plugin` — the LLM engine (streaming chat, silent summary synthesis).
- `text-to-image-plugin` — IMAGINER image generation.
- `upload-plugin` — publishing Share links.
- `super-fetch-plugin` — network fallback for reading the README / Share links without CORS.

### Main modules (objects)
| Object | Responsibility |
|---|---|
| `State` | Global state (active session, history, token settings) |
| `DB_Service` | IndexedDB data layer |
| `Session_Manager` | Session lifecycle: create, list, rename, export, import, share |
| `Premise_Manager` | Agent template manager (Absolute Premises & IMAGINER) |
| `LLM_Service` | Model calls, streaming, inactivity watchdog |
| `Memory_Manager` | Thermometer, summary synthesis, context window |
| `Image_Handler` | IMAGINER: visual prompts, generation, PNG metadata |
| `UI_Handlers` | Chat rendering (markdown), archive, scroll, retractable panel |
| `Config_Manager` | User settings and persistence |

### How the master prompt is built (per turn)
```
1. Absolute Premises      ← rules, first and always
2. Executive Summary      ← compressed memory (newest blocks first)
3. Recent history         ← un-summarized messages
```
The budget is distributed with a "newest first" window: if it doesn't all fit, the oldest summary blocks are dropped — a message is never split in half.

### Safety / sanitization
- Model responses are rendered as **markdown** (`marked`), but all raw HTML is escaped — an instruction injected into the LLM's output doesn't execute.
- The **Scratchpad** and the **Session Archive** are never sent to the model.

### Live documentation
The **📖 Docs** button downloads this very README from `https://raw.githubusercontent.com/lcarrillo1969/Cortex/main/README.md` and renders it inside the app, so the repository documentation and the generator documentation always match.

---

*Cortex: an external brain that handles the logistics — storage, context, formatting, and token optimization — so the artificial intelligence can focus on reasoning.*
