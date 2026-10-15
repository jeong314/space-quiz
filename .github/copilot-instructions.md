# Orbit space quiz

## Runtime and browser checks

The application must remain a single, self-contained `index.html` that opens directly through a `file://` URL. Keep CSS, JavaScript, SVG artwork, and assets inline; preserve operation without a server, dependencies, or network requests.

Open the page from the project directory with PowerShell:

```powershell
Start-Process .\index.html
```

For the integrated browser, open the absolute file URL and reload after editing. Read the page before clicking so element references are current.

To check one question, start a fresh quiz, select Neil Armstrong on question 1, and verify the score becomes `1 point`, the answer turns green, an explanation appears, and Next becomes enabled. In a separate fresh run, select Buzz Aldrin and verify the score stays `0 points`, the selected answer turns red, and Neil Armstrong is revealed in green.

For an end-to-end smoke check, complete all 10 questions with both correct and incorrect selections. Verify answered options are disabled, repeat selections cannot add points, progress reaches 100%, and results match the score and answer-history bars. Check the emoji tiers at scores 0, 4, 7, and 10, then verify replay resets score, progress, feedback, and question position.

Check native Tab, Enter, and Space interactions when browser tooling supports them. Programmatic `focus()` and `click()` checks establish focus management and activation behavior, but do not establish native keystroke behavior.

## Application structure

`index.html` contains theme and layout CSS, static quiz/results markup, then a plain JavaScript script. The `questions` array holds 10 ordered objects with `category`, `question`, four `answers`, a zero-based `correct` index, and an explanatory `fact`.

Runtime state is `current` (zero-based question position), `score` (one point per correct answer), `answered` (guards against repeat submissions), and `history` (one boolean per answered question). State lives only in memory and starts fresh on page reload.

`renderQuestion()` rebuilds answer buttons and clears question-local feedback. `selectAnswer()` locks all options, records the outcome, reveals the correct answer, updates score/progress, and enables Next. `updateProgress()` measures answered questions using `history.length`, not the current question position. Next advances explicitly; the final Next calls `showResults()`. Replay resets state and renders question 1.

The results reaction thresholds are perfect score, at least 7, at least 4, and below 4. Several labels, the progress maximum, and percentage calculation assume exactly 10 questions; keep that contract consistent if changing quiz length.

## UI and accessibility conventions

- Theme colors use CSS custom properties in `:root`, overridden by `prefers-color-scheme: dark`. Retain both themes, the centered 510px maximum-width column, 15px sans-serif body, and the 420px mobile breakpoint.
- Scope mission-control question styling to `#question`; results also use an `h2` and should not inherit the console treatment.
- Correct answers use `.correct` and `correct-pop`; incorrect selections use `.wrong` and `shake`. Feedback uses `.good` or `.bad`. Preserve the `prefers-reduced-motion` override for animations and transitions.
- Answer choices are native buttons grouped with `aria-labelledby="question"`. Disabled answers leave the tab order. After answering, focus moves to Next; advancing or replaying focuses the question heading; results focus their heading. Keep heading `tabindex="-1"` and visible focus outlines.
- Score and feedback use polite live announcements. Correctness is conveyed with text/ARIA labels and check/cross marks in addition to color. Results emoji have an accessible label; decorative artwork and answer letters are hidden from assistive technology.
- Toggle quiz/results using `hidden`; the `[hidden]` CSS rule ensures hidden panels stay out of layout. Build question content with `textContent`, `createElement`, and `replaceChildren`; existing `innerHTML` usage is limited to application-controlled numeric markup.
