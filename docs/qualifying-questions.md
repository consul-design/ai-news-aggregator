# Qualifying Questions
## Questions to Answer Before Committing to Build

**Date:** March 15, 2026

These questions should be answered (or deliberately deferred) before investing significant engineering time. They're organized by category to facilitate focused conversations.

---

## User Behavior & Intent

1. **Topic articulation gap:** Users say they want "AI regulation updates," but do they actually mean EU AI Act enforcement, US executive orders, Chinese AI governance, or all of the above? How much work do we put into helping users define their topics precisely enough for the AI to deliver high-relevance results?

2. **Consumption pattern:** Are target users "inbox zero" types who'll read every digest item, or "scan and bookmark" types who want 20 items to skim? This fundamentally changes how many articles we surface and how we format the digest.

3. **Topic drift over time:** Professional interests shift — someone tracking "LLM fine-tuning" six months ago now cares about "AI agent frameworks." How does the product handle evolving interests? Automatic drift detection, or manual topic editing only?

4. **Discovery vs. monitoring:** Are users trying to *discover* new developments they didn't know about, or *monitor* known ongoing stories? These require different scoring approaches — novelty detection vs. keyword tracking.

5. **Trust calibration period:** How many digests does a user need to receive before they trust the AI's judgment? If the first digest has 2 irrelevant articles out of 10, do they churn? What's the acceptable error rate in week 1 vs. month 3?

6. **Multi-topic conflict:** If a user tracks 5 topics, and the same article is relevant to 3 of them, how do we handle deduplication? Separate digests per topic, or one unified digest with topic tags?

7. **The "I already saw that" problem:** Power users follow multiple sources. If they already read an article via Twitter or a newsletter, seeing it again in our digest feels like noise. Can we detect or learn what they've already seen?

---

## Content Sourcing & Quality

8. **Source authority vs. freshness:** A peer-reviewed paper and a random blog post might both discuss "vector database performance." How do we weight source authority in relevance scoring? Do we need a source quality tier system?

9. **Paywalled content ethics:** If the most relevant article on a topic is behind a paywall (NYT, The Information, Bloomberg), do we surface it with just the title? Summarize the free portion? Exclude it entirely? Users will be frustrated seeing links they can't read.

10. **Newsletter access:** Newsletters are increasingly where niche expertise lives (Substack, Beehiiv). But accessing newsletter content requires email subscriptions. Do we ask users to forward newsletters to us? Create proxy subscriptions? How do we handle this technically and legally?

11. **Non-English content:** For topics like "European AI regulation," the most relevant content may be in German, French, or EU institutional publications. Do we translate? Summarize? Ignore? What's the i18n strategy for content (not UI)?

12. **Social media signal quality:** Twitter/X, Bluesky, and Reddit threads often contain breaking insights before formal articles. But they're also full of noise, hot takes, and misinformation. How do we incorporate social signals without drowning in low-quality content?

13. **Podcast and video content:** A 90-minute podcast might contain 5 minutes relevant to a user's topic. Is it worth transcribing and scoring full podcast episodes? At what cost? Or do we limit to show notes and timestamps?

14. **Content freshness window:** For a daily digest, what's the lookback window? 24 hours? If an important article was published 3 days ago but we just discovered it, do we include it? How do we handle the "old but new to us" problem?

---

## AI Reliability & Scoring

15. **Relevance scoring calibration:** How do we define "relevant"? A 7/10 relevance article about vector databases might be a general overview — not useful to an expert, very useful to a beginner. Do we need to understand user expertise level, not just topic interest?

16. **False positive cost vs. false negative cost:** A false positive (irrelevant article in digest) wastes time. A false negative (missed relevant article) defeats the product's purpose. Which is worse for user trust? How do we calibrate the threshold?

17. **Hallucinated summaries:** If the AI summary of an article misrepresents the content, and the user shares that summary, we've created misinformation. What's our verification layer? Do we flag AI summaries as AI-generated?

18. **Embedding drift:** As language around a topic evolves (e.g., "large language models" → "foundation models" → "frontier models"), do our embeddings keep up? How often do we re-embed topic descriptions and article content?

19. **Adversarial content:** SEO spam farms and AI-generated junk articles are proliferating. If an article is keyword-stuffed for "vector databases" but has no real substance, can our scoring system detect and filter it? What's our spam/quality layer?

20. **Scoring transparency:** When a user sees a article in their digest, should they be able to see *why* the AI selected it? ("Matched your topic 'EU AI regulation' because it discusses the AI Act enforcement timeline") Would transparency build or undermine trust?

---

## Delivery Format & UX

21. **Email vs. web vs. app:** The MVP is an email digest, but is email actually where users want this? Email is saturated. Would a daily push notification linking to a web page perform better? A browser extension? An RSS feed of scored articles?

22. **Digest frequency:** Daily is the assumption, but some topics move fast (breaking security vulnerabilities) while others are slow (academic research). Should users be able to set per-topic frequency (real-time alerts, daily, weekly)?

23. **Digest length sweet spot:** Too few articles and the user feels it's not worth it. Too many and they don't read it. Is the sweet spot 5 items? 10? 20? Does it vary by topic velocity? Should we let users set this?

24. **Summary depth:** One-sentence summary? Three-sentence summary? Full article rewrite? What level of summary actually saves the user time without losing critical nuance? Does depth depend on topic complexity?

25. **Actionability:** Beyond "here's what happened," should the digest include context like "this is significant because..." or "you should care because..."? How much editorial intelligence should the AI layer provide?

26. **Read state and interaction:** If the digest is an email, we get zero interaction data (no clicks, no read time). How do we learn what's relevant without feedback? Do we need a web interface just for implicit feedback collection?

---

## Business Model & Economics

27. **Willingness to pay for niche monitoring:** Feedly Pro+ is $99/year, Inoreader Pro is $90/year. Would users pay $84-120/year for a tool that *only* does hyper-specific topic digests? Or does it need to be part of a larger reading suite?

28. **Unit economics at scale:** If each user has 3 topics and we score 500 articles/day per topic, LLM API costs could be $5-15/user/month. At a $10/month price point, is there margin? How aggressively do we need to optimize the scoring pipeline?

29. **Free tier conversion math:** If we offer a free tier (1 topic, weekly digest), what conversion rate to paid do we need? If it's under 5% (typical for prosumer tools), do we need 100K+ free users to build a business? Is that realistic?

30. **B2B vs. B2C trajectory:** Individual professionals might pay $10/month. Enterprise teams (competitive intelligence, security threat monitoring, M&A research) might pay $50-200/seat/month. Does chasing B2B change what we build in the MVP?

31. **API as product:** Could the topic-scoring engine itself be the product? "Send us articles and a topic description, we'll score relevance" — a building block for other apps. Does an API business make more sense than a consumer digest?

---

## Technical Risk

32. **Rate limiting and source bans:** If we aggressively crawl news sites for content, we risk being rate-limited or blocked. What's our crawling strategy? Respectful intervals? Proxy rotation? Or do we rely entirely on RSS feeds and APIs?

33. **LLM provider dependency:** If we build on Claude and Anthropic has an outage, downtime, or price increase, our product stops working. Do we need multi-model support from day one? Or is that premature optimization?

34. **Email deliverability:** Daily digest emails can trigger spam filters, especially if they contain lots of links. What's our sender reputation strategy? Do we need a dedicated IP? SPF/DKIM/DMARC setup? Deliverability monitoring?

35. **Latency for real-time topics:** If a user tracks "zero-day vulnerabilities," they can't wait for a daily digest. Can we add real-time alerts without fundamentally redesigning the architecture? Or do we explicitly declare "this is a daily tool, not a real-time tool"?

36. **Data storage growth:** Every scored article, every generated summary, every user interaction accumulates. What's the data retention policy? Do we keep scored articles forever (for retraining) or expire them?

---

## Competitive Positioning & Defensibility

37. **The Perplexity threat:** Perplexity Discover already does AI-curated news for 45M monthly users. If they add custom topic tracking and daily digest emails, they instantly become a better-funded version of our product. What's our response?

38. **The Feedly response:** If Feedly adds natural language topic definition to Leo, they have 15 years of RSS infrastructure, enterprise customers, and AI capabilities. What would we have that Feedly can't replicate?

39. **Open source risk:** Could someone build this with open-source LLMs, pgvector, and RSS parsing in a weekend hackathon? Is the product defensible, or is the technology so accessible that moats don't exist?

40. **Network effects or lack thereof:** This product has no inherent network effects — my digest doesn't get better because you use it too. Without network effects, what prevents churn? Personalization history? Habit formation? Switching costs?

---

## Edge Cases & Scope Boundaries

41. **Controversial topics:** What if a user's topic is politically charged ("gun control legislation updates" or "immigration policy changes")? How do we handle bias in source selection and AI summarization? Do we show bias awareness like Ground News?

42. **Misinformation topics:** What if a user defines a topic around a conspiracy theory ("5G health effects")? Do we surface debunking content? Refuse the topic? Treat it neutrally? Where's the content moderation line?

43. **Competitive intelligence misuse:** Someone could use the tool to obsessively monitor a competitor or individual. Is this a feature (competitive intel) or a misuse (stalking)? Where's the line? Do we need usage policies?

44. **Zero-result days:** Some niche topics won't have new content every day. Sending an empty digest feels broken. Sending old content feels padded. What do we do on quiet days? Skip the digest? Send a "nothing new today" note?

45. **Topic overlap and redundancy:** If two users track nearly identical topics, should we be scoring articles twice? Can we cluster similar topics for efficiency? Does this create privacy concerns (inferring what others are tracking)?
