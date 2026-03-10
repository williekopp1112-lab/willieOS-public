# Gary — Technical Installation Guide

**Date:** March 9, 2026
**Version:** 0.2.0
**Audience:** Developers familiar with CLI, Node.js, and Rust toolchains

---

## System Requirements

| Requirement | Version |
|---|---|
| Node.js | 18+ |
| Rust | 1.77.2+ |
| macOS | 10.13+ (primary) |
| Linux | Ubuntu 22.04+ (secondary) |
| Tauri CLI | 2.x |

**macOS only:** Xcode Command Line Tools (`xcode-select --install`)

**Linux only:** Install WebKit and system deps first:
```bash
sudo apt install libwebkit2gtk-4.1-dev libssl-dev libayatana-appindicator3-dev
```

---

## 1. Install Prerequisites

```bash
# Install Rust (if not already installed)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update stable

# Install Tauri CLI globally
cargo install tauri-cli --version "^2"

# Verify
node --version    # should be v18+
rustc --version   # should be 1.77.2+
tauri --version   # should be 2.x
```

---

## 2. Clone & Install

```bash
# Clone the repo
git clone https://github.com/williekopp1112-lab/willieOS.git
cd willieOS

# Install frontend dependencies
npm install

# Install MCP server dependencies
cd willieos-mcp-server
npm install
cd ..
```

---

## 3. Environment Setup

Create `.env.local` in the project root:

```bash
# Suppresses the daily check-in popup during development
VITE_DISABLE_CHECKIN=true
```

> **Note:** All API keys are configured at runtime via the Settings UI — they are stored in SQLite, not in `.env` files. You'll configure them after first launch (see Step 5).

---

## 4. Run in Development

```bash
# Start Tauri dev server (launches Vite + native window with hot reload)
npx tauri dev
```

The app window opens automatically. First run may take 2–5 minutes while Rust compiles.

---

## 5. Configure API Keys (First Launch)

Open **Settings → AI** and enter at minimum:

- **Anthropic API key** — required for all AI features

Optional integrations (configure as needed):

| Integration | Setting key | Where to get it |
|---|---|---|
| Google OAuth (Calendar + Gmail) | `google.clientId` / `google.clientSecret` | Google Cloud Console |
| OpenAI (Whisper voice) | `openai.whisperKey` | platform.openai.com |
| OpenRouter (background models) | `ai.openrouterKey` | openrouter.ai |
| Brave Search | `search.braveApiKey` | api.search.brave.com |
| Telegram bot | `telegram.botToken` | @BotFather on Telegram |

---

## 6. Build for Production (macOS .app)

```bash
# Build release binary — use global CLI, NOT npm run tauri build
tauri build

# Install to /Applications
open src-tauri/target/release/bundle/dmg/
# Drag Gary.app to /Applications
```

---

## 7. Verification

```bash
# Confirm dev server starts without errors
npx tauri dev
# Expected: Tauri window opens, no Rust compile errors

# Lint check
npm run lint
# Expected: no errors

# Verify DB path exists after first launch
ls "~/Library/Application Support/com.willieos.app/willieos.db"
# Expected: file exists
```

In the app, open **Settings → AI**, enter your Anthropic key, then send a message in the AI Assistant. A response confirms the full stack is working.

---

## 8. Troubleshooting

**`sqlx migration mismatch` crash on startup**
> The installed `.app` is older than the DB schema. Rebuild: `tauri build`, then replace the app in `/Applications`.

**Tauri window doesn't open / blank screen**
> Vite may not be ready yet. Wait for `"Local: http://localhost:5173"` in terminal output before the window loads.

**`better-sqlite3` install fails (MCP server)**
> Requires a C++ build toolchain. On macOS: `xcode-select --install`. On Linux: `sudo apt install build-essential`.

**`tauri: command not found`**
> Use `npx tauri dev` instead, or add `~/.cargo/bin` to your `PATH`: `export PATH="$HOME/.cargo/bin:$PATH"`.

**Google OAuth fails**
> Ensure `http://localhost` is listed as an authorized redirect URI in Google Cloud Console for your OAuth client.

---

*Gary v0.2.0 — [github.com/williekopp1112-lab/willieOS](https://github.com/williekopp1112-lab/willieOS)*
