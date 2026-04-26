# Protokol

> **"Read less. Do more. Change faster."**

---

## The Problem

People consume knowledge (books, articles, courses) but behavior doesn't change. The gap between **knowing** and **doing** is where growth dies. A person reads *Atomic Habits*, highlights 40 passages, feels inspired for 3 days, and returns to the same patterns by Thursday.

The existing market offers two halves of a broken solution: **summary apps** (Blinkist, Headway, Shortform) that compress books into shorter reading — still passive consumption — and **habit trackers** (Habitica, HabitNow, Streaks) that track behaviors but don't know *what* to track or *why*. Nobody bridges the gap: extract the actionable core from a knowledge source and turn it into a time-bound daily protocol with built-in reflection.

**One sentence:** Protokol turns any book into a 30-day action plan you actually follow.

---

## Vision

Knowledge has a half-life. Within 48 hours of reading something, you forget ~70% of it (Ebbinghaus curve). The only knowledge that sticks is knowledge you **act on immediately and repeatedly**.

Protokol exists to close the gap between consumption and transformation. We believe one book, deeply applied for 30 days, beats 12 books passively skimmed.

**Big idea:** What if every book came with a 30-day operating manual — not a summary, but a protocol?

---

## Competitive Landscape

| App | What it does | Gap |
|---|---|---|
| **Mentorist** ($60/yr) | Extracts action steps from 1,000+ books. Daily micro-steps. 1.3M users. | Closest competitor. Steps are generic — not personalized to your context. No reflection/review loop. |
| **Headway** ($90/yr) | 15-min book summaries + flashcards + spaced repetition. 2,500+ titles. | Summarizes knowledge but doesn't convert it to daily actions. Consumption-first. |
| **Blinkist** ($120/yr) | Audio/text book summaries, 7,500+ titles. Largest library. | Pure consumption. No action layer at all. |
| **Shortform** ($168/yr) | Deep book guides with analysis + exercises. 1,100 titles. | Best depth, but exercises are scattered — no structured daily protocol. No tracking. |
| **Readwise** ($96/yr) | Surfaces highlights from books/articles via spaced repetition. | Review-only. Helps you *remember* but not *do*. |
| **Habitica** (free/freemium) | Gamified habit tracker (RPG mechanics). | Tracks habits but has no idea what habits to suggest or why. No knowledge input. |
| **Fabulous** ($60/yr) | Behavioral science-based routine builder. | Pre-built routines, not derived from books/knowledge the user chooses. |

**Our position:** We are NOT a summary app. We are NOT a habit tracker. We are the bridge between reading and doing — the **action compiler** for knowledge.

**Why this gap exists:** Summary apps make money per-title (more books = more revenue). They have no incentive to slow you down and say "stop reading, start doing." We do.

---

## Target User

**Primary:** Non-fiction readers who buy 5-15 books/year but apply less than 10% of what they read. Typically 22-35, knowledge workers, developers, founders, or ambitious professionals in growth markets (SEA, LATAM, India). They feel guilty about the gap between what they know and what they do.

**Secondary:** Coaches, managers, or mentors who assign books to teams/mentees and want to verify applied learning, not just reading.

---

## MVP Features (The Essential List)

The following features are the absolute minimum for a working v1. Everything else is cut.

### 1. Protocol Library (Pre-built)

Launch with **20 hand-curated 30-day protocols** from the most popular non-fiction books. Each protocol contains:

- 30 daily actions (1 per day, ~5-15 min each)
- 4 weekly reflection prompts (Day 7, 14, 21, 28)
- A "micro-action" per day (the 2-minute version if you're short on time)

**No AI generation at v1.** Curated content is higher quality and cheaper to build. AI-generated protocols are a v2 feature after validating demand.

**Launch titles (20):** Atomic Habits, Deep Work, Essentialism, Thinking in Bets, Crucial Conversations, The Happiness Advantage, Finish, So Good They Can't Ignore You, The 7 Habits, Thinking Fast and Slow, Never Split the Difference, How to Win Friends, The Lean Startup, Show Your Work, Measure What Matters, The Psychology of Money, Can't Hurt Me, Mindset, Digital Minimalism, Range.

### 2. Daily Action Screen

The core interaction. User opens app → sees today's action → does it → marks complete. That's it.

**Fields per day:**
- Day number and book title
- Main action (2-4 sentences of what to do)
- Micro-action (the 2-minute fallback)
- Checkbox to mark complete
- Optional: one-line journal note

**No calendar view, no analytics dashboard, no streak counter at v1.** The daily screen IS the product.

### 3. Weekly Review

Every 7th day, the action is replaced with 3-4 reflection prompts. User writes free-text responses. Saved locally.

This is the retention mechanism. Without reflection, action plans become mindless checklists.

### 4. Protocol Progress

A single progress bar showing X/30 days completed. Simple, visible, motivating.

### 5. Auth + Data Persistence

User creates an account (email + password or Google OAuth). Progress syncs across devices. That's the only reason we need auth.

---

## Features Explicitly Cut from v1

| Feature | Why cut |
|---|---|
| AI-generated protocols from any book | Expensive (API costs), quality risk, and we haven't validated the core loop yet |
| Social features / sharing | Adds complexity, unclear if users want this |
| Gamification (streaks, badges, XP) | Premature optimization — validate the core action loop first |
| Analytics dashboard | Users don't need charts, they need to DO the next action |
| Book summaries | We are not a summary app. The protocol assumes you've read the book (or are reading it alongside) |
| PDF/highlight import | Cool feature, but scope creep — doesn't validate the core hypothesis |
| Team/coach features | v2 after individual PMF |
| Mobile app (native) | PWA first. Native iOS/Android only after 1,000 active users |
| Multiple active protocols | v1 = one protocol at a time. Constraint is a feature (Essentialism!) |
| Custom protocol builder | v2 feature. v1 is pre-built only |
| Notification/reminder system | Browser push notifications only. No complex scheduling |
| Payment/subscription | v1 is FREE. Monetize after PMF. Don't optimize revenue before product-market fit |

---

## Tech Stack

**Constraint:** Cheap, fast to build, uses React + Go (developer's existing stack). Deploy for ~$0/month at low scale.

### Frontend
- **Vite + React + TypeScript** — fast dev server, instant HMR, small bundle
- **Tailwind CSS** — utility-first, no custom design system needed at v1
- **PWA** (service worker + manifest) — installable on phone without app store
- Deploy to **Cloudflare Pages** (free tier: unlimited sites, 500 builds/month)

### Backend
- **Go + Gin router** (lightweight, no framework bloat)
- Endpoints: auth, get-protocol, get-progress, save-progress, save-reflection
- Deploy to **render** free for deployment or**Railway** (free $5/month credit)

### Database
- **SQLite + Litestream** → replicated to Cloudflare R2 (or S3)
- Why SQLite: zero infrastructure, single-file DB, deployed on same Fly.io VM as Go backend
- Handles 10,000+ users easily before needing Postgres
- Alternative: **Turso** (managed SQLite edge DB, free tier: 500 DBs, 9GB storage)

### Auth
- **Go session-based auth** with bcrypt (simplest, no third-party dependency)
- Or **Lucia Auth** / **GoTrue** if you want OAuth quickly
- Google OAuth via direct implementation (no paid auth service needed)

### Content Storage
- Protocols stored as **JSON files in the repo** at v1
- No CMS, no admin panel. Edit JSON, push to git, redeploy
- Move to DB-backed CMS only when you have 50+ protocols


## Future Roadmap (Post-v1, in priority order)

1. **AI protocol generator** — paste a book's table of contents or your highlights, get a custom 30-day protocol (Claude API)
2. **User-submitted protocols** — community-built protocols with upvoting
3. **Protocol remix** — adjust duration (14-day, 60-day), intensity, focus area
4. **Import from Readwise** — pull highlights and generate actions from them
5. **Native mobile apps** — only after PWA proves the concept
6. **Coach/team mode** — assign protocols to teams, see completion rates
7. **Article/thesis support** — not just books, any knowledge source

---

## Database Schema (v1)

```sql
-- Users
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Active protocol per user (one at a time)
CREATE TABLE user_protocols (
  id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL REFERENCES users(id),
  protocol_id TEXT NOT NULL,
  started_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  current_day INTEGER DEFAULT 1,
  status TEXT DEFAULT 'active' -- active, completed, abandoned
);

-- Daily completion log
CREATE TABLE daily_progress (
  id TEXT PRIMARY KEY,
  user_protocol_id TEXT NOT NULL REFERENCES user_protocols(id),
  day_number INTEGER NOT NULL,
  completed BOOLEAN DEFAULT FALSE,
  note TEXT, -- optional one-line journal
  completed_at DATETIME,
  UNIQUE(user_protocol_id, day_number)
);

-- Weekly reflections
CREATE TABLE reflections (
  id TEXT PRIMARY KEY,
  user_protocol_id TEXT NOT NULL REFERENCES user_protocols(id),
  week_number INTEGER NOT NULL,
  content TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(user_protocol_id, week_number)
);
```

---

## API Endpoints (v1)

```
POST   /auth/register        — create account
POST   /auth/login           — get session token
POST   /auth/logout          — clear session

GET    /protocols             — list available protocols (id, title, book, description)
GET    /protocols/:id         — get full protocol (all 30 days)

POST   /me/protocols          — start a protocol
GET    /me/protocols/active   — get current active protocol + progress
PATCH  /me/progress/:day      — mark day complete, add note
POST   /me/reflections        — save weekly reflection
GET    /me/reflections        — get all reflections for active protocol
```

8 endpoints. That's the entire backend.

---

## Protocol JSON Format

```json
{
  "id": "essentialism",
  "title": "Essentialism: 30 Days of Less but Better",
  "book": "Essentialism by Greg McKeown",
  "description": "Stop over-committing. Learn to say no. Protect what matters.",
  "days": [
    {
      "day": 1,
      "type": "action",
      "action": "List every commitment you currently have. Don't filter. Include work, personal, social, side projects.",
      "micro": "Before saying yes to anything today, pause for 5 seconds.",
      "chapter_ref": "Chapters 1-3"
    },
    {
      "day": 7,
      "type": "review",
      "prompts": [
        "How many times did I say yes to something non-essential this week?",
        "What drove that — guilt, obligation, fear of missing out?",
        "What is ONE commitment I can drop next week?"
      ]
    }
  ]
}
```

---

## One-Page Summary

| | |
|---|---|
| **Product** | Protocol |
| **Motto** | Read less. Do more. Change faster. |
| **Problem** | People read books but don't change behavior |
| **Solution** | 30-day action protocols extracted from books |
| **MVP** | 20 protocols, daily action screen, progress tracking, weekly review |
| **Stack** | Vite+React / Go+Chi / SQLite / Cloudflare+Fly.io |
| **Timeline** | 6 weeks |
| **Budget** | $10-50 |
| **Monetization** | Free at launch → $36/year freemium after PMF |
| **Key metric** | Protocol completion rate (target: 15%+) |
| **Closest comp** | Mentorist (but we're protocol-first, not step-first) |
| **Differentiator** | We don't summarize books. We compile them into operating systems. |
