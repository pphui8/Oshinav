# Frontend Design

This document defines the visual system, information architecture, screen behavior, and interaction rules for Oshinav. It reflects the shared-activity model in `requirements.md`: activity information is shared, while tracking, status, follows, and notification preferences belong to each user.

## Product Experience

Oshinav is a calm, information-focused personal utility for time-sensitive anime, game, VTuber, and pop-culture activities.

The interface should feel:

**Calm · Clear · Dense · Fast · Reliable · Modern**

Content artwork can provide personality; the interface itself should remain quiet. Avoid promotional layouts, game-like decoration, large hero sections, heavy shadows, gradients, and excessive pills or badges.

The central experience is a date-first activity list. A user should be able to answer, at a glance:

1. What am I tracking?
2. What matters next, and when?
3. What is my status?
4. What new activity needs my attention?

## Design Principles

### Information before decoration

Use typography, position, and spacing before color or imagery. The default priority is:

1. Activity title
2. Next milestone and time remaining
3. Personal status and tracking state
4. Activity type, work/topic, location, and source

Dates and deadlines must be more prominent than supporting metadata. Never hide a deadline behind an icon-only action or a secondary tab.

### Lists before cards

Use compact list rows for activities, notifications, sources, and followed subjects. Use a card only when it creates a useful boundary, such as an import step, a duplicate-match decision, or a settings group.

### Shared information, personal action

Activity detail must clearly separate:

* **Shared activity information:** title, type, milestones, location, related subjects, and sources.
* **Your tracking:** tracked/untracked state, status, reminder settings, and status history.

An action such as “Stop tracking” must never look like “Delete activity.” Shared activity edits should show permission context and source attribution.

### Progressive disclosure

Show the next decision first. Put additional milestones, sources, status history, and extraction confidence below it or behind an expandable section. Do not make the user navigate away for a simple status change, follow, reminder, or tracking action.

### Content carries personality

Artwork is optional and should be small or moderate in size. Use it as recognition support in a row or detail header, never as the dominant visual. Missing artwork must have a stable, quiet placeholder.

## Information Architecture

The primary navigation is shallow and consistent:

* **Home** — upcoming deadlines, recently updated tracked activities, and unread notifications.
* **Activities** — the complete personal tracking list with filters.
* **Discover** — discovered activities and subject search.
* **Following** — followed works, creators, shops, and topics.

The global add action is available from Home, Activities, and Discover. It opens the import/manual-entry flow.

Notifications are available from a persistent bell in the app header. Settings and account actions live under the user menu, not in the primary navigation.

### Responsive shell

On mobile, use a bottom navigation bar with Home, Activities, Discover, and Following, plus a centered or adjacent add action. Keep the notification bell in the top bar.

On larger screens, use a narrow left sidebar for primary navigation, a top bar for search, notifications, and account, and a centered content column. The content column should remain readable rather than expanding indefinitely.

The add action must remain reachable without scrolling. Preserve the same information hierarchy across breakpoints; only layout density should change.

## Screen Designs

### Home

Home is an action-oriented dashboard, not a marketing landing page.

Recommended order:

1. Greeting and notification entry point.
2. **Next up** — the nearest upcoming milestone from tracked activities.
3. **Needs attention** — overdue or soon-to-close milestones, unread discoveries, and imports needing confirmation.
4. **Upcoming** — a compact chronological list.
5. **Recently updated** — shared activity changes relevant to the user.

Use an empty state that explains the two entry paths: add an activity or follow a subject. Do not show empty metric tiles without a useful action.

### Activities

The default view is a chronological list sorted by the next upcoming milestone. Each row contains:

* optional thumbnail or type marker;
* activity title and related work/topic;
* next milestone label, absolute date/time, and relative countdown when useful;
* the user’s status;
* a compact tracking/reminder affordance.

Provide lightweight filters for status, activity type, date range, and “needs attention.” Filters should be removable and reflected in the page title or toolbar. Avoid turning every filter into a permanent chip.

Rows open the activity detail. Row actions may include status, reminder, and stop tracking; destructive actions require confirmation.

### Activity detail

The header shows title, type, related subjects, next milestone, and the primary personal action. The primary action is context-sensitive:

* **Track activity** when the user is not tracking it.
* **Update status** when already tracking.
* **Set reminder** when a milestone exists but reminders are off.

Use two clear sections:

**Your tracking**

* tracking state;
* current status and status history;
* reminder preferences;
* stop tracking action.

**Activity information**

* all milestones in chronological order;
* location or online/offline context;
* related works/topics;
* sources and external references;
* last updated/source attribution.

Status must be selected from the workflow’s available values. Do not present statuses as a universal progress bar or imply that every activity moves from interested to attended.

### Add and import flow

The add flow supports URL, X post/content, screenshot, and manual entry. Use a focused bottom sheet on mobile and a centered dialog or narrow page on desktop.

The flow has four visible states:

1. **Choose source** — URL, X, screenshot, or manual entry.
2. **Processing** — show that extraction is asynchronous and allow the user to leave safely.
3. **Review** — show extracted fields, uncertainty, original source, and editable corrections.
4. **Match decision** — if a likely shared activity exists, compare the candidate with the existing activity and ask whether to reuse it; otherwise offer creation.

After confirmation, clearly state whether the user is now tracking an existing shared activity or has created a new one. Preserve the original source in the result.

Never silently merge uncertain matches. A low-confidence match should be presented as a decision with enough evidence: title, type, date, venue, related subject, and source.

### Discover

Discover has two complementary sections:

* **For you** — candidate activities found for followed subjects.
* **Find subjects** — search and follow works, creators, shops, brands, venues, and topics.

Discovery rows show why the item is relevant, its source, the next important date, and whether it is already tracked. Actions are “Track,” “View,” and “Dismiss.” Saving a discovery should reuse the shared activity when one exists.

Use a quiet “already tracked” state rather than presenting a duplicate save action.

### Following

Show followed subjects as a compact list with category, name, and notification state. Provide search and category filtering. Following and unfollowing should be immediate, reversible, and idempotent.

Subject detail may show related tracked and discovered activities, but it should not become a large profile page.

### Notifications

Notifications are personal and should be grouped by date or urgency. Each row includes:

* reason: milestone reminder, activity update, or new discovery;
* activity title;
* relevant date;
* read/unread state;
* direct action to open the activity or discovery.

Provide “mark read” and “mark all read.” Use semantic status colors sparingly; unread state should primarily use weight, a small indicator, and placement.

## Visual System

### Color tokens

| Role | Token | Hex |
| --- | --- | --- |
| App background | Icy Aqua | `#AEECEF` |
| Primary action | Dusty Grape | `#53599A` |
| Primary pressed/dark | Dark Grape | `#414676` |
| Information emphasis | Pacific Cyan | `#068D9D` |
| Secondary action | Steel Blue | `#6D9DC5` |
| Selection highlight | Pearl Aqua | `#80DED9` |
| Surface | Near White | `#F7FBFB` |
| Primary text | Deep Navy | `#20243A` |
| Muted text | Blue Gray | `#667085` |

Use Icy Aqua as the application background and Near White for readable surfaces. Dusty Grape is the main action color. Pacific Cyan is for dates, links, and information emphasis. Steel Blue supports secondary controls.

Reserve red, amber, and green for semantic meaning such as error, warning/overdue, and success. Do not use the full palette as decoration, and do not use multi-color gradients as default backgrounds.

All text and controls must meet accessible contrast requirements. A color must never be the only signal for status, unread state, or urgency.

### Typography

Use **IBM Plex Sans** for navigation, titles, body copy, metadata, and controls. Use **IBM Plex Mono** sparingly for dates, times, countdowns, IDs, and status labels.

Keep the scale compact and consistent:

* page title: large, semibold;
* section title: medium, semibold;
* activity title: medium, semibold;
* body and controls: regular/medium;
* metadata: small, muted;
* countdown/date: semibold mono with tabular numerals.

Prefer weight and spacing over all-caps or decorative type. Dates should include an unambiguous absolute form; relative text such as “2 days left” is supplemental.

## Component Rules

### Activity row

Rows use a clear left-to-right hierarchy: recognition, title/context, next milestone, personal status, then optional action. Keep rows compact but provide a comfortable touch target. Long titles wrap rather than truncate important dates.

### Milestone list

Display milestones chronologically. Use distinct treatments for date-only and timed milestones; retain the source timezone when a time is known. Never invent a time for a date-only milestone or silently convert an unknown timezone.

### Status control

Use a menu, segmented control, or bottom sheet appropriate to the number of available workflow statuses. Status labels must describe the user’s relationship, not the shared activity’s global state.

### Source list

Show source type, title/domain, captured date, and an external-link action. Multiple sources should be visibly grouped under the same activity rather than shown as duplicate activities.

### Buttons and controls

Use one primary action per region. Secondary actions are text or outlined controls. Destructive actions are separated from routine actions and require confirmation when they affect personal tracking. Icon-only buttons require accessible labels and tooltips on larger screens.

### Cards, borders, and radius

Use moderate corner radii and 1px low-contrast borders to define surfaces. Shadows should be minimal and functional. Avoid oversized rounded containers, floating decoration, and nested cards.

## State and Error Design

Every data-driven screen needs loading, empty, error, and stale/updating states. Async imports and discovery jobs must show queued/processing/needs-confirmation/completed/failed states without losing the submitted source.

Use plain, actionable copy:

* explain what happened;
* say whether anything was saved;
* provide the next action;
* preserve or link to the original source when relevant.

Optimistic updates are appropriate for follow, unfollow, read, status, and reminder changes when rollback is clear. Activity creation and matching should show a confirmed result before claiming success.

## Accessibility and Localization

Support keyboard navigation, visible focus, screen-reader labels, touch targets, reduced motion, and text resizing. Do not rely on hover, animation, color, or icon shape alone.

The initial audience is Japanese users. Design for Japanese text expansion and wrapping, Japanese date/time conventions, and timezone-aware displays. Keep machine-readable dates and IDs out of primary user-facing copy unless useful. Preserve date-only values as date-only.

## Overall Rule

> Let the content be expressive; let the interface be quiet.

Oshinav should feel like a reliable personal information tool that makes shared activity data useful to an individual fan.
