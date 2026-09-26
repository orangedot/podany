# Anypod

[![Live Web App](https://img.shields.io/badge/Live_App-anypod.org-f97316?style=for-the-badge&logo=cloudflare)](https://anypod.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)
[![Open Source](https://img.shields.io/badge/Open_Source-MIT-green?style=for-the-badge&logo=github)](https://github.com/orangedot/anypod)

> 🌐 **Live App:** [**anypod.org**](https://anypod.org) — no account needed to start listening.

**Your podcasts. No algorithms. No ads. No tracking. Complete sovereign sync.**

Anypod is a private, lightweight, open-source podcast player and self-hosted sync hub. Listen immediately in any modern browser without an account, install it as a standalone PWA, or sync seamlessly across your devices and native apps like **AntennaPod**.

---

## Why Anypod?

| Feature / Attribute | Anypod (Cloud / Edge) | Anypod (Self-Hosted / Docker) | AntennaPod | Spotify | Apple Podcasts | Pocket Casts |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **No Account Required** | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ |
| **Ad-Free & No Tracking** | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ |
| **Open Source** | ✅ (MIT) | ✅ (MIT) | ✅ (GPLv3) | ❌ | ❌ | ❌ |
| **Self-Hostable** | ✅ (Cloudflare) | ✅ (Docker + Local SQLite) | ❌ (Client Only) | ❌ | ❌ | ❌ |
| **Auto-Deletes Inactive Data** | ✅ (50–60 Days) | ✅ ("Cleaner Monday") | N/A (Local Only) | ❌ | ❌ | ❌ |
| **Cross-Platform PWA** | ✅ (Desktop/Mobile) | ✅ (LAN / Reverse Proxy) | ❌ (Android Only) | ✅ (Native) | ❌ (Apple Only) | ✅ (Web/App) |
| **AntennaPod / gPodder Sync** | ✅ | ✅ | ✅ (Client) | ❌ | ❌ | ❌ |
| **Voice Boost & Silence Skip** | ✅ (Web Audio DSP) | ✅ (Web Audio DSP) | ✅ (Native Sonic) | 🟡 (Speed Only) | 🟡 (Speed Only) | ✅ (Paid Tier) |
| **Unified Omnibar (`Cmd+K`)** | ✅ | ✅ | ❌ | 🟡 | 🟡 | 🟡 |
| **YouTube Feeds** | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |

---

## Key Features

### 🎙️ Advanced Audio Engine & Spoken-Word DSP
* **Dynamic Voice Boost:** Real-time Web Audio `DynamicsCompressorNode` lifts quiet voices and limits volume peaks without clipping.
* **Smart Silence Skipping:** Amplitude-aware RMS thresholding automatically accelerates pauses to 2.5x speed and instantly restores normal tempo when speech resumes.
* **Battery & Background Guard:** Automated CPU throttling via the Page Visibility API pauses canvas drawing when the screen is locked or tab is hidden, preventing mobile background kills.
* **System Lock-Screen MediaSession:** Native OS lock-screen controls, scrub position state, and Bluetooth headphone buttons.

### 🔍 Unified Omnibar (3-in-1 Search)
* **Single Query Entrypoint (`Cmd+K` / `Ctrl+K`):** Replaces separate search bars and modal popups.
* **Instant Local Filter:** Real-time filtering across your subscriptions, episode notes, and downloaded audio.
* **Worldwide Discovery:** Direct, debounced querying against the global podcast directory.
* **Direct RSS / Atom URL Paste:** Paste any feed or media URL directly into the bar to follow instantly.

### 📲 Cross-App Sync (AntennaPod / gPodder Compatible)
* **Built-in gPodder API (`/api/2/` & `/api/gpodder/`):** Connect native open-source mobile podcatchers like **AntennaPod** directly to your personal Anypod server to synchronize episode subscriptions and playback progress.
* **Cloudflare D1 / Local SQLite:** Serverless edge or local SQLite storage ensures instant synchronization with zero memory overhead.

### 🛡️ Privacy & Security by Design
* **Zero Third-Party SDKs:** No analytics scripts, trackers, or telemetry beacons.
* **Account Auto-Deletion:** Optional cloud accounts inactive for 50 days are notified and permanently wiped after 60 days.
* **SSRF & CSP Protection:** Edge proxy enforces strict IP filtering (blocking loopback, private subnets, and cloud metadata endpoints) and strict CSP headers.
* **Offline Audio Caching:** Progressive Web App service worker provides full `HTTP 206 Partial Content` support for offline scrubbing.

---

## Architecture & Project Structure

The project uses a modular vanilla ES2022 architecture bundled by `esbuild` for production:

```text
anypod/
├── functions/               # Cloudflare Pages Functions & API Routes
│   └── api/
│       ├── 2/               # gPodder API endpoint for AntennaPod sync
│       ├── gpodder/         # Alternate gPodder API route
│       ├── audio-proxy.js   # Range-forwarding, CORS-safe media proxy
│       ├── auth/            # Passwordless magic-link authentication
│       ├── sync/            # D1 synchronization (feeds, positions, favorites)
│       └── utils.js         # SSRF validator and network security helpers
├── public/                  # Static assets & PWA shell
│   ├── dist/                # Production build artifacts (esbuild output)
│   ├── _headers             # Security headers & Content Security Policy
│   ├── index.html           # Main HTML shell
│   └── sw.js                # Service Worker with HTTP 206 Range synthesis
├── src/                     # Modular Application Source Code
│   ├── audio/               # Audio engine, Web Audio live DSP & spectrum probe
│   ├── config/              # Constants, icons, and starter feeds
│   ├── state/               # Store state & circular-safe event bus
│   ├── sync/                # Client-side sync & auth orchestration
│   ├── ui/                  # DOM bindings, cards, timeline, feeds grid & omnibar
│   ├── utils/               # Time/date formatters, storage & URL helpers
│   └── main.js              # Application entry point
├── docker-compose.yml       # Docker compose setup with SQLite persistence
├── Dockerfile               # Container build definition
├── schema.sql               # SQLite / Cloudflare D1 schema definition
└── wrangler.json            # Cloudflare Pages & D1 binding configuration