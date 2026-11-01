# Editorial headlines that survive real text: wrapping is a layout contract

Run: `20261101T140000Z-website-design`  
Scheduled: 2026-11-01 09:00 America/New_York (14:00 UTC)  
Evidence retrieved: 2026-11-01

## Question and material gap

How can an editorial portfolio retain deliberate headline composition when user spacing, narrow layouts and long project names invalidate its ideal line breaks?

The live catalog contained 15 packages at inspection. Its two website-design references cover the broad editorial system and forced-color affordances. This package adds a bounded failure analysis of text geometry rather than another visual-style survey. The topic directory contained those two packages plus its README; no same-run package was present. A prepublication reread found a new third website package, `20261101T135953Z-editorial-grid-lanes-01`, and 16 total catalog entries. That package addresses grid algorithms and reading order; it was read and preserved. The present question remains distinct: user-overridden text geometry, balanced wrapping and emergency word breaks. This is documentary research, not an implemented accessibility audit.

## Primary-source findings

**S1 — Overrides are an acceptance condition, not mandatory art direction.** WCAG 2.2 Understanding Text Spacing explains that supported markup must tolerate user adjustments without losing content or functionality: line height at least 1.5 times font size, paragraph spacing at least 2 times, letter spacing at least 0.12 times and word spacing at least 0.16 times. These are not required authored defaults. Applicable language/script exceptions remain relevant. Understanding documents are informative explanations of the criterion. [W3C Text Spacing](https://www.w3.org/WAI/WCAG22/Understanding/text-spacing.html)

**S2 — A narrow viewport is more than a mobile screenshot.** Understanding Reflow describes the AA requirement for vertically scrolling content at a width equivalent to 320 CSS pixels, without information/function loss or two-dimensional scrolling. Necessary two-dimensional content has exceptions; an ordinary editorial heading is not automatically exempt because its design is dramatic. The document relates 1280 CSS pixels at 400% zoom to that width. [W3C Reflow](https://www.w3.org/WAI/WCAG22/Understanding/reflow.html)

**S3 — Balance is an enhancement with limits.** MDN documents `text-wrap` as a shorthand for wrapping mode and style. `balance` improves line distribution but has engine-specific limits on the number of lines; the retrieved documentation names six for Chromium and ten for Firefox. Its feature-level availability banner explicitly warns that parts have differing support. Do not translate that banner into a universal promise for every keyword or browser. [MDN text-wrap](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/text-wrap)

**S4 — Breaking a long token also affects intrinsic sizing.** MDN distinguishes `overflow-wrap: anywhere` from `break-word`: both can break an otherwise unbreakable string when needed, but only `anywhere` includes those introduced opportunities in min-content sizing. Neither inserts a hyphen. That difference matters when a long project identifier influences a component's minimum width. [MDN overflow-wrap](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/overflow-wrap)

## Original design synthesis: separate three responsibilities

1. **Content owns meaning.** Preserve the full project name as actual text and maintain a meaningful heading hierarchy. Do not shorten a unique name merely because its ideal screenshot used two lines.
2. **Layout owns accommodation.** Let the text block grow and the next section move. A reserved two-line height plus hidden overflow is a brittle content policy disguised as visual polish.
3. **Typography owns refinement.** Balanced wrapping and a measured text width improve the preferred composition, but their absence must not conceal content. A designer-approved three-line result is better than a clipped two-line result.

These are implementation recommendations inferred from the documented failure conditions, not measured preference or conversion findings. The earlier editorial-system note already recommends fluid sizing; this addendum addresses what happens after that preferred size encounters inconvenient content.

## Original CSS sketch — not executed

```css
.project-heading {
  max-inline-size: 18ch;
  margin-block: 0 0.6em;
  line-height: 1.1;
  overflow-wrap: anywhere;
}

@supports (text-wrap: balance) {
  .project-heading { text-wrap: balance; }
}
```

The `18ch` measure and `1.1` authored line height are starting design choices, not conformance thresholds. They must tolerate user overrides. This sketch intentionally omits fixed block height, clipping and forced line breaks. It does not reset the entire stylesheet or establish a complete responsive type scale. A surrounding grid/flex layout may still impose an incompatible minimum width; inspect its track and item sizing instead of assuming this selector repairs every ancestor.

Use emergency wrapping selectively for long names/identifiers. It can visibly divide an unfamiliar name, so review the actual result; it is not language-aware hyphenation. Do not apply arbitrary breaking to all prose merely to make a single pathological identifier fit. Balanced wrapping cannot compensate for a container that refuses to shrink.

## Debugging order and future acceptance matrix

| Failure | First inspection | Proposed correction | Future verification |
|---|---|---|---|
| Last line disappears after spacing override | Fixed height, overflow clipping, line clamping | Remove the content cap; allow surrounding flow to expand | All four spacing overrides together, no loss of words/actions |
| One long project name widens the page | Token wrapping and ancestor minimum sizing | Consider emergency wrapping plus appropriate component sizing | Long synthetic token at 320 CSS-pixel equivalent width |
| Heading changes between engines | Balance support/line limits and actual font metrics | Accept ordinary wrapping as the baseline | Record browser versions and compare readable layouts |
| Art-directed line breaks become awkward in translation | Hard-coded breaks and narrow width assumptions | Prefer natural wrapping; review language-specific composition | Long translated sample and native-language review |
| Mobile looks correct but zoom fails | Desktop zoom path and sticky/absolute positioning | Test enlargement independently of a mobile preset | 1280-wide viewport at 400% zoom, with exceptions documented |

Run these checks on the real page before adoption. Include unusually long names, short names, an unavailable preferred font and mixed-script samples; font substitution and multilingual behavior are proposed test inputs, not verified results here. Distinguish the spacing test from the reflow test in recorded evidence. A single screenshot cannot establish both, and this matrix is not a complete WCAG audit.

## Integration and tradeoffs

The proposal uses CSS and existing semantic HTML. React or Next.js can retain their current component structure; no new package, server, MCP service, installation or paid operating service is required by this pattern. Framework behavior was not exercised. The main cost is review across actual content, languages and browsers, not a new infrastructure dependency.

Alternatives include manually art-directed breaks per content item or a measured JavaScript text-layout system. Both increase editorial or runtime responsibilities; neither removes the need to support resizing and overrides. Start with native flow and only investigate those alternatives for a demonstrated requirement. No benchmark compares them in this run.

## Evidence limits and publication meaning

Four primary documentation sources were read. WCAG version is 2.2; MDN pages are living references without a pinned software release. No exact documentation revision, release date or commit was verified, so those fields remain null in the source register. Browser limits are source-reported, not independently tested. Re-check support before implementation.

No third-party assets, fonts, repositories or substantial source text are reproduced. Original synthesis and the short CSS sketch are not a licence grant for external material. No application files changed; no browser execution, performance benchmark, accessibility certification or usability study was performed. Approval means bounded documentary QA only.
