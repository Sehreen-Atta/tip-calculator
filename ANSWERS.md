# ANSWERS.md

## 1. How to run

No build step needed — this is a single HTML file with no dependencies.

```bash
git clone https://github.com/Sehreen-Atta/tip-calculator.git
cd tip-calculator
open index.html         # macOS
# OR
python3 -m http.server 3000   # then visit http://localhost:3000
```

Deployed at: **https://comforting-daifuku-6532bd.netlify.app/**

Full setup details are in README.md.

---

## 2. Stack & design choices

**Stack: Vanilla HTML/CSS/JS — no framework, no build tool.**

A tip calculator has zero state-management complexity — there's no async data, no routing, no component tree. Reaching for React or Vue here would mean adding a build step, a node_modules folder, and 100KB+ of runtime for what is essentially four input fields and some arithmetic. Vanilla JS handles it in ~120 lines with instant startup time and zero configuration. A recruiter cloning the repo and double-clicking `index.html` works immediately with no `npm install`.

**Visual decision 1 — dark background + acid-green accent**

The output panel (right card) uses a near-black background with a bright chartreuse (`#c8f135`) accent. This makes the "per person" number — the one that actually matters to users — pop without needing size alone to establish hierarchy. The large number at the bottom of the output card is the eye's first stop after entering data. I chose this over a light/neutral theme because tip calculators are often used at restaurant tables in dim light, and high-contrast dark UIs are more legible under those conditions. The accent color also makes the active tip preset button immediately visible — users can glance and confirm which preset is selected without reading.

**Visual decision 2 — two-column layout breaking to a single column at 600px**

On desktop, inputs live on the left and results on the right. This allows users to see the output update *as they type* without any scrolling — the two panels are always in peripheral vision at the same time. At 600px and below the layout stacks vertically, inputs on top and results below. I considered putting results on top on mobile (so users don't have to scroll down to see output) but decided against it: users naturally read top-to-bottom, and showing output before inputs would be disorienting. The result panel updates live, so a quick scroll down after entering the bill feels acceptable — and on typical phones at 360px the result panel is still above the fold anyway given the compact input heights.

---

## 3. Responsive & accessibility

**Responsive behavior:**

At **360px** (narrow Android), the layout collapses to a single column. Tip preset buttons stay in a 3-column row (they're short labels) with the custom input spanning the full width below. Font sizes use `clamp()` so the big "per person" number scales gracefully. The input keyboard on mobile is set to `inputmode="decimal"` for the bill and tip fields and `inputmode="numeric"` for the people count, so the correct soft keyboard appears without requiring type="number" quirks on iOS.

At **1440px**, the two-column grid stays constrained to `max-width: 780px` and centered. It doesn't stretch to fill the full viewport — a tip calculator on a wide desktop doesn't benefit from spanning the full width, and wide line-lengths on the input labels would look awkward.

**Accessibility I handled:**

Preset tip buttons have `aria-pressed="true/false"` toggling so screen readers announce the active selection state ("10%, pressed" vs "10%, not pressed"). The results panel has `aria-live="polite"` and `aria-atomic="true"` so screen readers announce the updated totals after the user finishes typing, without interrupting every keystroke. Error messages use `role="alert"` and `aria-describedby` linking each error to its input.

**Accessibility I knowingly skipped:**

I did not implement a high-contrast mode toggle or `prefers-contrast: more` media query. The dark theme with `#c8f135` on near-black passes WCAG AA for large text but the smaller muted labels (`color: #555` on `#0d0d0d`) fail contrast for small text. Fixing this properly would require a separate color ramp for high-contrast mode, which felt out of scope for a calculator. In a production app I would audit with Axe and fix the failing small-text instances.

---

## 4. AI usage

I used Claude (claude.ai) during development in the following places:

**a) Initial HTML structure and ARIA attributes**

I asked Claude to scaffold the basic input structure with correct ARIA (`aria-describedby`, `aria-live`, `role="alert"`). It gave me a solid skeleton. What I changed: Claude's initial output put `aria-live="assertive"` on the results panel, which would have read out every single keystroke as the user typed. I switched it to `aria-live="polite"` with `aria-atomic="true"` so the full updated result is announced only when the user pauses, not on every character.

**b) Rounding logic**

I asked Claude to explain different rounding strategies for bill splitting. It described round-half-up, banker's rounding, and ceiling rounding. I chose `Math.ceil(rawPerPerson * 100) / 100` (ceiling to nearest paisa) specifically because it guarantees the group never underpays — relevant when the bill has to be fully covered. Claude's initial code didn't show the "change to split back" remainder message; I added that myself because ceiling rounding without showing the overage would confuse users ("why is my total Rs 1 more than the grand total?").

**c) Mobile inputmode attributes**

I asked which `inputmode` values to use for number inputs to trigger the right keyboard on mobile. Claude correctly told me `inputmode="decimal"` for the bill/tip and `inputmode="numeric"` for people count. I used this directly without changes.

---

## 5. Honest gap

The custom tip input and the preset buttons can get into a slightly awkward state: if a user clicks "15%" (activates the preset), then clicks into the custom field and types something invalid (e.g. "abc"), then deletes it, the preset still shows as active even though the custom field is now empty and the tip is technically computing from the preset. The intended behavior is that typing anything in the custom field should always deactivate the preset, but the current logic re-uses the last `activeTip` if the custom field is empty. 

With another day I would refactor the tip state into a single source of truth — a `tipState` object with a `mode` field (`'preset' | 'custom' | 'none'`) — and drive both the preset button display and the calculation from that, rather than the current mix of `activeTip` variable and conditional checks. That would make edge cases like "type custom → delete → re-click preset" deterministic and easy to test.
