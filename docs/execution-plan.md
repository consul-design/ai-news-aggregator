# Execution Plan
## AI News Aggregator — 5-Day MVP Build

**Date:** March 15, 2026

---

## Phase 1: MVP (Days 1–5)

### Day 1: Data Pipeline — Ingestion & Storage

**Goal:** Articles from RSS feeds and a news API are being pulled, parsed, and stored in the database on a schedule.

- [ ] Project scaffolding: FastAPI backend, PostgreSQL database, config management (environment variables), project structure
- [ ] Database schema: `users`, `topics`, `articles`, `article_scores` tables. Enable pgvector extension for embedding storage.
- [ ] RSS feed parser: Use feedparser to pull from 15-20 curated RSS feeds (Ars Technica, Hacker News, TechCrunch, The Verge, MIT Tech Review, etc.). Extract title, URL, published date, summary/description.
- [ ] News API integration: Connect GNews or NewsAPI.ai for broader article discovery. Pull top articles across tech/science/business categories.
- [ ] Content deduplication: Detect and skip duplicate articles across sources (URL normalization + title similarity)
- [ ] Ingestion scheduler: Run ingestion every 6 hours via APScheduler. Log results.
- [ ] Seed the database with 24-48 hours of articles for testing

**End of Day 1:** Running `python ingest.py` pulls hundreds of articles from RSS + news API and stores them in PostgreSQL. Scheduler runs automatically every 6 hours.

---

### Day 2: AI Scoring Pipeline

**Goal:** Given a topic description and a set of articles, the system scores relevance and generates summaries for top matches.

- [ ] Embedding generation: Generate embeddings for all ingested articles (title + summary text) using OpenAI text-embedding-3-small. Store vectors in pgvector.
- [ ] Topic embedding: When a user creates a topic, embed the topic description. Store in pgvector.
- [ ] First-pass filtering: Use pgvector cosine similarity to find the top ~100 articles most similar to each topic embedding. Fast, cheap, broad net.
- [ ] LLM relevance scoring: Send each candidate article (title + summary) along with the topic description to Claude Sonnet 4.6. Get back: relevance score (0-10), one-sentence explanation, one-sentence article summary. Use batch API for cost savings.
- [ ] Score storage: Save all scores in `article_scores` table (article_id, topic_id, score, explanation, summary, scored_at)
- [ ] Scoring scheduler: Chain scoring to run after each ingestion cycle. Only score new articles that haven't been scored yet for each topic.
- [ ] Prompt engineering: Write and test the system prompt for relevance scoring. Optimize for precision (few false positives) over recall.

**End of Day 2:** Running `python score.py --topic "open-source vector databases"` scores all recent articles against that topic. Top results are genuinely relevant.

---

### Day 3: Digest Assembly & Email Delivery

**Goal:** The system assembles a daily digest from scored articles and sends it as a clean email.

- [ ] Digest assembly logic: For each user, query top-scoring articles (7+ score) across all their topics from the last 24 hours. Deduplicate. Rank by score. Cap at 10 items.
- [ ] Email template: Clean, plain-text-first HTML email. TabDump-inspired format:
  - Header: "Your AI News Digest — [Date]"
  - Per article: Title (linked), source name, publication date, AI summary (1 sentence), topic tag
  - Footer: "Manage topics" link, unsubscribe link
- [ ] Email delivery: Integrate Resend or SES. Send via API. Handle bounces/errors.
- [ ] Digest scheduler: Trigger digest assembly and email send at each user's configured time (default: 8am local)
- [ ] Empty digest handling: If fewer than 3 articles score above threshold, skip sending and log it. Don't send an empty or padded email.
- [ ] Send test digests to founder email. Evaluate quality manually.

**End of Day 3:** A real email arrives in the inbox with 5-10 relevant articles, each with an AI summary. It looks good and the content is relevant.

---

### Day 4: Settings Page & User Management

**Goal:** A user can sign up, define topics, configure their digest, and manage their subscription via a web page.

- [ ] Settings page UI: Single-page web app (HTMX + Jinja2 templates, or minimal React). Sections:
  - Topics: Add/edit/remove topic descriptions (text input). Show current topics with edit/delete.
  - Email: Set delivery email address
  - Digest time: Dropdown (morning 8am / noon 12pm / evening 6pm)
  - Frequency: Daily or weekly toggle
- [ ] Magic link auth: User enters email → receives a login link → clicking link creates a session token. No passwords.
- [ ] API endpoints: CRUD for topics, user settings update, digest preview
- [ ] Topic onboarding: When a user adds a new topic, immediately embed it and show 3-5 example articles from the existing database that would match. "Here's what your digest might look like."
- [ ] Basic input validation: Topic description length limits, email format validation, rate limiting on API endpoints
- [ ] Deploy settings page (same server as backend, or separate static host)

**End of Day 4:** A new user can visit the settings page, enter their email, describe 1-3 topics, pick a digest time, and see a preview of matching articles.

---

### Day 5: Integration, Deploy, Test

**Goal:** Ship it. Full end-to-end flow works. Real users can start receiving digests.

- [ ] End-to-end test: Sign up → add topics → wait for ingestion → scoring runs → digest assembled → email sent → open email → click through to articles. Verify entire flow.
- [ ] Error handling: Retry logic for RSS fetch failures, Claude API timeouts, email delivery failures. Graceful degradation (if one RSS feed is down, others still work).
- [ ] Prompt tuning: Review 3-5 real digests. Adjust relevance scoring prompt based on quality of results. Tighten or loosen threshold.
- [ ] Deploy backend to Railway or Fly.io. Set up environment variables, database connection, scheduled tasks.
- [ ] Email deliverability setup: Configure SPF, DKIM, DMARC for sending domain. Verify email delivery to Gmail, Outlook, Apple Mail.
- [ ] Monitoring: Basic health checks — is the ingestion running? Is scoring completing? Are emails being sent? Alert on failures (simple log monitoring or Sentry).
- [ ] Recruit 5-10 alpha users from founder's network. Set up their topics. Monitor first real digests.
- [ ] Document: Brief README with setup instructions, environment variables, how to add RSS feeds, how to manually trigger ingestion/scoring/digest.

**End of Day 5:** Real users are receiving daily email digests with AI-scored, relevant articles about their specific topics.

---

## Phase 2: Learn & Iterate (Weeks 2-4)

After MVP validates, based on user feedback:

- **Feedback collection:** Add thumbs up/down per article in digest email (via tracking URLs). Use feedback to tune relevance thresholds and scoring prompts.
- **Source expansion:** Add more RSS feeds based on user topics. Integrate a second news API for broader coverage. Start pulling from Hacker News API (comments + upvotes as quality signals).
- **Digest quality dashboard:** Internal tool showing scoring distributions, user engagement (email opens, link clicks), feedback signals. Identify patterns in what works and what doesn't.
- **Topic refinement:** Let users add "positive examples" (articles they liked) and "negative examples" (articles they didn't want) to calibrate their topics beyond natural language description.
- **Multiple digest formats:** Add a web-based digest view (not just email) for users who prefer reading in-browser. Share links to individual digests.
- **Weekly summary:** For weekly digest users, generate a "this week in [topic]" narrative summary instead of just listing articles.

---

## Phase 3: Scale & Differentiate (Months 2-3)

If the core loop works (users engage, digests surface valuable content, retention is strong):

- **Newsletter ingestion:** Let users forward newsletters to a dedicated email address. Parse newsletter content and include it in the scoring pipeline.
- **Social signal integration:** Pull from Bluesky firehose and Reddit API. Surface trending discussions relevant to user topics. Solve the signal-to-noise problem.
- **Source authority scoring:** Weight sources by quality tier (established publications > personal blogs > social media). Let users adjust source preferences.
- **Real-time alerts:** For fast-moving topics (security vulnerabilities, breaking news), offer instant notifications when a high-relevance article is detected. Push notification or SMS.
- **Multi-language content:** Translate and score non-English articles relevant to user topics (EU publications, Japanese tech blogs, etc.).
- **Team features:** Shared topics, team digests, internal annotation and discussion on articles. Enterprise pricing.
- **Podcast transcription:** Transcribe podcast episodes, score segments for topic relevance, surface relevant clips with timestamps.
- **API access:** Expose the topic-scoring engine as an API for developers building their own tools.
- **Browser extension:** Surface relevance scores on articles as users browse the web. "This is 9/10 relevant to your 'AI agents' topic."

---

## Agent Task Breakdown

For each day, here's how the work breaks down into discrete agent tasks:

### Day 1 Tasks
1. **Scaffold project** — Create FastAPI project structure, requirements.txt, config module, .env template
2. **Design and create database schema** — PostgreSQL tables with pgvector extension, migration scripts
3. **Build RSS ingestion module** — feedparser integration, article parsing, deduplication logic
4. **Build news API ingestion module** — GNews/NewsAPI.ai client, article normalization
5. **Build ingestion scheduler** — APScheduler setup, 6-hour cycle, logging
6. **Curate initial RSS feed list** — Research and select 15-20 high-quality feeds across tech, science, business

### Day 2 Tasks
7. **Build embedding pipeline** — OpenAI embedding API integration, batch embedding generation, pgvector storage
8. **Build topic embedding module** — Embed user topic descriptions, store and update vectors
9. **Build first-pass filter** — pgvector cosine similarity query, return top-N candidates per topic
10. **Build LLM scoring module** — Claude API integration, relevance scoring prompt, batch processing
11. **Design and test scoring prompt** — Iterate on system prompt for relevance + summary quality
12. **Build scoring scheduler** — Chain to ingestion, score only new articles, idempotency

### Day 3 Tasks
13. **Build digest assembly module** — Query top scores, deduplicate, rank, cap at 10 items
14. **Design email template** — HTML + plain-text email, TabDump-inspired format
15. **Integrate email provider** — Resend or SES setup, send API, error handling
16. **Build digest scheduler** — Per-user send times, empty digest handling
17. **Manual QA** — Send test digests, evaluate relevance and summary quality

### Day 4 Tasks
18. **Build settings page UI** — Topic management, email config, digest time, frequency
19. **Build magic link auth** — Email-based login flow, session tokens
20. **Build API endpoints** — CRUD for topics, user settings, digest preview
21. **Build topic onboarding preview** — Show example matches when user creates a topic

### Day 5 Tasks
22. **End-to-end integration test** — Full flow from signup to email delivery
23. **Error handling and retry logic** — Graceful degradation across all external dependencies
24. **Deploy to production** — Railway/Fly.io, environment config, database provisioning
25. **Email deliverability setup** — SPF, DKIM, DMARC, delivery testing across providers
26. **Set up monitoring** — Health checks, failure alerts
27. **Alpha user onboarding** — Set up 5-10 users, configure topics, monitor first digests
