# Walkthrough: UMGN Personal AI Chatbot Web Application

The **UMGN** personal AI chatbot web application is complete and verified. It is built with Next.js 14, TypeScript, Tailwind CSS, Prisma ORM, and JWT authentication with HttpOnly cookies.

---

## 1. What Was Built

### A. ChatGPT-Style User Interface
- **Responsive Layout**: Designed for seamless usage across both smartphones (with slide-out drawer) and laptops/desktops (with collapsible sidebar).
- **Sidebar & Chat History**:
  - "New chat" button and chronological grouping: *Today*, *Yesterday*, *Previous 7 Days*, and *Older*.
  - Inline chat renaming and delete buttons.
  - User footer with account avatar, Settings modal trigger, and Logout.
- **Main Chat Viewport**:
  - Welcome hero with starter prompt cards (Coding, Explaining, Drafting, Database Optimization).
  - Clean message bubbles with ChatGPT-style aesthetics.
  - Markdown rendering with GitHub-flavored markdown (`remark-gfm`).
  - Code blocks with language badges and one-click "Copy code" feedback.
  - Live typewriter streaming with blinking cursor.
  - Message action buttons (Copy message, Regenerate).
  - Auto-expanding textarea with Enter-to-send and Shift+Enter for newlines.
  - "Stop generating" button while streaming.
- **Settings Modal**:
  - **General**: User profile overview and Dark/Light mode toggle.
  - **AI Model & Persona**: System prompt customization, model selector (Gemini 1.5 Flash/Pro, GPT-4o/Mini), and temperature slider.
  - **Data Controls (Privacy & GDPR Sovereignty)**:
    - *Download JSON Export*: Downloads full user data, chats, and messages.
    - *Clear All Chats*: Purges all chat history with confirmation.
    - *Delete Account*: Hard-deletes account, credentials, settings, and conversations.

---

### B. Server-Side Security & Cloud Architecture
- **Zero API Key Leakage**: LLM calls (`GEMINI_API_KEY`, `OPENAI_API_KEY`) run strictly server-side. No keys are ever transmitted to the frontend.
- **Graceful Fallback Engine**: If no external API key is supplied, UMGN runs an intelligent built-in streaming engine so the app works out-of-the-box.
- **Data Minimization**: Only `username`, `email`, and salted `passwordHash` (12 rounds of bcrypt) are stored. No trackers, telemetry, or unnecessary PII.
- **HttpOnly Cookies**: Session tokens use stateless `jose` JWTs in `HttpOnly`, `SameSite=Strict`, `Secure` cookies.
- **Rate Limiting**: In-memory sliding-window limiter on `/api/auth/*` and `/api/chat/stream`.
- **Security Headers**: HSTS, Content-Security-Policy, X-Frame-Options (`DENY`), and X-Content-Type-Options (`nosniff`).

---

### C. Database Architecture (Prisma ORM)
- Single-file SQLite database (`prisma/umgn.db`) for personal/team use (10–50 users).
- Multi-user relational schema with cascade deletes:
  - `User` ⟷ `UserSettings` (1-to-1)
  - `User` ⟷ `Chat` (1-to-many)
  - `Chat` ⟷ `Message` (1-to-many)
- Easily scalable to cloud PostgreSQL (AWS RDS, Supabase, Neon) by changing the provider in `schema.prisma`.

---

### D. Production Deployment & Automated HTTPS
- **Multi-Stage Dockerfile**: Builds optimized standalone production Next.js runner.
- **docker-compose.yml with Caddy**: Automatically provisions and renews Let's Encrypt SSL/TLS certificates for HTTPS on any cloud instance.
- **[DEPLOYMENT.md](file:///c:/Users/LENOVO/Documents/ud/DEPLOYMENT.md)**: Step-by-step production hosting and scaling instructions.

---

## 2. Verification & Automated Test Results

An automated end-to-end integration test suite was run against the live UMGN backend (`scripts/test-api.mjs`).

```
=== Starting UMGN Automated Integration Tests ===

[1/7] Testing Registration...
✓ Registration succeeded: user_1789283310331

[2/7] Testing Session Verification (/api/auth/me)...
✓ Session verified for user ID: cmtzh32im0000n0ebvhsb6pyl

[3/7] Testing Chat Creation (/api/chats)...
✓ Chat created with ID: cmtzh32ky0003n0ebtmo641tn

[4/7] Testing Chat Streaming (/api/chat/stream)...
✓ Received streamed response (381 bytes):
Preview: "Hello! I am **UMGN**, your personal AI assistant.  I am running securely with server-side isolation,..."

[5/7] Testing Data Export (/api/user/export)...
✓ Data exported successfully! Contained 1 chat(s) and 2 message(s).

[6/7] Testing Clear Chats (/api/user/chats)...
✓ Clear chats result: Cleared 1 chat(s) successfully.

[7/7] Testing Permanent Account Deletion (/api/user/account)...
✓ Account deletion result: Your account and all associated data have been permanently deleted.

=======================================================
🎉 ALL INTEGRATION TESTS PASSED SUCCESSFULLY! (7/7)
=======================================================
```

---

## 3. How to Run UMGN

### Development Mode
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

### Production Mode
```bash
npm run build
npm start
```

### Docker with Automatic HTTPS
```bash
docker-compose up -d --build
```
