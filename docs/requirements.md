# Requirements

## Overview

This application helps anime, game, VTuber, and pop-culture fans in Japan keep track of events, ticket reservations, preorders, lottery applications, merchandise releases, and other time-sensitive activities.

The target user may follow many works, creators, shops, and official accounts. Important information is often spread across official websites, X posts, ticketing pages, e-commerce pages, Animate pages, and other fragmented sources. Some activities require advance registration, booking, lottery entry, payment, pickup, or attendance. Users may forget what they booked, when they paid, when reservations open, or when an event actually happens.

The MVP should help users answer three practical questions:

1. What events, tickets, orders, or reservations have I already saved?
2. What deadlines or dates do I need to remember?
3. What new activities are happening for the anime, creator, or topic I care about?

The MVP should support two core discovery paths:

1. Passive registration, where a user provides a URL, X post, screenshot, or other source and the system extracts structured information.
2. Active discovery, where the system monitors selected sources for new activities related to works, creators, or topics the user follows.

The MVP goal is to complete the save, discover, track, and reminder loop before investing in advanced search, recommendation, or RAG-based features.

## Terminology

- **Activity:** one trackable real-world item, such as a concert, ticket sale, preorder, or pickup window.
- **Activity type:** the category of an activity, such as event, ticket, lottery, preorder, merchandise release, pickup, or shipment. `Reminder` is not an activity type; it is a notification generated for an activity.
- **Milestone:** a meaningful date or time belonging to an activity, such as sales opening, application closing, payment due, pickup starting, or event starting. An activity may have several milestones.
- **User status:** the user's relationship to an activity, such as interested, applied, booked, paid, ordered, won, lost, picked up, attended, or cancelled. These states are workflow-dependent; the implementation must not assume that every activity uses one universal linear sequence. This is separate from the activity type and from notification delivery status.
- **Source:** the URL, post, screenshot, monitored feed, or manual input from which activity information was obtained.

All milestones that include a time must carry a timezone or an explicit unknown-timezone state. Date-only milestones must remain date-only. Reminder offsets and the default display timezone are MVP decisions that must be defined before notification scheduling is implemented.

## Goals

- Let users quickly save an event, ticket, preorder, lottery, or reservation from a URL, social post, or screenshot.
- Extract useful information into a structured activity object.
- Help users remember what they have already booked, ordered, paid for, or plan to attend.
- Let users follow works, creators, franchises, shops, or topics and receive related updates.
- Notify users at important lifecycle moments, such as application opening, preorder deadlines, payment deadlines, pickup windows, shipment timing, and event start.
- Validate whether users find value in automated activity tracking and reminders.

## Non-Goals

- Advanced RAG or vector search.
- Complex recommendation algorithms.
- Full-scale source coverage across every possible website.
- Sophisticated ranking, deduplication, or personalization beyond what is needed for the MVP.
- Full calendar integration beyond basic reminder delivery.
- Full order management, payment processing, or ticket resale functionality.

## Core Concepts

### Activity

An activity is the main object that users track. It may represent an event, ticket booking, lottery application, merchandise preorder, pickup window, paid order, release, or other time-sensitive item.

Expected fields include:

- Activity name
- Activity type, such as event, ticket, lottery, preorder, merchandise release, pickup, or shipment
- Date or date range
- Milestones, such as reservation, application, sales, payment, pickup, release, or event start
- Location when relevant
- Online/offline status when relevant
- Related work, franchise, creator, shop, or topic
- Current user status and, when needed, status history; examples include interested, applied, booked, paid, ordered, won, lost, picked up, attended, or cancelled
- Related context when available, such as venue, store, work, creator, or campaign
- Official website or source URL
- Source type, such as URL, X post, screenshot, manual entry, or monitored source
- Notification schedule derived from the activity's milestones

### Work Or Topic

A work or topic is something the user follows. It may represent an anime, manga, game, franchise, VTuber, creator, brand, shop, venue, or other subject that can have related activities.

Expected fields include:

- Name
- Category, such as anime, game, VTuber, creator, shop, brand, or venue
- Optional aliases or keywords
- Official links when available
- User follow status

### Notification

A notification reminds users about an activity at important moments.

Initial notification triggers should include, when the corresponding milestone is known:

- One day before reservation, application, or sales opens, if a reminder offset is configured
- When reservation, application, or sales opens
- Before reservation, application, payment, pickup, or preorder closes, if a reminder offset is configured
- On the event start date
- On the release, pickup, or shipment date when known

## Functional Requirements

### Passive Registration

Users should be able to submit activity information through:

- A webpage URL
- An X post URL or copied post content
- A screenshot containing activity information
- Manual entry when automatic extraction is not enough

The system should:

- Determine whether the submitted content contains a trackable activity.
- Classify the activity type, such as event, ticket, lottery, preorder, merchandise release, pickup, or shipment.
- Extract relevant details when a trackable activity is detected.
- Normalize extracted data into a structured activity object.
- Preserve the original source link or uploaded source reference.
- Ask for user confirmation or correction when extracted information is uncertain.
- Create notifications based on extracted milestones such as dates, deadlines, reservation times, release dates, or pickup windows.

### Active Discovery

Users should be able to follow works, creators, shops, or topics.

After a user follows something, the system should periodically search configured information sources for related activities. The initial implementation should define the polling cadence and source freshness policy. Initial source types may include:

- Official websites
- Official X accounts or posts
- E-commerce pages
- Ticketing pages
- Animate or similar event and merchandise sources
- Other manually configured reliable sources

The system should:

- Search sources using names, aliases, and related keywords.
- Detect potential new activities.
- Match discovered activities to followed works, creators, shops, or topics.
- Associate matched activities with users who follow the related subject.
- Notify users when a new relevant activity is found, subject to duplicate suppression and the user's notification settings.

### Activity Tracking

The system should track each registered or discovered activity through key lifecycle points.

The system should:

- Store activity details in a structured format.
- Maintain notification schedules for each activity's milestones.
- Allow users to record their own status, such as booked, applied, paid, ordered, or attended.
- Send reminders at configured timing points.
- Allow users to view upcoming and tracked activities.
- Allow users to update or remove activities.

### Frontend Requirements

The frontend should support:

- Creating an activity manually or from submitted content.
- Viewing a list of tracked activities.
- Viewing activity details.
- Editing activity information.
- Deleting an activity.
- Updating user status for an activity.
- Following a work, creator, shop, or topic.
- Unfollowing a work, creator, shop, or topic.
- Viewing followed works, creators, shops, and topics.
- Receiving or viewing notifications.

## MVP Scope

The MVP should focus on a simple but complete user loop:

1. A user submits activity information or follows a work, creator, shop, or topic.
2. The system extracts or discovers activity information.
3. The system creates a structured activity.
4. The system associates the activity with the user.
5. The user records their status when needed, such as booked, paid, ordered, or applied.
6. The system schedules reminders.
7. The user receives notifications at important times.

## Success Criteria

The MVP is successful if:

- Users can save events, tickets, preorders, and reservations from common sources with minimal manual input.
- Users can follow works, creators, shops, or topics and receive relevant discoveries.
- Activity objects contain enough information to answer what the user saved, what status it is in, and what date or deadline matters next.
- Reminders are delivered at meaningful times.
- The full save/discover-to-notification loop works reliably enough to test user value.
