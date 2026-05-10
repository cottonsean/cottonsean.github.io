# ADR-0001: Update Photo Caption to Identify Subject by Name

**Date:** 2026-05-10
**Status:** Accepted

## Context
A photo caption on the personal portfolio/website identified a person in an image only as "my brother" without providing their name. This left the subject anonymous to site visitors who may not have personal context, and missed an opportunity to properly credit and acknowledge the individual pictured at what appears to be a significant personal event.

## Decision
The caption was updated to include the full name of the person pictured — Mr. Anthony Bothwell — along with the context of the occasion (his birthday celebration). The existing tone and cultural references ("Black and Gold," "1906 forever") were preserved, as they carry intentional meaning tied to Alpha Phi Alpha fraternity heritage.

## Consequences

**Benefits:**
- The subject is now properly identified and credited by name, which is a basic courtesy when featuring someone in a public-facing medium.
- Visitors unfamiliar with the site owner's personal network gain meaningful context about the relationship and occasion.
- The caption becomes more personal and specific, strengthening the authenticity of the "life" section of the site.

**Trade-offs/Risks:**
- Including a real person's full name on a public website introduces minor privacy considerations, though this is presumed acceptable given the celebratory and consensual nature of the context.
- No structural, styling, or technical changes were made — this is purely a content update with negligible risk to site stability or layout.

## Files Changed

| File | Change |
|------|--------|
| `index.html` | Updated the `life-caption` div text within a `.life-card.life-tall` section to replace the generic reference "my brother" with the named identification "Mr. Anthony Bothwell at his birthday celebration." |