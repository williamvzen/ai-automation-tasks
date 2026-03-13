---
name: format-calendar-meetings
description: Converts raw calendar/meeting paste (between ---INPUT---) into a day-grouped list of meeting titles only. Use when the user pastes calendar data with ---INPUT---, asks to format meetings, or wants "calendar to list" / "meetings by day" output.
---

# Format Calendar Meetings

Takes raw calendar export or paste (delimited by `---INPUT---`) and returns a clean day-grouped list of meeting titles only.

## When to Use

- User pastes calendar/meeting data wrapped in `---INPUT---` ... `---INPUT---`
- User asks to "format my calendar," "convert meetings to list," or "meetings by day"
- User wants the standard day + bullet list output from a calendar paste

## Conversion Rules

1. **Extract days**: Each day line looks like `Today-Monday, March 9th` or `Today-Friday, March 13th`. For output, use **only the weekday**: e.g. `Monday`, `Friday` (strip "Today-", comma, and the date part like ", March 9th").

2. **Extract meeting titles only**: For each event line (e.g. `10:00 AM-10:30 AM eComm Standup [Mondays]`), keep **only the title** (strip time range, "Going? Yes", etc.).

3. **Remove these entries entirely**:
   - Any event whose title is **Google Calendar** (with or without `[7:30 AM]` or similar).
   - Any event titled **All-day Home** (or "All-day Home" as the only content).

4. **Strip bracket context from titles**: If the title contains text in square brackets, remove that bracket and its contents. Examples:
   - `eComm Standup [Mondays]` → `eComm Standup`
   - `eComm Standup [Tuesdays - 1]` → `eComm Standup`
   - `eComm Weekly Brainstorm Session [optional]` → `eComm Weekly Brainstorm Session`

5. **Day format**: In the output, the day line must be the weekday only (e.g. `Monday`, `Tuesday`, …, `Friday`), not "Friday, March 13th".

## Output Format

Use exactly this structure. Only day name as header, then bullets with meeting title only:

```text
**{{DAY1}}**:
 - {{Meeting1}}
 - {{Meeting2}}

**{{DAY2}}**:
 - {{Meeting1}}
 - {{Meeting2}}
```

- One blank line between day blocks.
- No times, no "Going? Yes", no brackets in titles.

## Workflow

1. Locate the content between `---INPUT---` and the closing `---INPUT---` (or end of paste).
2. Split by day lines (e.g. `Today-Monday, March 9th`).
3. For each day: derive weekday only; collect event lines; for each event apply removal rules and bracket stripping; keep only the title.
4. Emit the result in the output format above.

## Example

**Input** (excerpt):

```text
---INPUT---
Today-Monday, March 9th
10:00 AM-10:30 AM eComm Standup [Mondays]
Going? YesAll-day HomeGoogle Calendar  [7:30 AM]
Today-Tuesday, March 10th
10:00 AM-10:30 AM eComm Standup [Tuesdays - 1]
Going? YesAll-day HomeGoogle Calendar  [7:30 AM]
Today-Friday, March 13th
10:30 AM-11:30 AM eComm Weekly Brainstorm Session [optional]
Going? Yes12:00 PM-12:50 PM Architecture Guild Roundtable
---INPUT---
```

**Output**:

```text
**Monday**:
 - eComm Standup

**Tuesday**:
 - eComm Standup

**Friday**:
 - eComm Weekly Brainstorm Session
 - Architecture Guild Roundtable
```

## Edge Cases

- **Empty day**: If after filtering a day has no meetings, omit that day block.
- **Multiple spaces / line breaks**: Treat "Google Calendar" and "All-day Home" as whole titles; remove those lines regardless of surrounding text (e.g. "All-day HomeGoogle Calendar" as adjacent tokens still means remove both entries).
- **Brackets**: Remove any `[ ... ]` segment from the title text, including `[optional]`, `[Mondays]`, `[Tuesdays - 1]`, etc.
