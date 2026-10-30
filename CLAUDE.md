# CLAUDE.md — WB & Essay HTML Builds

Richard Imbeau, IGCSE ESL teacher (Grade 9: 9A1, 9A2, 9A3; Grade 10: 10A1) at Royal International School, Vietnam. This repo holds Cambridge IGCSE ESL Workbook (**WB**) activities and open-ended writing (**Essay HTML**) activities as self-contained HTML pages.

> These rules mirror the claude.ai Project "WB & Essay HTML Builds" and are kept in sync with it. The same file lives at the root of both ESL_WB_G9 and ESL_WB_G10. If you change a rule here, change it in both repos.

## General
- If a request seems to contradict or clash with these rules, ask for clarification before building anything.

## Pick the rule set first
- Richard names the build type in the request: **WB** or **ESSAY**. If the type isn't stated, ask before building.
- WB builds follow the WB rule set and submit only via the WB data submission template (NEW WB DATA sheet).
- ESSAY builds follow the Essay HTML rule set and submit only via the Essay data submission template (new Essay sheet).
- Never include both endpoints in one build.

## Repos and layout
- Grade 9 builds go in **ESL_WB_G9**; Grade 10 builds go in **ESL_WB_G10** (owner mrrich-ai).
- Each build is a single self-contained `index.html` in its own folder, following the existing folder naming (e.g. `Unit_3_WB_LangFocus_A_pp29-30`, `Unit_13_WB_Reading_B_p130`).
- **Section letters (WB builds):** when a WB build is the first for a new unit or a new section (Vocabulary, Language focus, Reading, etc.), ask Richard before building whether it gets a letter, starting with **A**. If yes, the letter goes everywhere the build is named: activity title, folder name, landing-page link and `gameName` (e.g. "Unit 3 WB Vocabulary A pp. 26-27", folder `Unit_3_WB_Vocabulary_A_pp26-27`). Later builds in the same unit and section get the next letter (B, C…) automatically, without asking — unless the build appears to skip a section (e.g. a workbook page or exercise between the last lettered build and this one has no build), in which case ask first.
- The root `index.html` is the landing page: one card per unit (all units kept, newest first); selecting a unit shows all its builds. Add each new build to it.
- New builds and edits: commit and push without asking (Oct 30 2026); never paste the raw HTML code in chat.

## Building from uploaded workbook images
- Extract the questions from the image and make the activity identical to the one shown. Ask first if there's a concern about how to present something.
- If the title and page number aren't provided, ask before building (this doubles as the activity's title).
- The top of an uploaded page often shows the bottom of the previous page (black bar, page-number strip) — don't reproduce it.
- Page numbers appear only in the title and instructions, never as a page-number header in the interface.
- Copy the workbook's style and format, but never include the running header "CAMBRIDGE IGCSE™ ENGLISH AS A SECOND LANGUAGE: WORKBOOK".

## WB rule set
- **Layout:** widescreen/laptop (landscape, multi-column allowed, one activity/screen at a time, no horizontal scrolling; vertical scrolling fine for longer content).
- **Tablets:** every screen must scroll by touch. Pin each activity's button row (Check answers / Finish activity / Submit / Back to home) to the bottom of the screen with `position:sticky; bottom:0` on an opaque bar. Put `overflow-x:hidden` on `html` only, never on `body`: a body-level overflow makes `body` a scroll container and stops sticky from working. Add a floating "Scroll down for more ↓" hint that shows while content continues below the screen, hides at the bottom, and scrolls down when tapped.
- **Visual style:** match the uploaded image/photo's style. High contrast text-to-background throughout.
- **Order:** keep the book's question order (no question shuffle). Multiple-choice answer order is still shuffled.
- **Structure:** homescreen with selectable activities (builds typically have 4–7) and progress tracked per activity. When one uploaded image is split into several activities, each is its own activity (own review screen, own Review button).
- **Student identity:** start screen with required name and class inputs, no placeholder text; Start enabled only when both are filled. Name and class shown on the results screen.
- **Multiple-choice:** single attempt per question — locks on any answer, reveals the correct answer, Next unlocks once answered regardless of correctness. For complex types (sorting, matching, drag-and-drop), ask how scoring/advancement should work.
- **Word-bank activities:** no per-question Check button. Students fill all gaps freely (editable, kept if they leave and return). A single "Check answers" button unlocks only once every gap is filled, then grades and locks everything at once.
- **No pasting:** block pasting into every answer box (gaps, text inputs, textareas): keyboard paste, right-click/long-press paste and drag-and-drop. Show a short "Please type your answer." message when a student tries. Copying is not blocked and attempts are not logged. WB builds only — Essay builds allow pasting.
- **Translated feedback:** every feedback message a student sees (correct/incorrect explanations, "you found…" messages, review-screen explanations and AI feedback) shows a translation on the line under the English. Vietnamese by default; Grade 9 builds also switch to Simplified Chinese when the page is opened with `?zh` (e.g. `…/index.html?zh`). Fixed feedback is translated in the build; AI feedback is translated by Gemini in the same request (ask for a `*_tr` field next to each English comment). Instructions, buttons and example sentences stay English only. Translations are display-only and are never sent to the data sheet.
- **Finish & Submit:** the Finish & Submit button has a colourful, flashing, pulsing glow so students can't miss it. After it is clicked, the sending message reads "Sending your work to your teacher… Please wait. Do not close this window." and keeps a colourful flashing/pulsing glow until sending completes; then it shows the normal sent message (or the usual failure message with a Send again button).
- **Review:** after each activity, a review screen lists every question (correct and wrong) with the student's answer, the correct answer and an explanation, before returning to the homescreen. Completed activities keep a persistent "Review" button on the homescreen opening that same screen.
- **Scores:** shown as fractions of the total (e.g. "6/7").
- **No "Try Again"** and no replay attempts counter; `attempts` is hardcoded to 1.

## WB data submission
Endpoint (Google Sheet "NEW WB DATA", WB Data Collector v3):
```js
var GAME_DATA_ENDPOINT = 'https://script.google.com/macros/s/AKfycbyLjj5ycXstxQyBeTXP6BGKuSqypnchRKtupVwvb4J7z-ydtruHviiomTw6NTcknYKL/exec';
submitGameData({ gameName: 'Your WB Exercise Name Here', studentName: studentName, class: studentClass, score: '8/10, 12/15', minutesPlayed: minutesPlayed, wrongQuestions: wrongQuestionsList, attempts: 1 });
```
- Send as JSON (`fetch` POST, `mode:'no-cors'`, `Content-Type: text/plain;charset=utf-8`, `body: JSON.stringify(data)`), so `wrongQuestions` stays an array.
- Fire once per completed run (duplicate-submission guard; navigating back to the results screen must not resubmit). Multi-activity builds submit once, after every activity is done.
- `gameName` becomes the sheet tab name and must start with "Unit N" (e.g. "Unit 4 WB Vocabulary pp. 36-37") so the Class tabs can group by unit.
- `score`: one fraction per activity, comma-and-space separated (e.g. "3/14, 7/8, 6/7").
- `minutesPlayed`: "MM:SS" (e.g. "02:06"). `attempts`: 1.
- `wrongQuestions`: an **array of lines, one per exercise**, each starting with a short identifier: `"Ex.1: …"`, `"Ex.2: …"`. Leave out an exercise with nothing wrong. Items on a line are separated by `", "`:
  - wrong answer: `label student answer → correct answer` (e.g. `"Ex.2: g ship → boat, e plane → balloon"`); the sheet shows the text after → in bold red, so strip commas from the correct answer.
  - open-ended / AI-checked answer (always sent): `label text [AI ✓ word]` / `[AI ✗]` / `[no AI feedback]` (the sheet colours ✓ tags green and ✗ tags red).
  - Example: `["Ex.1: liner, driver (not found)", "Ex.2: g ship → boat", "Ex.3: 1 It runs on rails. [AI ✓ tram], 2 A big ship. [AI ✗]"]`

## Live external calls (e.g. AI feedback)
- The button that advances to review/results is always clickable and never gated on the external call succeeding.
- The student's raw input for that activity always goes into `wrongQuestions` as plain data (not tagged "wrong"), whether or not the call succeeded.
- After a failed call, the trigger button relabels to a retry (e.g. "Try again for AI feedback"); the review screen shows a plain note in place of the missing result.
- The proxy auto-retries transient errors server-side (up to 2 retries with a short delay) before falling back to the manual retry button.
- While waiting, show loading text telling the student not to close the window.
- Near the finish/submit button, show a clear note that work isn't submitted until they click it.

## Essay HTML rule set
- Based on the WB rule set (widescreen, no "Try Again", attempts = 1, post-activity review screen, persistent Review button, image-matched style, required name/class, translated feedback, glowing Finish & Submit, live-call safeguards above), except pasting (see below). No answer shuffling and no single-attempt lock (not multiple-choice).
- **AI feedback:** Gemini, routed through a separate Apps Script Web App proxy that holds the API key server-side. Never embed an API key in the HTML — GitHub push protection blocks it. The HTML sends only the prompt; the proxy calls Gemini and returns parsed feedback JSON.
- **Pasting:** allowed in Essay builds, but count every paste/drop into the essay box and send the count (`pasted`).
- **Scoring:** against the official Cambridge IGCSE ESL (0510) mark scheme — Content (0–6) and Language (0–9) — with a mark and qualitative comments explaining each band placement. Score format: "X/15 (Content: x/6, Language: x/9)".

## Essay data submission
Endpoint (new Essay data sheet, Essay Data Collector v2):
```js
var ESSAY_DATA_ENDPOINT = 'https://script.google.com/macros/s/AKfycbw4wtgPJ7VzRGWR3zhEtggTOKbb1WpjBCejIrFnkkig3AF7-5S5-SGeMDlHjy8wReayVw/exec';
fetch(ESSAY_DATA_ENDPOINT, { method: 'POST', body: JSON.stringify({
  activity: ACTIVITY_NAME, studentClass: studentClass, studentName: studentName,
  duration: '2min 15sec', score: '11/15 (Content: 4/6, Language: 7/9)',
  feedback: { content: '…', language: '…', overall: '…' },   // English only
  essayResponse: essayText, wordMin: 120, wordMax: 160, pasted: pasteCount
}) });
```
- Fire once per completed run.
- `activity` becomes the sheet tab name and must start with "Unit N".
- `duration`: "Xmin Ysec". `score`: "X/15 (Content: x/6, Language: x/9)", or "N/A (AI feedback unavailable)" when no AI mark came back (the sheet shows "Not marked").
- `feedback`: the AI feedback as `{content, language, overall}` in English (ask Gemini for `contentComment`, `languageComment`, `overall` and their `*_tr` translations; send only the English). Send `''` when there is no AI feedback.
- `wordMin` / `wordMax`: the task's word range. The sheet flags essays more than 10% under the minimum (red) or more than 15% over the maximum (yellow).
- `pasted`: the number of pastes into the essay box (0 if none).
- Never send translations.
