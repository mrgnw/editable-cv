# Editable CV - Project Plan

A fork of [editable-website](https://github.com/michael/editable-website) adapted as a CV/résumé template.

## Overview

This project combines the inline-editing capabilities of editable-website with the clean, professional format of the [r/EngineeringResumes template](https://docs.google.com/document/d/1MBvhATv8y-ESORopRoLSZ3f3HjkM_Qa_f8fIHAEqgnI/edit).

Data structure follows [JSON Resume](https://jsonresume.org/schema) for interoperability. See `schema/resume.schema.json` for the full schema.

---

## Technology Stack

- **Framework**: SvelteKit (upgrading to Svelte 5 with runes)
- **Database**: SQLite (same as editable-website)
- **Styling**: Tailwind CSS
- **Editing**: ProseMirror (PlainText/RichText components)
- **Data Schema**: JSON Resume compatible

---

## JSON Resume Schema (Simplified for CV)

We'll implement the relevant portions of the JSON Resume spec:

```json
{
  "basics": {
    "name": "John Doe",
    "label": "Software Engineer",
    "email": "john@example.com",
    "phone": "(555) 123-4567",
    "url": "https://johndoe.com",
    "location": { "city": "San Francisco", "region": "CA" },
    "profiles": [
      { "network": "GitHub", "url": "https://github.com/johndoe" },
      { "network": "LinkedIn", "url": "https://linkedin.com/in/johndoe" }
    ]
  },
  "skills": [
    { "name": "Languages", "keywords": ["Python", "JavaScript", "Rust"] },
    { "name": "Frameworks", "keywords": ["SvelteKit", "React", "FastAPI"] }
  ],
  "work": [
    {
      "name": "Company",
      "position": "Senior Engineer",
      "location": "San Francisco, CA",
      "startDate": "2022-06",
      "endDate": null,
      "highlights": ["Built X that improved Y by Z%"]
    }
  ],
  "projects": [
    {
      "name": "Project Name",
      "url": "https://project.com",
      "highlights": ["Description of what it does"]
    }
  ],
  "education": [
    {
      "institution": "University",
      "area": "Computer Science",
      "studyType": "BS",
      "endDate": "2020-05"
    }
  ]
}
```

---

## Phases

### Phase 1: Foundation & Cleanup ✅

- [x] Set up fork with proper git remotes
- [x] Save JSON Resume schema to `schema/resume.schema.json`
- [x] Update `package.json` name to `editable-cv`
- [x] Remove unused routes (`/blog`, `/imprint`)
- [x] Remove unused components (Testimonial, IntroStep, ArticleTeaser, etc.)
- [x] Clean up the main page to be a blank slate

### Phase 2: Upgrade to Svelte 5

- [ ] Update dependencies to Svelte 5 and SvelteKit 2
- [ ] Convert stores to runes (`$state`, `$derived`)
- [ ] Update components to use `$props()` and `$bindable()`
- [ ] Test PlainText/RichText editors work with Svelte 5

### Phase 3: Data Model

- [ ] Define CV schema following JSON Resume spec
- [ ] Update database schema (`sql/schema.sql`)
- [ ] Create seed data with example CV
- [ ] Update API endpoints for CV data
- [ ] Add `JSON_RESUME_URL` env var support for importing resume from URL

#### JSON_RESUME_URL Import Feature

Add an optional `JSON_RESUME_URL` environment variable that allows bootstrapping a resume from an external JSON Resume file.

**Behavior:**

- If `JSON_RESUME_URL` is set and database has no CV data → fetch and use the JSON from URL
- If `JSON_RESUME_URL` is set and database has CV data → database takes precedence (user edits win)
- If `JSON_RESUME_URL` is not set → use default placeholder CV or database data

**Implementation:**

```
# .env
JSON_RESUME_URL=https://example.com/resume.json
```

- Fetch happens server-side in `+page.server.js`
- Validate fetched JSON against schema before using
- Consider caching the fetched JSON to avoid repeated requests
- Add a "Reset from URL" button in admin UI to re-import (with confirmation)

### Phase 4: Resume Layout

Build the main page matching Engineering Resume template:

1. **Header** - Name centered, contact info inline below
2. **Skills** - `Category: item, item, item` format
3. **Experience** - Job entries with bullet points
4. **Projects** - Title + URL with bullet descriptions
5. **Education** - Institution, degree, date

### Phase 5: Components

| Component              | Purpose                                |
| ---------------------- | -------------------------------------- |
| `CVHeader.svelte`      | Name + contact links row               |
| `CVSection.svelte`     | Section wrapper with title and divider |
| `SkillsSection.svelte` | Editable skill categories              |
| `WorkItem.svelte`      | Single job with editable highlights    |
| `ProjectItem.svelte`   | Project with link + highlights         |
| `EducationItem.svelte` | Degree entry                           |
| `EditableList.svelte`  | Add/remove/reorder list items          |

### Phase 6: Styling

- [ ] Print-friendly CSS (`@media print`)
- [ ] A4/Letter page sizing
- [ ] Professional typography (match template)
- [ ] Minimal, clean design
- [ ] PDF export button

---

## What We Keep from editable-website

- ✅ PlainText/RichText inline editing
- ✅ Login system & admin password
- ✅ SQLite persistence
- ✅ EditorToolbar (save/cancel)
- ✅ Modal component
- ✅ Core infrastructure (hooks, API routes)

## What We Remove

- ❌ Blog section (`/blog`)
- ❌ Testimonials
- ❌ FAQs
- ❌ IntroSteps (marketing flow)
- ❌ Imprint page
- ❌ Marketing imagery/icons
- ❌ Footer with counters

---

## Future Enhancements

- [ ] Multiple resume versions/variants
- [ ] Export to JSON Resume format
- [ ] Import from JSON Resume (file upload)
- [ ] Export to PDF (server-side)
- [ ] Theme variants
- [ ] Cloudflare Pages deployment option
- [ ] Sync/refresh from `JSON_RESUME_URL` on demand
- [ ] Diff view when URL source differs from database version

---

## Resources

- [Original editable-website](https://github.com/michael/editable-website)
- [JSON Resume Schema](https://jsonresume.org/schema)
- [r/EngineeringResumes Wiki](https://old.reddit.com/r/EngineeringResumes/wiki/)
- [Engineering Resume Template](https://docs.google.com/document/d/1MBvhATv8y-ESORopRoLSZ3f3HjkM_Qa_f8fIHAEqgnI/edit)
