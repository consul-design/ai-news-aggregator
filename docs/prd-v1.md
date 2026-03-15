# Product Requirements Document — v1
## AI News Aggregator

**Date:** March 15, 2026
**Status:** Ready to build

---

## What We're Building

A personal AI-powered news aggregator that lets you describe hyper-specific topics in plain English and delivers a daily email digest of the most relevant content from across the web — with AI-generated summaries for each article. A modern, automated successor to TabDump.

**One-liner:** "Describe what you care about. Get a daily email with only the articles that matter."

---

## The Problem

Staying current on niche professional topics is unreasonably hard. Google News covers broad categories but misses niche content. RSS readers require you to already know which feeds matter. Newsletters pile up unread. Social media buries signal in noise. There's no tool that lets you simply say "I care about open-source vector databases" and then reliably surfaces the 5-10 most relevant articles from that day — wherever they were published.

The people most affected are professionals who need to track specific domains: developers following specific technologies, analysts monitoring industries, researchers tracking subfields, founders watching competitors. They spend 1-2 hours daily piecing together updates from a dozen sources. Most of what they find is irrelevant. Much of what's relevant, they miss.

---

## MVP Scope (Build in 3-5 Days)

### 1. Natural Language Topic Definition

- Single text input: user describes their interest in plain language
- Examples: "open-source vector databases," "European AI regulation updates," "Rust programming language ecosystem"
- No feed management, no source selection — the user describes what they care about, not where to find it
- Start with 1-3 topics per user

### 2. Multi-Source Content Ingestion

- Pull articles from a configurable set of RSS feeds (start with 15-20 quality tech/news feeds)
- Pull from one news API (GNews or NewsAPI.ai) for broader coverage
- Parse each article: title, URL, publication date, source name, first 500 words or available summary
- Run ingestion on a schedule (every 6 hours)

### 3. AI-Powered Relevance Scoring

- For each article, score relevance against each user topic using Claude API
- Two-pass approach:
  - **Pass 1 (cheap):** Embedding similarity for rough filtering — reduce candidate pool from thousands to hundreds
  - **Pass 2 (accurate):** LLM scoring on filtered candidates — relevance score (0-10) + one-sentence explanation of why it's relevant
- Store scores in database for digest assembly
- Relevance threshold: only include articles scoring 7+ in the digest

### 4. Daily Email Digest

- One email per user per day at their configured time
- Contains top 5-10 articles across all their topics, ranked by relevance score
- Each article includes:
  - Title (linked to original)
  - Source name and publication date
  - AI-generated one-sentence summary
  - Relevance tag (which topic it matched)
- Clean, scannable format — no images, no clutter, inspired by TabDump's minimalist list format
- Footer: link to manage topics/unsubscribe

### 5. Simple Settings Page (Web)

- **Topics:** Add, edit, or delete topic descriptions
- **Email address:** Where to send the digest
- **Digest time:** When to send (morning, afternoon, evening)
- **Frequency:** Daily or weekly
- No account system for MVP — use a magic link or simple token-based auth

### What's NOT in v1

- No native mobile app — email is the interface
- No user accounts with passwords — magic link auth only
- No social features (sharing, community, comments)
- No feedback loop or learning — raw relevance scoring, no implicit/explicit signal collection
- No real-time alerts — daily digest only
- No podcast/video transcription — text content only
- No newsletter ingestion — RSS + news API only
- No source authority ranking — all sources weighted equally
- No multi-language support — English content only
- No bias detection — relevance only, not perspective analysis

---

## How It Works

```
User visits settings page → describes topic in plain English →
Backend ingests articles from RSS feeds + news API (every 6 hours) →
Embedding pass filters to ~100 candidates per topic →
LLM scores candidates for precise relevance (0-10) →
Daily digest assembles top 5-10 articles across all topics →
Email sent at user's configured time →
User reads digest, clicks through to original articles
```

---

## User Personas

### Primary: The Niche Professional

**Profile:** Senior developer, startup founder, analyst, or researcher who needs to track specific technical or industry domains for their work.

**Behavior:** Currently cobbles together updates from Hacker News, Twitter, 5-10 RSS feeds, 3-4 newsletters, and occasional Google searches. Spends 60-90 minutes daily on this. Misses important developments regularly. Feels anxious about being out of the loop.

**What they'd use this for:** "Track 'AI agent frameworks' and 'vector database benchmarks' for me. Just tell me what happened today."

**Willingness to pay:** $7-12/month — already pays for tools like Feedly Pro, Readwise, or Superhuman.

### Secondary: The Curious Generalist

**Profile:** Tech-literate professional who follows broad interests but doesn't want to manage RSS feeds or newsletters.

**Behavior:** Reads Hacker News and Twitter casually. Subscribes to too many newsletters, reads 20% of them. Wishes someone would just tell them the important stuff.

**What they'd use this for:** "Track 'climate tech startups' and 'AI art tools' — I'm curious but I don't want to work at staying informed."

**Willingness to pay:** $5-8/month — price-sensitive, might convert from free tier.

---

## Tech Stack

| Component | Choice | Why |
|-----------|--------|-----|
| LLM | Claude API (Sonnet 4.6) | Best cost/quality ratio for classification and summarization |
| Embeddings | OpenAI text-embedding-3-small (or Voyage AI) | Cheap, fast first-pass filtering |
| Backend | Python + FastAPI | Fast to build, great async support, rich ecosystem for ML/AI |
| Database | PostgreSQL + pgvector | Single database for relational data + vector similarity search |
| Email | Resend or Amazon SES | Reliable delivery, good deliverability, simple API |
| RSS Parsing | feedparser (Python) | Battle-tested RSS/Atom parser |
| News API | GNews API (free tier) or NewsAPI.ai | Broader coverage beyond RSS feeds |
| Content Extraction | newspaper3k or trafilatura | Clean article text from URLs when needed |
| Scheduler | APScheduler or cron | Trigger ingestion + digest assembly |
| Settings UI | Simple HTML + HTMX, or minimal React page | One page, nothing fancy |
| Hosting | Railway or Fly.io | Simple deployment, good for Python backends |

---

## Key Decisions

**Why email, not an app?**
- Zero friction — no app to install, no new interface to learn
- Email is the original "daily digest" medium — it's where TabDump lived
- Forces simplicity — we can't hide behind a fancy UI, the content has to be good
- No push notification fatigue — users check email on their own schedule
- Dramatically reduces MVP scope — no frontend to build beyond a settings page

**Why embedding + LLM two-pass, not just LLM?**
- Scoring every article with an LLM call is expensive at scale (~$0.01-0.02 per article)
- Embedding similarity is ~100x cheaper and handles first-pass filtering well
- LLM scoring on a reduced candidate set gives high accuracy at manageable cost
- The two-pass approach scales: 10,000 articles → 500 candidates (embedding) → 10 digest items (LLM)

**Why not scrape the whole web?**
- RSS feeds + one news API covers 80%+ of the content that matters for tech/professional topics
- Web scraping is legally complex, technically fragile, and ethically questionable
- Starting with open sources (RSS is explicitly opt-in by publishers) is cleaner
- Can expand source coverage later without changing the core architecture

**Pricing model (TBD by founder):**
- Suggest: $8/month or $69/year
- Free tier: 1 topic, weekly digest (not daily) — enough to prove value, constrained enough to drive conversion
- No ads — the product is attention-saving, ads are attention-stealing

---

## Success Criteria (Week 1 Post-Launch)

- 5-10 real users receiving daily digests
- 70%+ of digest articles are genuinely relevant to the stated topic (manual QA)
- At least 1 user reports discovering something they would have missed otherwise
- Email open rates above 50%
- No hallucinated or wildly inaccurate AI summaries
- Users say "this saves me time" (qualitative)
- Digest generation completes reliably every day without manual intervention

---

## Open Questions (to resolve before or during build)

1. **Topic refinement UX:** Should the settings page show example articles that match the topic definition, so users can calibrate their description before the first digest?
2. **Source transparency:** Should the digest show which source each article came from (beyond just the domain name)? Does "via HN discussion" or "via Ars Technica RSS" add trust?
3. **Quiet topic days:** What do we send when there's nothing new? Skip the email? Send a "nothing today" note? Include lower-relevance articles?
4. **Feedback mechanism:** Even without a full feedback loop, should we add a simple "thumbs up/down" per article in the email (via unique tracking URLs)? When do we add this?
5. **Rate limiting the LLM:** If we have 100 users with 3 topics each scoring 500 articles, that's 150K LLM calls/day. Budget implications? Do we need to batch more aggressively?
