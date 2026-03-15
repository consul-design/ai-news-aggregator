# AI News Aggregator

> **Status:** Idea
> **Created:** 2026-01-07
> **Last Research Update:** 2026-01-07

## One-Line Summary
A personalized, AI-powered news aggregator that delivers updates on hyper-specific topics from multiple sources, inspired by the now-defunct TabDump.

## The Idea (Cleaned Up)
Create a modern successor to TabDump—a beloved tech news aggregator that shut down around 2014. Unlike traditional RSS readers or news apps, this platform would use AI to let users define very specific topics of interest (not just "tech" but "open-source database migration tools" or "European AI regulation updates") and pull relevant content from across the web, newsletters, podcasts, social media, and other sources.

The key differentiator is specificity and cross-source aggregation. Users aren't just subscribing to feeds—they're defining topics at a granular level and having AI surface relevant information regardless of where it originates. Think of it as having a research assistant who reads everything and only tells you what matters for your specific interests.

## Research

### Similar Existing Projects
- **[Artifact](https://en.wikipedia.org/wiki/Artifact_(app))** (Acquired by Yahoo 2024) - "TikTok for text" by Instagram co-founders Kevin Systrom and Mike Krieger. Used AI for personalized recommendations. Shut down due to insufficient market opportunity, but technology acquired by Yahoo.
- **[Feedly + Leo AI](https://feedly.com)** - RSS reader with AI-powered filtering, topic tracking, and priority inbox. Closest existing product to this concept.
- **[Ground News](https://ground.news/)** - News aggregator focused on showing bias and coverage across sources for the same story.
- **Google News** - AI-curated news but lacks the hyper-specific topic definition capability.
- **Inoreader** - RSS reader with AI-powered filtering and rules.

### Relevant Technologies & Topics
- **LLM-based content classification** - For understanding article relevance to specific topics
- **Web scraping & content extraction** - Tools like Diffbot, Mercury Parser for clean article extraction
- **Embedding-based semantic search** - Vector databases for topic matching
- **RSS/Atom feeds** - Still the backbone of content aggregation
- **Newsletter parsing** - Email API integration for newsletter content

### Key People & Companies
- **Kevin Systrom & Mike Krieger** - Built Artifact, proved AI-first news aggregation has demand
- **Stefan Constantinescu** - Original TabDump creator
- **Edwin Khodabakchian** - Feedly CEO, pioneer in RSS + AI combination
- **Nuzzel (acquired by Twitter)** - Social news aggregation concept

### Recent Developments
- **2024-03** - Yahoo acquired Artifact's technology after the app announced shutdown
- **2024-01** - Artifact founders admitted market wasn't big enough for standalone app
- **2025** - Feedly Leo continues to evolve AI features for professional users
- **Ongoing** - Newsletter aggregators like Matter, Readwise Reader gaining traction

## MVP (Minimum Viable Product)

The simplest version: a single-user web app where you describe a topic in natural language ("updates on open-source vector databases"), and it sends you a daily email digest.

**Core experience:**
- One text box to describe your topic
- Backend pulls from 3-5 RSS feeds you manually add + a news API
- LLM scores each article against your topic description (simple relevance check)
- Daily email with the top 5-10 relevant articles, each with a one-sentence AI summary

**What to cut:**
- No account system (just your email + topic stored in a simple database)
- No fancy UI—could literally be a single HTML form
- No multi-source aggregation beyond RSS + one API
- No feedback loops or learning—just raw relevance scoring
- No mobile app

**The magic to prove:** Can an LLM reliably surface niche, specific content that generic news apps miss? If the daily email consistently surfaces things you'd otherwise have missed, the core idea works.

## Open Questions
- Should this be a standalone app or a browser extension that enhances existing reading?
- Subscription model vs. ad-supported vs. one-time purchase?
- How to handle paywalled content ethically?
- Focus on consumer market or professional/enterprise (threat intelligence, competitive analysis)?
- What made Artifact's market "not big enough" and how to avoid same fate?

## Raw Idea (Original)
> recreation of the old tabdump website but instead use ai and allow you to get updates on very specific topics across many different sources

