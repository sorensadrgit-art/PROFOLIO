# Art-directed project images: preserve meaning across crops

Run: `20261101T200000Z-website-design` · Author: Dot · Evaluated: 2026-11-01.
Status: documentary research; implementation and browser testing not performed.

## Question and incremental value
How can a premium portfolio change its project-image composition on narrow screens without losing the evidence or link purpose the image communicates?

The five existing website-design packages were read before selection: premium-editorial-system-01, website-design-smoke, editorial-grid-lanes-01, the 14:00 headline-wrapping package and the 17:00 contact-form package. The editorial foundation already mentions responsive media. This package adds a specific content-review contract for crops and text alternatives, rather than repeating performance or layout advice. No same-run directory was present at initial inventory.

## Source findings
- **Selection is not cropping.** HTML distinguishes art direction (different image content for different rendered contexts) from size/resolution variants of the same composition. Width-descriptor candidates plus `sizes` inform resource selection; they do not promise a particular candidate in every browser or network state. [S1: HTML Standard](https://html.spec.whatwg.org/multipage/images.html#art-direction)
- **Use native alternatives.** A `picture` contains sources and one `img`; media conditions can select an alternate composition. Presentation belongs to the child image. The image also supplies a fallback when no source is applicable. MDN documents broad availability; this is documentation, not a tested browser matrix. [S2: picture](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/picture)
- **A filled frame can hide evidence.** `object-fit: cover` preserves aspect ratio but clips when frame and image ratios differ. `contain` preserves the entire image and may leave unused bands. `fill` can distort. [S3: object-fit](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/object-fit)
- **Position changes alignment, not meaning.** `object-position` aligns replaced content within its box. [S4: object-position](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/object-position)
- **Alternative text follows context.** WAI distinguishes informative, redundant and decorative images. A complex visual may need equivalent information elsewhere on the page rather than an overloaded short alternative. [S5: decision tree](https://www.w3.org/WAI/tutorials/images/decision-tree/)
- **An image-only link needs a destination/action description.** When text within the same link already supplies that purpose, an image adding no other information can have an empty alternative. This is not a blanket rule that all linked thumbnails are decorative. [S6: functional images](https://www.w3.org/WAI/tutorials/images/functional/)

## Original synthesis: a crop contract
Treat a project image as a content component before treating it as a rectangle. Proposed fields: asset identifier, rights evidence, contextual purpose, indispensable visible details, wide and narrow compositions, fallback composition, alternative text, optional long description, review owner and revision. These fields are a suggested editorial schema, not an existing product feature.

| Situation | Proposed decision | Failure to prevent |
|---|---|---|
| Same scene remains legible at every width | One composition, resolution candidates | Adding needless crop maintenance |
| Secondary scenery overwhelms the project on narrow screens | Authored narrow composition through picture | Cutting out the feature that supports the case study |
| Screenshot/diagram proves a technical outcome | Preserve the full evidence or provide a separate readable detail with explanation | A beautiful crop that changes the claim |
| Thumbnail merely repeats a title in the same link | Review whether empty alt is appropriate | Repeated link names |
| Image is the only link content | Supply the destination purpose | An unnamed or misleading navigation target |

**Editorial invariant (proposal):** every candidate must still support the same contextual description. If the wide view shows a team collaboration and the narrow view only a product, a shared description may become misleading. Either choose a different crop or restructure the content with explicit separate explanations. This invariant is our design recommendation, not a quoted normative rule.

A focal point alone is insufficient: it locates one subject but cannot guarantee two important features survive every aspect ratio. Prefer an explicit narrow asset when preserving the composition requires editorial judgment. Do not assume CSS clipping reduces transfer bytes; resource selection and visual framing are separate concerns.

## Implementation, alternatives and costs
This pattern uses HTML and CSS; no JavaScript library, package dependency, API key, MCP server, account or new service is required. It can be rendered by a static site or a framework, but no React/Next.js image wrapper integration was assessed. Confirm any wrapper preserves the intended source and image semantics before adopting it.

Start with one composition. Alternatives are `contain` for full-image evidence, a separately authored crop with `picture`, or a distinct detail figure with its own explanation. Do not add a JavaScript viewport detector merely for this decision. Existing image hosting can serve authored assets; storage, transfer, editorial review and extra derivative files remain costs. No provider prices or byte savings were measured.

No repository/tool licence is being recommended. Browser-native features require no bundled third-party implementation here. Source documentation is linked, not relicensed; this package reproduces no documentation examples or protected artwork. Publicly visible portfolio imagery is not permission to copy it. Asset-specific licences, attribution, client consent and crop/derivative restrictions must be checked separately; no actual asset rights were inspected.

## Proposed acceptance exercise — NOT EXECUTED
Use original or properly licensed fixtures: a two-subject scene, a screenshot with edge labels, a redundant linked thumbnail and an image-only link.
1. Inspect wide/narrow layouts, intermediate widths, zoom and orientation changes; check that required evidence remains visible.
2. Inspect chosen image URL and network transfers separately from visual crop. Do not infer savings from a smaller frame.
3. Review each candidate against the shared alternative and surrounding text. Check link names with assistive technology.
4. Disable image loading and confirm useful purpose/content remains; inspect fallback behavior with the target browser matrix.
5. Record actual asset dimensions and rendered sizes, reserve appropriate space, and measure layout shift rather than assuming none.

Future application: a CMS crop-review checklist could reject publication until each composition has an editorial approval and rights record. A visual regression gallery could show all candidate/frame combinations. Neither system was implemented.

## Limits
Six documentation sources were evaluated on 2026-11-01. No repository code, npm release, browser runtime, assistive technology, real imagery, licence grant for an image, hosting invoice or production integration was tested. The web.dev picture page was inaccessible through the retrieval tool and was excluded as evidence. Documentary approval is not deployment permission or accessibility certification.

