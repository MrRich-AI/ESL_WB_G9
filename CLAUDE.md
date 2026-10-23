# CLAUDE.md — WB & Essay HTML Builds

Richard Imbeau, IGCSE ESL teacher (Grade 9: 9A1, 9A2, 9A3; Grade 10: 10A1) at Royal International School, Vietnam. This repo holds Cambridge IGCSE ESL Workbook (**WB**) activities and open-ended writing (**Essay HTML**) activities as self-contained HTML pages.

> These rules mirror the claude.ai Project "WB & Essay HTML Builds" and are kept in sync with it. The same file lives at the root of both ESL_WB_G9 and ESL_WB_G10. If you change a rule here, change it in both repos.

## General
- If a request seems to contradict or clash with these rules, ask for clarification before building anything.

## Repos and layout
- Grade 9 builds go in **ESL_WB_G9**; Grade 10 builds go in **ESL_WB_G10** (owner mrrich-ai).
- Each build is a single self-contained `index.html` in its own folder, following the existing folder naming (e.g. `Unit_3_WB_LangFocus_A_pp29-30`, `Unit_13_WB_Reading_B_p130`).
- The root `index.html` is the landing page: one card per unit (all units kept, newest first); selecting a unit shows all its builds. Add each new build to it.
- Ask before committing or pushing a new build.

## Building from uploaded workbook images
- Extract the questions from the image and make the activity identical to the one shown. Ask first if there's a concern about how to present something.
- If the title and page number aren't provided, ask before building (this doubles as the activity's title).
- The top of an uploaded page often shows the bottom of the previous page (black bar, page-number strip) — don't reproduce it.
- Page numbers appear only in the title and instructions, never as a page-number header in the interface.
- Copy the workbook's style and format, but never include the running header "CAMBRIDGE IGCSE™ ENGLISH AS A SECOND LANGUAGE: WORKBOOK".

## WB rule set
- **Layout:** widescreen/laptop (landscape, multi-column allowed, one activity/screen at a time, no horizontal scrolling; vertical scrolling fine for longer content).
- **Visual style:** match the uploaded image/photo's style. High contrast text-to-background throughout.
- **Order:** keep the book's question order (no question shuffle). Multiple-choice answer order is still shuffled.
- **Structure:** homescreen with selectable activities (builds typically have 4–7) and progress tracked per activity. When one uploaded image is split into several activities, each is its own activity (own review screen, own Review button).
- **Student identity:** start screen with required name and class inputs, no placeholder text; Start enabled only when both are filled. Name and class shown on the results screen.
- **Multiple-choice:** single attempt per question — locks on any answer, reveals the correct answer, Next unlocks once answered regardless of correctness. For complex types (sorting, matching, drag-and-drop), ask how scoring/advancement should work.
- **Word-bank activities:** no per-question Check button. Students fill all gaps freely (editable, kept if they leave and return). A single "Check answers" button unlocks only once every gap is filled, then grades and locks everything at once.
- **Review:** after each activity, a review screen lists every question (correct and wrong) with the student's answer, the correct answer and an explanation, before returning to the homescreen. Completed activities keep a persistent "Review" button on the homescreen opening that same screen.
- **Scores:** shown as fractions of the total (e.g. "6/7").
- **No "Try Again"** and no replay attempts counter; `attempts` is hardcoded to 1.

## WB data submission
Endpoint (Google Sheet "WB Data Collector"):
```js
var GAME_DATA_ENDPOINT = 'https://script.google.com/macros/s/AKfycbwEdGlILb09xvDeqX9EZGHXOTqQA8Um7Se0nojnr2vC4ujaGAaV4iz7TwDWB1Pxbf7F2Q/exec';
submitGameData({ gameName: 'Your WB Exercise Name Here', studentName: studentName, class: studentClass, score: correctCount + '/' + totalQuestions, minutesPlayed: minutesPlayed, wrongQuestions: wrongQuestionsList, attempts: 1 });
```
- Fire once per completed run (duplicate-submission guard; navigating back to the results screen must not resubmit).
- Field order: gameName, studentName, class, score, minutesPlayed, wrongQuestions, attempts.
- `wrongQuestions`: comma-separated "Q#: response" pairs using the student's own answer (e.g. "2: article, 5: blog"); empty string or omitted if nothing missed.
- `minutesPlayed`: "MM:SS" (e.g. "02:06"). The sheet's Timestamp column shows date and time.

## Live external calls (e.g. AI feedback)
- The button that advances to review/results is always clickable and never gated on the external call succeeding.
- The student's raw input for that activity always goes into `wrongQuestions` as plain data (not tagged "wrong"), whether or not the call succeeded.
- After a failed call, the trigger button relabels to a retry (e.g. "Try again for AI feedback"); the review screen shows a plain note in place of the missing result.
- The proxy auto-retries transient errors server-side (up to 2 retries with a short delay) before falling back to the manual retry button.
- While waiting, show loading text telling the student not to close the window.
- Near the finish/submit button, show a clear note that work isn't submitted until they click it.

## Essay HTML rule set
- Based on the WB rule set (widescreen, no "Try Again", attempts = 1, post-activity review screen, persistent Review button, image-matched style, required name/class, live-call safeguards above). No answer shuffling and no single-attempt lock (not multiple-choice).
- **AI feedback:** Gemini, routed through a separate Apps Script Web App proxy that holds the API key server-side. Never embed an API key in the HTML — GitHub push protection blocks it. The HTML sends only the prompt; the proxy calls Gemini and returns parsed feedback JSON.
- **Scoring:** against the official Cambridge IGCSE ESL (0510) mark scheme — Content (0–6) and Language (0–9) — with a mark and qualitative comments explaining each band placement. Score format: "X/15 (Content: x/6, Language: x/9)".

## Essay data submission
- Endpoint (Google Sheet "Essay Data"): `https://script.google.com/macros/s/AKfycbwNhsD9-6Zxcm9zz2Rq3uCwx8jxbu3KPYcLULLtjV85zPoUsCmAp5xcNnXepWfOitUSBA/exec`
- Fire once per completed run.
- Field order: timestamp (added server-side), activity, studentClass, studentName, duration, score, feedback, essayResponse — matching sheet headers Timestamp | Activity | Class | Name | Duration | Score | AI Feedback | Essay Response.
- The Apps Script writes columns by fixed array position, not by matching header text.
- `duration` formatted as "Xmin Ysec" (e.g. "2min 15sec").
