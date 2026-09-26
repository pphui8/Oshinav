# Oshinav Requirements Research

## Executive conclusion

Yes, some people would use Oshinav, but probably not because they need another place to browse the same information they already see on X, TikTok, YouTube, Instagram, fan communities, or official accounts. They would use it if it reliably converts scattered announcements into something social platforms are bad at preserving:

> “What do I need to do, by when, for the activities I care about?”

There is evidence of demand for this job. Existing services already offer event discovery, fan calendars, screenshot-to-schedule extraction, ticket and task reminders, and shared fan information. This validates the problem but also means the current broad proposition is not differentiated enough.

The recommended product direction is an **import-first personal operations layer for fan activities**:

1. The user encounters an announcement in an existing community or official channel.
2. They share a URL, post, or screenshot with Oshinav.
3. Oshinav extracts the activity and its action deadlines.
4. The user confirms the details and status.
5. Oshinav reminds them about the next meaningful action.

Shared discovery can become a later multiplier, but it should not be the first assumption. The first proof should be whether people repeatedly import real announcements and rely on the resulting reminders.

This is a desk-research conclusion, not proof of product-market fit. The next step should be a small behavioral validation with real fans.

## Research questions

This report evaluates:

- Would a real fan use the proposed workflow?
- Is the need strong enough when social media already distributes the information?
- What alternatives already exist?
- Which requirements are essential for a credible first version?
- What should be tested before investing in broad crawling, recommendation, or a community database?

## What the existing requirements imply

The current requirements describe a shared activity database covering events, ticket sales, lotteries, preorders, payments, pickups, shipments, and releases. It combines:

- passive registration from URLs, X posts, screenshots, and manual entry;
- active discovery from followed works, creators, shops, and topics;
- duplicate matching across users and sources;
- personal status, tracking, and notifications.

This is a coherent system, but it currently combines three products:

| Product layer | User job | Main risk |
|---|---|---|
| Information discovery | Find announcements relevant to my interests | Social platforms and official channels are already strong here |
| Personal planning | Remember deadlines, payments, pickups, travel, and attendance | Strongest unmet utility; users can feel the value directly |
| Community knowledge base | Reuse structured information submitted by other fans | Potential network effect, but difficult cold start and moderation |

The first version should prove the personal-planning job. Discovery and community reuse should be designed so they can be added without being required for the first user to receive value.

## Evidence from adjacent products and communities

### The problem is already recognized

Eventernote has long positioned itself around voice-actor, idol, and artist event aggregation. It advertises broad event coverage, personalized recommendations, calendar synchronization, notifications for newly created events, and participant/community features. This suggests that fans do value a structured event layer outside a social feed. It also means event discovery alone is an established category, not an empty market. [Eventernote](https://www.eventernote.com/)

Oshinomi is especially close to the current concept. Its public site describes collecting schedules scattered across fan clubs, SNS, and official sites; importing from screenshots or URLs with AI; reminders for ticket applications and streaming; and automated monitoring of selected official sources. It reports more than 1,100 artists and 11,000 schedules. These are company-reported figures, so they should be treated as directional evidence rather than independently verified usage, but the existence and specificity of the product are strong competitive evidence. [Oshinomi](https://oshinomi.app/)

Other products show that users also want the operational details around an activity, not just an event title. The “推し活アラート” app turns screenshots into events and tasks, including ticket applications, payments, merchandise preorders, and preparation; its App Store listing describes a free tier and a paid tier for additional AI extraction. [推し活アラート on the App Store](https://apps.apple.com/jp/app/%E6%8E%A8%E3%81%97%E6%B4%BB%E3%82%A2%E3%83%A9%E3%83%BC%E3%83%88/id6788907357)

Sanrio’s “おしきゅん” also describes a community-collected schedule, task list, and notes. Grape focuses on live/ticket calendars, reminders, spending, and multiple artists. These examples indicate several validated use cases: collecting schedules, remembering actions, tracking tickets, and coordinating many interests. [おしきゅん](https://www.sanrio.co.jp/special/oshikyun/), [Grape](https://play.google.com/store/apps/details?hl=ja&id=jp.karabina.grape)

### Social media is a source and competitor, not a complete substitute

Social media has important advantages Oshinav should not try to replace:

- announcements appear where fans already spend time;
- fans receive social proof, reactions, translations, and interpretation;
- following an account is easier than configuring a new information service;
- communities often discover news faster than a centralized product.

However, a feed is optimized for recency and engagement, not durable task completion. It is weak at answering:

- Have I already entered this lottery?
- When does payment close in my timezone?
- Which of the five related deadlines matters next?
- Did this announcement update an activity I already saved?
- What did I order, and when can I pick it up?

The best positioning is therefore not “leave social media and read our feed.” It is “keep using your communities; send important announcements here so they become actionable and do not disappear.”

This is also consistent with recent survey evidence. In a 2026 Tanita survey of 1,000 respondents, “being short of time” was the second most common concern about fan activities at 19.4%, and “being swept around by information about one’s favorite” was fourth at 10.3%. The survey does not prove that an app would solve these problems, but it supports information overload and time pressure as relevant pains. [Tanita fan-activity survey](https://api-img.tanita.co.jp/files/user/news/pdf/2026/fanactivities_research.pdf)

Video Research reports that having a favorite is not limited to teenagers: 62.1% of its Gen Z respondents and 27.1% of its Gen X respondents reported having one. It describes fan activity as involving video media, SNS use, and purchasing behavior. This supports a broad potential audience, while also warning against designing only for a young, single-platform user. [Video Research](https://www.videor.co.jp/digestplus/article/consumer241118.html)

There is a counter-risk: a utility that adds more notifications can increase the same fatigue it claims to reduce. Research reported by Web担当者Forum found “too much information to keep up with” among reasons for fan fatigue. Oshinav must optimize for fewer, higher-confidence reminders rather than maximizing discovery volume. [Web担当者Forum summary of fan-fatigue research](https://webtan.impress.co.jp/n/2023/02/07/44254)

## Likely users and high-value situations

### Primary early adopter

The most promising early user is a multi-interest fan who regularly handles time-sensitive actions:

- follows several creators, franchises, or groups;
- uses X, official websites, ticketing services, shops, and streaming platforms;
- has missed or nearly missed an application, payment, preorder, or pickup deadline;
- currently uses screenshots, bookmarks, notes, calendar entries, or memory as a workaround;
- is willing to spend a few seconds confirming extracted information if the reminder is trustworthy.

This user has a recurring pain and can evaluate the product through observable behavior.

### Secondary users

- fans who attend events in multiple cities and need travel-related milestones;
- fans who manage physical merchandise, releases, shipments, and pickups;
- fans who are new to a creator or franchise and need a reliable timeline;
- fan groups coordinating attendance or sharing an announcement;
- users who want a quiet personal archive rather than public social interaction.

### Lower-priority users

- casual fans who follow one account and rarely act on deadlines;
- users who only want news or entertainment content;
- users who already have a well-maintained Google Calendar and do not manage many fan activities;
- users whose main need is fan conversation, where social platforms remain better.

## Jobs to be done

The central job is:

> When I see a fan-related announcement that may require action later, I want to capture the important dates and my current status quickly, so I can act without rereading feeds and official pages.

Supporting jobs:

1. **Capture:** “Save this screenshot/post without manually retyping everything.”
2. **Understand:** “Tell me what this is and which dates matter.”
3. **Decide:** “Tell me whether I need to apply, pay, pick up, attend, or do nothing.”
4. **Remember:** “Remind me early enough to act.”
5. **Review:** “Show my next deadlines and what I have already handled.”
6. **Trust:** “Let me inspect the source and correct mistakes.”
7. **Reuse:** “If another fan already structured this activity, let me use it without duplicating work.”

The sixth job is crucial. A wrong date or silently merged activity can be worse than no automation, especially for ticket lotteries and payment deadlines.

## Product hypotheses

These hypotheses should be tested in order:

| ID | Hypothesis | Evidence needed |
|---|---|---|
| H1 | Fans experience enough missed or nearly missed deadlines to seek a dedicated solution. | Interviews plus examples of recent missed/near-missed actions |
| H2 | Importing an announcement is more attractive than manually creating a calendar event. | Users choose import in a realistic task test and complete it |
| H3 | Users will confirm extracted details if the review is fast and transparent. | Completion rate, correction rate, and time to confirmation |
| H4 | A next-milestone view is more useful than a general news feed. | Users can answer “what should I do next?” without search |
| H5 | Users trust source-preserving reminders more than opaque AI-generated reminders. | Trust rating, source-opening behavior, and correction behavior |
| H6 | Shared structured activities reduce repeated work for at least one narrow community. | Multiple independent users save/reuse the same activity |
| H7 | Discovery notifications do not create fatigue when filtered by followed subjects and urgency. | Notification opt-outs, dismissals, and qualitative feedback |

H6 and H7 should not block learning H1–H5.

## Recommended MVP requirements

### Must have

- Import from a URL, screenshot, or pasted post text.
- Extract title, activity type, source, relevant dates, timezone state, work/topic, and required action when possible.
- Show an editable review screen before saving.
- Preserve the original source and extraction timestamp.
- Represent multiple milestones, especially application close, payment due, pickup, release, and event start.
- Let the user save a personal status such as interested, applied, paid, ordered, or attended.
- Show the next milestone prominently in a chronological list.
- Send configurable reminders for high-confidence milestones.
- Make uncertainty visible; never silently invent an exact time or silently merge a doubtful duplicate.
- Let the user correct, pause, or delete their personal tracking relationship.

### Should have

- Import from a share sheet or mobile “share to Oshinav” action.
- Duplicate suggestions based on official URL, ticketing URL, name, date, venue, and related subject.
- A minimal shared activity record so a second user can reuse a confirmed activity.
- Change history or “last verified” information for source updates.
- Calendar export after the internal workflow is reliable.

### Defer

- Broad crawling of every official site and social platform.
- Recommendation ranking and vector/RAG search.
- Public social profiles, follower counts, comments, or a general-purpose fan feed.
- Automatic status transitions that claim a user applied, paid, won, or attended.
- Complex spending analytics and travel planning.
- Full coverage claims across anime, games, VTubers, idols, and all pop-culture categories.

## Important requirement changes

### Reframe active discovery

Current requirements place active source monitoring near the center. It should be reframed as an optional enhancement after import reliability is proven. Source monitoring has high operational cost, stale-data risk, platform/API restrictions, and a difficult coverage promise. The product should initially support a small number of explicitly reliable sources or user-provided sources.

### Treat “action” as a first-class field

An activity title and date are not sufficient. The data model should capture, when known:

- action: apply, buy, pay, pick up, watch, attend, collect, or informational;
- action deadline;
- action URL;
- eligibility or conditions;
- confidence and missing-information state;
- last verified time;
- source excerpt or screenshot reference.

### Separate discovery confidence from reminder confidence

It may be reasonable to show a low-confidence candidate in Discover. It is not reasonable to schedule a high-consequence payment reminder from low-confidence extraction without confirmation. The notification system should require a confirmed milestone or clearly label an unverified reminder.

### Make source provenance visible

Every important milestone should show where it came from and when it was last checked. Shared data should not look authoritative merely because it exists in the database.

### Design for user-owned data

Users should be able to export or recreate their tracked activities in a standard calendar format. This reduces switching anxiety and is particularly important for a utility that users will trust with deadlines.

## Risks and mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| Existing products already solve the broad pitch | Oshinav becomes a less complete copy | Own the import-to-action workflow and source transparency for a narrow segment |
| Social platforms are faster and more engaging | Users do not open a second feed | Do not compete for browsing time; integrate with sharing and deliver utility notifications |
| Extraction errors | A missed payment or wrong lottery date damages trust | Confirmation, confidence, source display, conservative reminders, correction loop |
| Notification fatigue | The product becomes another source of anxiety | User-controlled urgency, deduplication, digest options, quiet defaults |
| Cold-start shared database | No reuse value at the beginning | Start with personal import; seed only one narrow category/community |
| Source/API/platform restrictions | Crawling may be brittle or disallowed | Prefer user-submitted URLs and permitted integrations; keep source adapters modular |
| Privacy and sensitive fan behavior | Screenshots may contain account/order details | Minimize retention, redact where possible, explain processing, offer deletion/export |
| Ambiguous workflows | “Interested” does not imply “applied” or “paid” | Keep status personal and user-controlled; avoid universal progress assumptions |

## Validation plan before building the full system

### Interviews: 8–12 people

Recruit a mix of multi-fandom fans, event attendees, merchandise buyers, and streaming/VTuber users. Ask for the last three announcements they acted on, not their abstract opinion of the idea. Request screenshots or describe the actual workaround they used, if they are comfortable.

Questions:

- Where did you first see the announcement?
- What did you need to do after seeing it?
- What did you save, and where?
- Have you missed a deadline or nearly missed one?
- Which dates were confusing?
- What would make you distrust an automated reminder?
- Would you share a post/screenshot into a tool? Why or why not?

### Concierge prototype: 10–20 users, two weeks

Do not begin with a crawler. Let users submit real URLs or screenshots through a simple form or prototype. Manually or semi-manually return structured activities and reminders. Measure behavior:

- number of real imports per user;
- percentage of imports that contain an actionable milestone;
- time from import to confirmation;
- correction rate and error categories;
- number of reminders opened or acted upon;
- repeat use after the first week;
- number of “I would have missed this” moments;
- whether users voluntarily submit a second activity without prompting.

### Strong early signal

Continue if at least several users repeatedly import real announcements, correct or confirm the extracted activity, and report that a reminder changed behavior or prevented a miss. A particularly strong signal is users asking for share-sheet support or adding more subjects without being prompted.

### Weak signal

Be cautious if users praise the concept but continue to use screenshots, bookmarks, or their existing calendar; if they only browse but do not track; or if reminders are routinely dismissed. Positive interview language without repeated behavior is not enough.

## Success criteria for the first product experiment

The experiment should aim to prove these outcomes, not just feature completion:

- A user can turn a real announcement into a confirmed activity in under one minute in common cases.
- The user can identify the next action and deadline without reopening the original feed.
- Users trust the source and can correct extraction errors.
- Users return to review upcoming milestones at least weekly during an active fan period.
- At least one narrow community produces reuse: the same activity is confirmed once and tracked by multiple people.
- Reminder volume remains low enough that users keep notifications enabled.

## Final recommendation

Build Oshinav if the goal is to make fan participation more reliable and less mentally expensive. Do not build it initially as another place where fans consume information.

The best initial promise is:

> **Bring any important fan announcement here. Oshinav tells you what it is, what you need to do, and when.**

That promise is useful even when the user spends most of their screen time on social media, because social media remains the discovery and community layer. Oshinav becomes the durable planning and accountability layer. The product is worth pursuing if real users repeatedly perform that handoff with real deadlines; otherwise, the broad community database and automated discovery ambitions should be reduced or stopped.

## Sources consulted

- [Eventernote](https://www.eventernote.com/) — event aggregation, recommendations, calendar sync, notifications, and community features.
- [Oshinomi](https://oshinomi.app/) — fan schedule aggregation, URL/screenshot AI import, reminders, and monitored sources.
- [推し活アラート on the App Store](https://apps.apple.com/jp/app/%E6%8E%A8%E3%81%97%E6%B4%BB%E3%82%A2%E3%83%A9%E3%83%BC%E3%83%88/id6788907357) — screenshot extraction into schedules and tasks, reminders, and pricing model.
- [おしきゅん](https://www.sanrio.co.jp/special/oshikyun/) — community-collected schedules and task management.
- [Grape on Google Play](https://play.google.com/store/apps/details?hl=ja&id=jp.karabina.grape) — multi-artist live/ticket calendar, reminders, and spending views.
- [Tanita fan-activity survey 2026](https://api-img.tanita.co.jp/files/user/news/pdf/2026/fanactivities_research.pdf) — reported concerns including time scarcity and being overwhelmed by fan information.
- [Video Research: 2024 fan-activity data](https://www.videor.co.jp/digestplus/article/consumer241118.html) — fan activity across age groups and its relationship to SNS, video, and purchasing.
- [Web担当者Forum: fan-fatigue research summary](https://webtan.impress.co.jp/n/2023/02/07/44254) — information overload as a reported contributor to fan fatigue.

*Research date: 2026-09-26. Competitor feature descriptions and usage figures are based on publicly available pages and may change; company-reported figures are explicitly treated as directional evidence.*
