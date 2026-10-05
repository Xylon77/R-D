# BCC Portal – R&D Findings

Testing notes for the Bogura City Corporation digital platform (https://bcc.zohirrayhan.com/).

- **Tested on:** 5 October 2026
- **Method:** Manual testing as a normal visitor in a desktop browser (Chrome DevTools: Console and Network tabs, HAR export). No automated scanning, load testing, or intrusive testing was done.
- **Scope:** Home (All Services), Notice Board, notice detail pages, RSS/Atom feeds, Report a problem form, language toggle.
- **Limitations:** Findings come from one tester on one connection. Performance numbers are from a single run.

## Summary

| # | Issue | Severity | Status |
|---|-------|----------|--------|
| 1 | Language toggle / language tabs do not show Bangla | High | ✅ Resolved during testing |
| 2 | Client-side crash after submitting a report | High | ✅ Resolved during testing |
| 3 | Inconsistent spelling, wording and date formats | Low | ✅ Resolved during testing |
| 4 | Confusing subtitle on notice page | Low | ⚠️ Open |
| 5 | Interface labels not localised on notice page | Medium | ✅ Resolved during testing |
| 6 | RSS feed descriptions truncated mid-sentence | Medium | ⚠️ Open |
| 7 | Tables and lists flattened in feed output | Medium | ⚠️ Open |
| 8 | Feed in one language only, opens as raw XML | Low | ⚠️ Open |
| 9 | RSS/Atom buttons prominent and unexplained | Low (UX) | ⚠️ Open |
| 10 | No graceful error page | Medium | ⚠️ Open |
| 11 | Wording errors in the report form | Low | ⚠️ Open |
| 12 | Slow loading: slow server responses and sequential fetching | Medium | ⚠️ Open |

---

## Resolved during testing

### 1. Language toggle did not switch to Bangla — RESOLVED
- **Severity:** High
- **Pages:** Header language button on the home page; language tabs on notice pages (History of change)
- **Steps to reproduce (original):**
  1. Open the home page and click the language button labelled "Bang".
  2. Open a notice, then "History of change", and click each language tab.
- **Expected behavior:** The interface and content switch to Bangla (বাংলা).
- **Actual behavior (original):** The page stayed in English with reworded text (e.g. "Report a problem" → "Report the problem"), and the button changed to "EN". On notice pages both language tabs were labelled "English".
- **Supporting evidence:** The RSS feed (`/api/v1/notices/feed.rss`) already served every notice in Bangla (`bn-BD`), so the translated content existed but was not being shown.
- **Status:** Fixed by the site's maintainers during this R&D. Verified on: [date]. Screenshots of the original behavior are kept below for the record.
- **Screenshots:** `screenshots/home-before.png`, `screenshots/home-after.png`, `screenshots/notice-tab-1.png`, `screenshots/notice-tab-2.png`

### 2. Client-side crash after submitting a report — RESOLVED
- **Severity:** High
- **Page:** Report a problem, after submission
- **Steps to reproduce (original):** Complete steps 1–3 of the report form and submit.
- **Expected behavior:** Confirmation message with a tracking reference.
- **Actual behavior (original):** The page was replaced by "Application error: a client-side exception has occurred while loading bcc.zohirrayhan.com".
- **Console errors (original):** Minified React error #418 (hydration mismatch), followed by `NotFoundError` on `insertBefore` and `removeChild`.
- **Status:** No longer reproducible as of [date].
- **Note on certainty:** The original crash was observed in a browser with extensions active, and browser extensions or built-in page translation can cause this error pattern. A clean (incognito) reproduction was not completed before the issue stopped occurring, so the exact cause cannot be confirmed.
- **Screenshots:** `screenshots/crash.png`, `screenshots/console.png`

### 3. Inconsistent spelling, wording and date formats — RESOLVED
- **Severity:** Low
- **Observed (original):** "Bogura" vs "Bogra" City Corporation; "licence" vs "license"; "31 October" vs "October 31" (mixed on the same page); sentence case vs title case in navigation and headings; unnatural phrasings such as "Know the status" and "dirt" (for garbage); toggle label "Bang" instead of the language's own name (বাংলা).
- **Status:** Fixed by the site's maintainers during this R&D, together with the language fix (issue 1). Verified on: [date].

### 5. Interface labels not localised on notice page — RESOLVED
- **Severity:** Medium
- **Observed (original):** "History of change", "Versions", "Current", "Version 1 (1 in total)", "Full text", and the timestamp format did not change with the language selection. The subtitle also did not change with the selected tab.
- **Status:** Fixed together with the language fix (issue 1). Verified on: [date].

---

## Open issues

### 4. Confusing subtitle on notice page
- **Severity:** Low
- **Observed:** The line above the heading reads "The last date for renewal of notice board/trade license is October 31", which refers to renewing a *notice board* and duplicates the heading.
- **Suggested fix:** Use the notice title or a breadcrumb instead.

### 6. RSS feed descriptions truncated mid-sentence
- **Severity:** Medium
- **Examples:** `BCC/NOT/2026/0004` (tender notice) ends at "দরপত্র জামানত ৳…" with the amount missing; `BCC/NOT/2026/0003` (dengue drive) ends at "পোর্টালের সমস্যা…".
- **Expected behavior:** Full text, or a summary that ends at a sentence boundary with a link to the full notice.

### 7. Tables and lists flattened in feed output
- **Severity:** Medium
- **Examples:** The streetlight schedule (`BCC/NOT/2026/0008`) loses its table structure ("সপ্তাহ ওয়ার্ড ১ম ১ – ৫ ২য় ৬ – ১০…"). The tender, dengue and recruitment notices lose their list separators.

### 8. Feed available in one language only, opens as raw XML
- **Severity:** Low
- **Observed:** The RSS and Atom buttons both return an identical Bangla-only feed regardless of the interface language, displayed as unstyled XML with no explanation.
- **Suggested fix:** Offer an English feed or label the language, add a feed stylesheet, or add a short explanation and "copy feed URL" option.

### 9. RSS/Atom buttons are prominent and unexplained
- **Severity:** Low (UX/design)
- **Observed:** Two buttons ("RSS", "Atom") sit beside the Notice Board title, in the spot users expect search or filter. Both return identical content.
- **Problems:** City residents are unlikely to use feed readers; two formats for the same data add confusion; clicking leads to raw XML with no guidance.
- **Suggested fix:** Keep one feed link in the footer with a short description, or offer resident-friendly alerts (email, SMS, social media).
- **Screenshots:** `screenshots/notice-board-header.png`, `screenshots/atom-output.png`

### 10. No graceful error page
- **Severity:** Medium
- **Observed:** When the report-submission crash occurred (issue 2), users saw the default framework message with no explanation, no retry option, and no indication whether the report had been saved.
- **Suggested fix:** Add a custom error page with a retry/home button and, where possible, a way to check whether the submission went through.

### 11. Wording errors in the report form
- **Severity:** Low
- **Examples:** Breadcrumb reads "Tell ← the problem"; page title "New Reports" (plural); step label "2. Location." with a stray period; awkward file-limit text ("8 MB each ≤", "shortened before sent").
- **Screenshot:** `screenshots/report-form.png`

### 12. Slow loading: slow server responses and sequential fetching
- **Severity:** Medium
- **Page tested:** Home (`/`), Chrome DevTools HAR export
- **Test conditions:** [Chrome version, network, throttling on/off, cache disabled yes/no]
- **Measurements (single run):**

  | Metric | Value |
  |---|---|
  | Requests / data transferred | 42 / ~457 KB |
  | DOMContentLoaded / Load | 1.34 s / 2.66 s |
  | Main HTML time to first byte | ~948 ms |
  | API calls (`flags`, `notices`, `auth/me`) | ~807 / 834 / 887 ms server wait |
  | API calls start at | ~2.67 s (after page load) |
  | Last request finished | ~5.9 s |

- **Findings:**
  1. The page is small, so the delay comes from waiting, not downloading.
  2. The HTML is generated per request (`no-store`, Cloudflare `DYNAMIC`) with ~0.95 s to first byte.
  3. Content is fetched client-side only after JavaScript loads, so notices likely appear around 3.5 s in (estimated from request timings).
  4. All three API endpoints take 0.8–0.9 s of server wait; `/api/v1/flags` has no cache headers.
  5. `/api/v1/auth/me` returns 401 for anonymous visitors and is called on every page load.
  6. 14 route-prefetch requests (`?_rsc=`) fire on the home page; several take 1.6–2.0 s.
  7. Static assets wait ~290 ms each despite being cached for a year, suggesting high latency from this region (or throttling during capture).
  8. Six Bangla web-font files (~201 KB, about 44% of transferred bytes) load from Google Fonts.
- **What works well:** HTTP/3, modern compression, long-lived cache headers on static assets, sensible cache headers on the notices API, no images on the home page.
- **Suggested fixes:** Cache or statically generate the home page; fetch notices on the server so content ships with the HTML; add cache headers to `/flags`; skip `/auth/me` when no session exists; limit prefetching; self-host fonts; consider hosting or a CDN edge closer to Bangladesh.
- **Evidence:** `bcc_zohirrayhan_com.har`

---

## Tools used

- Chrome DevTools (Console, Network, HAR export)
- Manual testing and screenshots

## Notes

- Issues marked "Resolved" were observed and later fixed during this R&D (language switching, spelling and wording consistency, and the submission crash). The original reproduction details are kept for the record.
