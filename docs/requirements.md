# Requirements

## Overview

This application helps anime, game, VTuber, and pop-culture fans in Japan keep track of events, ticket reservations, preorders, lottery applications, merchandise releases, and other time-sensitive activities.

The target user may follow many works, creators, shops, and official accounts. Important information is often spread across official websites, X posts, ticketing pages, e-commerce pages, Animate pages, and other fragmented sources. Some activities require advance registration, booking, lottery entry, payment, pickup, or attendance.

The application maintains a **shared activity database** that can be contributed to by multiple users and monitored sources. Users maintain their own personal relationship with shared activities, including their status, tracking state, and notification preferences.

The MVP should help users answer three practical questions:

1. What activities am I tracking?
2. What deadlines or dates do I need to remember?
3. What new activities are happening for the anime, creator, or topic I care about?

The MVP should support two core discovery paths:

1. **Passive registration**, where a user provides a URL, X post, screenshot, or other source and the system extracts structured information.
2. **Active discovery**, where the system monitors selected sources for new activities related to works, creators, or topics users follow.

When new information is submitted or discovered, the system should first determine whether it refers to an existing shared activity. If it does, the system should update or associate the existing activity rather than create a duplicate.

The MVP goal is to complete the save, discover, track, and reminder loop before investing in advanced search, recommendation, or RAG-based features.

---

## Terminology

### Activity

One trackable real-world item, such as a concert, ticket sale, lottery, preorder, merchandise release, pickup window, shipment, or event.

An activity is shared between users.

### Activity Type

The category of an activity, such as:

* event
* ticket
* lottery
* preorder
* merchandise release
* pickup
* shipment

`Reminder` is not an activity type.

### Milestone

A meaningful date or time belonging to an activity, such as:

* sales opening
* application opening
* application closing
* payment due
* pickup starting
* release
* shipment
* event starting

An activity may have multiple milestones.

All milestones that include a time must carry a timezone or an explicit unknown-timezone state. Date-only milestones must remain date-only.

### User Status

The user's relationship to an activity, such as:

* interested
* applied
* booked
* paid
* ordered
* won
* lost
* picked up
* attended
* cancelled

These states are workflow-dependent. The implementation must not assume that every activity follows one universal sequence.

User status belongs to the user's relationship with an activity, not to the shared activity itself.

### Work or Topic

Something that users can follow and that can be associated with activities.

Examples include:

* anime
* manga
* game
* franchise
* VTuber
* creator
* brand
* shop
* venue
* other topic

### Source

The source from which activity information was obtained, such as:

* URL
* X post
* screenshot
* monitored source
* manual input

Multiple sources may describe the same activity.

---

## Shared and Personal Data

The system should distinguish between **shared activity information** and **personal tracking information**.

### Shared

The following information may be shared between users:

* Activity
* Milestone
* Work or Topic
* Source
* Activity-to-Work/Topic relationships
* External references when available

Multiple users should be able to reference the same Activity.

### Personal

The following information belongs to an individual user:

* User's tracked activities
* User status
* User's followed Works or Topics
* Notification preferences
* Notifications

Deleting or stopping tracking an activity for one user must not delete the shared activity.

---

## Goals

* Let users quickly save an event, ticket, preorder, lottery, or reservation from a URL, social post, or screenshot.
* Reuse activities already discovered by other users.
* Avoid duplicate shared activities when multiple sources refer to the same activity.
* Let users follow works, creators, shops, or topics and receive related updates.
* Help users remember what they have already booked, ordered, paid for, or plan to attend.
* Notify users at important lifecycle moments.
* Allow information from multiple sources to contribute to the same activity.
* Validate whether users find value in automated activity tracking, discovery, and reminders.

---

## Non-Goals

* Advanced RAG or vector search.
* Complex recommendation algorithms.
* Full-scale source coverage across every possible website.
* Sophisticated ranking, deduplication, or personalization beyond what is needed for the MVP.
* Full calendar integration beyond basic reminder delivery.
* Full order management, payment processing, or ticket resale functionality.

---

## Core Concepts

### Activity

Expected fields include:

* Activity name
* Activity type
* Date or date range
* Milestones
* Location when relevant
* Online/offline status when relevant
* Related Works, franchises, creators, shops, or topics
* Official website or source URL
* Sources
* External references when available

### Work or Topic

Expected fields include:

* Name
* Category
* Optional aliases or keywords
* Official links when available

### Source

The system should preserve the original source associated with discovered or submitted information.

A single Activity may have multiple Sources.

### Notification

A notification reminds a user about an Activity at an important milestone.

Initial notification triggers should include, when the corresponding milestone is known:

* When reservation, application, or sales opens
* Before reservation, application, payment, pickup, or preorder closes, when a reminder offset is configured
* On the event start date
* On the release, pickup, or shipment date when known
* When a new relevant activity is discovered for a followed Work or Topic

Reminder offsets and the default display timezone must be defined before notification scheduling is implemented.

---

## Functional Requirements

### Passive Registration

Users should be able to submit activity information through:

* A webpage URL
* An X post URL or copied post content
* A screenshot
* Manual entry

The system should:

* Determine whether the submitted content contains a trackable activity.
* Classify the activity type.
* Extract relevant details.
* Normalize extracted data.
* Preserve the original source.
* Determine whether the information refers to an existing shared activity.
* Associate the source with the existing activity when a match is found.
* Create a new shared activity when no suitable match exists.
* Ask for user confirmation or correction when extraction or matching is uncertain.
* Associate the activity with the submitting user.
* Create notifications based on relevant milestones.

### Activity Matching

When an activity is submitted or discovered, the system should check existing shared activities before creating a new one.

Matching may use:

* External IDs
* Official URLs
* Ticketing URLs
* Activity name
* Aliases
* Dates
* Venue
* Creator or Work
* Activity type
* Other extracted identifiers

The MVP should prioritize reliable matching over sophisticated semantic search.

When the system cannot determine a match with sufficient confidence, it should allow the user to confirm whether a candidate refers to an existing activity.

### Active Discovery

Users should be able to follow Works, creators, shops, or topics.

After a user follows something, the system should periodically search configured information sources for related activities.

Initial source types may include:

* Official websites
* Official X accounts or posts
* E-commerce pages
* Ticketing pages
* Animate or similar event and merchandise sources
* Other manually configured reliable sources

The initial implementation should define the polling cadence and source freshness policy.

The system should:

* Search sources using names, aliases, and related keywords.
* Detect potential new activities.
* Match discovered activities against existing shared activities.
* Create a new activity when no suitable match exists.
* Update an existing activity when new information is found.
* Associate activities with relevant Works or Topics.
* Identify users who follow the relevant Works or Topics.
* Notify relevant users according to their notification settings.
* Suppress duplicate notifications.

### Activity Tracking

The system should:

* Store activities in a shared structured format.
* Store milestones associated with activities.
* Maintain activity sources.
* Allow users to track shared activities.
* Allow users to update their own status.
* Allow users to configure notifications for tracked activities.
* Allow users to view upcoming and tracked activities.
* Allow users to update or remove their personal tracking relationship.

Removing an activity from a user's personal tracking must not remove the shared activity.

### Frontend Requirements

The frontend should support:

* Creating an activity manually or from submitted content
* Viewing tracked activities
* Viewing activity details
* Viewing activity milestones and sources
* Editing activity information where permitted
* Removing a personal activity
* Updating user status
* Following a Work, creator, shop, or topic
* Unfollowing a Work, creator, shop, or topic
* Viewing followed Works, creators, shops, and topics
* Viewing notifications

---

## MVP Scope

The MVP should focus on a simple but complete loop:

1. A user submits activity information or follows a Work, creator, shop, or topic.
2. The system extracts or discovers activity information.
3. The system checks whether the activity already exists in the shared database.
4. The system creates or updates the shared activity.
5. The system associates the activity with the relevant user and/or followed topic.
6. The user records their status when needed.
7. The system schedules reminders.
8. The user receives notifications.
9. Other users can discover and track the same shared activity without creating duplicate activity records.

---

## Success Criteria

The MVP is successful if:

* Users can save events, tickets, preorders, and reservations from common sources with minimal manual input.
* Multiple users can reuse the same shared activities.
* The system can identify common cases where different sources refer to the same activity.
* Users can maintain independent statuses and notification settings for the same activity.
* Users can follow works, creators, shops, or topics and receive relevant discoveries.
* Activity objects contain enough information to answer what the user saved, what status it is in, and what date or deadline matters next.
* Reminders are delivered at meaningful times.
* Important activity information discovered from one source can be reused by other users.
* The full save/discover → shared activity → personal tracking → notification loop works reliably enough to test user value.
