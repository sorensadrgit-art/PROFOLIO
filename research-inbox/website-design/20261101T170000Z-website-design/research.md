# A premium contact form must explain failure, not erase the conversation

Run: 20261101T170000Z-website-design  
Scheduled: 2026-11-01T17:00:00Z (12:00 America/New_York)  
Evaluation: 2026-11-01

## Question and gap

How can a restrained editorial contact form remain understandable when submission fails, without replacing clear labels and recoverable errors with decorative feedback?

The live catalog had 23 packages and four website-design packages when inspected. All four website references were read: the editorial foundation, forced colors, Grid Lanes and headline wrapping. The topic directory contained no same-run package or unfinished package. This addendum narrows the foundation's brief contact-form recommendation into a feedback-state contract, not another visual-style survey. It is separate from backend write-consistency research: no endpoint implementation is proposed.

## Source-backed findings

- **S1 — Names stay attached to controls.** WAI recommends explicit label association where possible: label `for` matches control `id`. A label provides a larger activation area and supplies an accessible control name. Context can sometimes justify a visually hidden label, but hiding it is not the general default. [Labeling Controls](https://www.w3.org/WAI/tutorials/forms/labels/)
- **S2 — Recovery needs local and overall information.** WAI recommends clear submission feedback. An error list should identify the corresponding field, explain correction and link to the control; `aria-describedby` can associate a local error. The tutorial distinguishes checking after submission, during typing and after focus changes rather than prescribing one validation moment for every field. [User Notification](https://www.w3.org/WAI/tutorials/forms/notifications/)
- **S3 — Feedback need not steal focus.** The WCAG 2.2 explanation of 4.1.3 covers programmatically identifiable status messages that do not change context. A focused dialog is a different interaction, not the universal mechanism for ordinary status. “Waiting” and completion are distinct states; simply removing busy text may not communicate completion. Understanding documents are informative, not a separate conformance standard. [Status Messages](https://www.w3.org/WAI/WCAG22/Understanding/status-messages.html)
- **S4 — Input type is not input purpose.** The explanation of 1.3.5 distinguishes a broad type such as email from the purpose of collecting the user's email. Defined autocomplete tokens can expose purpose for applicable personal-data inputs. Actual browser autofill is not the conformance test; arbitrary project-description fields do not acquire a made-up autocomplete token. [Identify Input Purpose](https://www.w3.org/WAI/WCAG22/Understanding/identify-input-purpose.html)

## Original design proposal: persistent context, modest feedback

Use a compact visible label above each field, a short instruction when necessary and a reserved flow location for feedback. Let error text increase the component height instead of clipping it to preserve an ideal screenshot. Keep the message the visitor wrote in the current form when validation fails. These are proposed product decisions, not measured conversion findings.

Keep these states separate:

| State | Suggested visible feedback | Proposed behavior |
|---|---|---|
| Editing | Labels and any needed instructions | Do not classify incomplete prose as an error on every keystroke |
| Rejected fields | Summary plus field-specific explanation | Keep entered values; provide a clear path to each affected control |
| Pending request | A short waiting message | Prevent accidental duplicate actions without claiming completion |
| Confirmed acceptance | Precise acknowledgement of what the service confirmed | Do not claim a person read the message or that email delivery succeeded |
| Unknown outcome / network failure | Explain uncertainty and available next step | Preserve the draft; do not disguise ambiguity as a validation error |

The acceptance-versus-delivery distinction is an original UI contract recommendation. This run did not inspect or test a server, message queue or email provider. Backend guarantees require their own evidence.

## Small original markup sketch — not executed

```html
<label for="contact-email">Your email</label>
<input id="contact-email" name="email" type="email"
       autocomplete="email" aria-describedby="email-hint">
<p id="email-hint">Use an address where you can receive a reply.</p>
<p id="submission-status" role="status"></p>
```

This is only field identity and a candidate status region, not a working form. A future implementation must render and associate actual errors, choose notification/focus behavior, validate requests server-side, and preserve data appropriately. Do not announce the entire form every time one value changes. Test the chosen announcement strategy with actual assistive technology; this sketch proves no browser or screen-reader behavior.

## Debugging and future acceptance

| Symptom | Inspect | Proposed future check |
|---|---|---|
| Field purpose disappears after typing | Placeholder-only design or hidden label | Name remains visible and programmatically associated |
| Error is visible but not discoverable | Summary, association and focus strategy | Keyboard reaches affected field; error is understandable |
| Busy spinner vanishes silently | Pending-to-result message transition | Completion or uncertainty communicated without contradictory announcements |
| Retry clears a long enquiry | State reset before confirmed outcome | Synthetic draft survives validation and transport failure |
| Autofill behaves unexpectedly | Purpose tokens and browser heuristics | Inspect exposed purpose separately from whether autofill occurs |
| Error copy breaks the composition | Fixed heights and narrow layouts | Long localized errors remain readable under enlargement |

These tests are not executed and do not constitute a complete accessibility or security audit.

## Adoption and limits

Existing HTML/React/Next components can express this proposal without a new package, MCP connection or paid service; framework integration was not tested. Prefer a native form baseline over an animation-led overlay unless a concrete requirement justifies additional interaction complexity. Server processing, abuse prevention, retention and privacy notices remain separate design work; never put enquiry contents into public research artifacts or diagnostic URLs.

Four W3C primary documentation pages were read. The notification tutorial reports an update of 2022-06-03; unobserved revision dates and commits remain null. No third-party assets or substantial source text are copied. No install, deployment, live form submission, runtime test, usability study, benchmark or accessibility certification occurred. Approval means bounded documentary review only.

