# Frontend Design

## Design Direction

The app is a **calm, information-focused personal utility** for tracking anime, game, VTuber, and pop-culture activities.

The UI should feel:

**Calm · Clear · Dense · Fast · Reliable · Modern**

Avoid:

**Flashy · Promotional · Game-like · Cluttered · Over-designed**

Content artwork provides personality; the interface itself should remain quiet.

### Design References

* **ChatGPT** — simplicity and focus
* **X** — information density and scannability
* **Apple** — typography and spacing
* **Linear** — restraint and hierarchy

---

## Core Principles

### 1. Information First

Prioritize the information users need to make decisions quickly:

1. Activity title
2. Important date / deadline
3. User status
4. Supporting information

Dates and deadlines should have strong visual prominence.

### 2. Lists Over Cards

Prefer compact list items and sections over large rounded cards.

Cards should only be used when they provide meaningful grouping or separation.

### 3. Strong Hierarchy

Use typography, spacing, and position before color or decoration.

* Large / bold → primary information
* Semibold → titles and sections
* Regular → supporting information
* Muted → metadata

### 4. Restrained Decoration

Avoid unnecessary:

* Gradients
* Heavy shadows
* Large rounded containers
* Decorative backgrounds
* Illustrations
* Excessive animation
* Excessive badges

### 5. Images Identify Content

Anime/game/VTuber artwork should help users recognize an activity or work, but should not dominate the interface.

Prefer small or moderate image areas rather than large hero images.

---

# Color

The visual identity uses a cool aqua + grape palette.

| Role         | Color        | Hex       |
| ------------ | ------------ | --------- |
| Background   | Icy Aqua     | `#AEECEF` |
| Primary      | Dusty Grape  | `#53599A` |
| Primary Dark | Dark Grape   | `#414676` |
| Accent       | Pacific Cyan | `#068D9D` |
| Secondary    | Steel Blue   | `#6D9DC5` |
| Highlight    | Pearl Aqua   | `#80DED9` |
| Surface      | Near White   | `#F7FBFB` |
| Text         | Deep Navy    | `#20243A` |
| Muted Text   | Blue Gray    | `#667085` |

### Color Rules

* `Icy Aqua` is the main application background.
* `Dusty Grape` is the primary brand/action color.
* `Pacific Cyan` is used for important information and emphasis.
* `Steel Blue` supports secondary information and actions.
* `Pearl Aqua` provides subtle selection/highlight states.
* Semantic **red / amber / green** should remain reserved for status and system meaning.
* Avoid using the supplied multi-color gradients as default backgrounds.

Color should support hierarchy, not replace it.

---

# Typography

### Primary Font

**IBM Plex Sans**

Use it throughout the application for:

* Navigation
* Activity titles
* Body text
* Metadata
* Dates and numbers

IBM Plex Sans is free and open source and provides a clean, technical character similar to modern developer tools without making the UI feel like an IDE.

### Monospace

**IBM Plex Mono**

Use sparingly for technical or highly structured information such as:

* Dates
* Times
* Countdown values
* IDs
* Status labels

Example:

```text
2026.09.28
18:00
3 DAYS LEFT
```

### Typography Rules

Prefer **weight and size** over decorative typography.

Keep the number of font sizes and weights small and consistent.

---

# Interaction

Primary actions should always be obvious:

* Add activity
* Save
* Change status
* Follow
* Set reminder

Use lightweight interactions whenever possible:

* Inline actions
* Bottom sheets
* Contextual menus

Avoid unnecessary navigation for simple state changes.

---

# Navigation

Keep navigation shallow and predictable.

Suggested primary navigation:

* **Home**
* **Activities**
* **Discover**
* **Following**

The `+` / Add action should be easy to access from the main experience.

---

# Component Style

Components should feel compact and functional.

Prefer:

* Moderate corner radius
* Minimal shadows
* Clear spacing
* Strong text hierarchy
* Consistent iconography
* High information density

Avoid:

* Excessively rounded UI
* Floating decorative elements
* Large empty hero sections
* Excessive pills and badges
* Multiple competing accent colors

---

# Overall Rule

> **Let the content be expressive; let the interface be quiet.**

The product should feel like a reliable personal information tool rather than an entertainment portal.
