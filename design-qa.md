# Design QA

## Comparison target

- Source visual truth: Figma node [`1874:2912`](https://www.figma.com/design/PY7TrkajaPqPCxAUJY3RKa/门户项目?node-id=1874-2912), course information card, 936 × 449 px.
- Implementation: [task-panorama-portal.html](/Users/huangwen/Documents/ChatGPT/大象学堂门户%202/task-panorama-portal.html), rendered in the in-app browser at `http://127.0.0.1:8765/task-panorama-portal.html`.
- Viewport/state: 1935 × 817 CSS px, desktop initial state, device scale factor 1. This produces the 1680px content width needed to inspect the original 936px card and 720px map at 1:1; the browser was restored to its default viewport after verification.
- Evidence: Figma screenshot from `get_screenshot` and browser screenshot captured after the CSS/markup fix.

## Focused comparison

- Card frame: source 936 × 449 px with the supplied card-shape asset; implementation keeps that asset as the visible outline and applies the Figma `100px` backdrop blur through an exact SVG silhouette mask, plus the Figma drop shadow on `::before`.
- Title: source x=32, y=32, 40/48 heavy text; implementation measured at the same relative offset and 40px/48px at the active preview breakpoint.
- Course kicker: source x=712, y=32, 192 × 32, 24px white heavy text; implementation is positioned from the right at the same dimensions.
- Facts: source two rows of 46px with 12px row gap, six 32px icon circles and 18px source icons; implementation uses six local Figma SVGs inside 32px circles and matching separators.
- Summary: source x=32, y=224, width=872, height=113, 16px regular text on `#edf1f8`; implementation matches the relative geometry and token.
- CTA: source x=32, y=361, 148 × 56, 18px semibold text and supplied arrow asset; implementation keeps the asset and applies the Figma transform orientation.
- Map chip: `5 项课程任务` is regular weight (400), not bold.

## Required fidelity surfaces

- Fonts/typography: title 40/48 heavy, kicker 24/32 heavy, facts 14/22 + 16/24, summary 16px regular, CTA 18px; map task-count text is explicitly regular weight.
- Spacing/layout: 32px card padding, 24px title-to-facts gap, 12px facts row gap, 16px facts-to-summary gap, 24px summary-to-CTA gap.
- Colors/tokens: card surface remains transparent at the wrapper level; blur/opacity are clipped to the card silhouette so the ribbon and page background no longer bleed through sharply.
- Image/assets: six course fact icons and CTA arrow use the supplied local Figma SVG assets; no generic icon replacement remains in the scoped region.
- Copy/content: all labels, values, kicker, summary, and task-count chip match the provided design/comments.

## Comparison history

1. [P1] Removed the extra hero-copy background; retained the Figma card-shape asset as the visible outline.
2. [P1] Corrected the kicker to 24px/32px and matched its right-aligned placement.
3. [P2] Replaced direct icon styling with 32px Figma icon circles containing 18px source SVGs for all six facts.
4. [P2] Corrected CTA arrow orientation with the Figma transform.
5. [P2] Set `5 项课程任务` to regular weight 400.
6. Post-fix browser evidence: viewport renders without console errors; measured geometry matches the source-relative offsets above.
7. [P1] SVG `foreignObject/backdrop-filter` did not execute when loaded as an image. Rebuilt the blur as a local transition layer positioned only beneath the upper-right blue ribbon's curved overlap, while preserving the sharp ribbon and original card fill/border above it. This matches node `1874:2913` without blurring the full white course card.

## Findings

No actionable P0, P1, or P2 mismatches remain in the annotated course-card and task-count surfaces.

## Follow-up polish

- P3: The Figma font renderer may differ slightly from the browser's local font fallback at the anti-aliasing level; the measured type sizes, weights, line heights, and positions match.

final result: passed

## Knowledge Graph Three.js Rendering Update

- Source visual truth: authenticated in-app-browser capture of [`eval-dashboard.html`](https://cxoa.chaoxing.com/aibase/_/g8jxzx3zjg/eval-dashboard.html), `Three.js r185` canvas at 1230 × 444 CSS px / 2460 × 888 device px (DPR 2), default state.
- Implementation: browser-rendered [task-panorama-portal.html](/Users/huangwen/Documents/ChatGPT/大象学堂门户%202/task-panorama-portal.html) at `http://127.0.0.1:8799/task-panorama-portal.html#graph`, 732 × 600 CSS-pixel graph canvas / 1464 × 1200 device px (DPR 2), default and level-one-selected states.
- Full-view evidence: the source and implementation captures were reviewed at the same desktop browser viewport. The comparison is scoped to the graph canvas because the portal intentionally has a different shell, node dataset, and card layout.
- Focused comparison: both canvases use Three.js r185 `SphereGeometry(48, 32)`, `MeshPhysicalMaterial`, source light rig, ACES Filmic tone mapping (1.08), translucent additive halo, and a black `TorusGeometry` selection outline. The local graph uses the source's 22° perspective projection and world-coordinate conversion, rather than pixel-positioned lighting.

### Comparison history

1. [P1] The first local r185 import failed because the r185 renderer entry imports `three.core.js`. Fix: localized the official matching `three.core.js` module beside the official entry module.
2. [P1] The initial implementation placed meshes in pixel coordinates under a pixel-space orthographic camera. Its point lights were effectively out of range, making the center sphere too dark and unlike the source. Fix: moved the portal map into the source's world-space/perspective camera model, preserving the source light positions and fog calculation.
3. [P2] The center vertex colors were blended in RGB and terminal nodes used orange. Fix: applied the source's HSL-interpolated root color logic and the requested hierarchy colors: blue `#1677FF`, green `#00B578`, pink `#F05AAF`.
4. Post-fix evidence: the local canvas identifies itself as `three.js r185`, renders at DPR 2 with no browser console errors, and the selected `问题定义` node updates the detail card while showing the black torus outline. No toast element was created.

### Required fidelity surfaces

- Fonts and typography: labels retain the source's PingFang/Microsoft YaHei stack, dark `#0D1014` color, 600 weight, and white multi-shadow legibility treatment.
- Spacing and layout rhythm: the portal keeps its intentional node coordinates and detail panel; within every node, WebGL geometry follows the DOM node's measured center and radius so drag and zoom remain aligned.
- Colors and visual tokens: source material roughness, metalness, clearcoat, transmission, emissive treatment, 1.28/1.35 halo scale, and black `#0D1014` selection ring are used. Hierarchy colors follow the explicitly requested blue/green/pink mapping.
- Image quality and asset fidelity: the component is renderer-native WebGL, with no screenshot, iframe, or raster replacement. Official Three.js r185 modules are local assets.
- Copy and content: existing portal node labels and detail-card content are preserved, including their existing node-to-detail linkage.

### Findings

No actionable P0, P1, or P2 mismatches remain in the migrated sphere renderer. The portal's graph topology, labels, and right-hand detail card intentionally differ from the source dashboard and were preserved by request.

### Follow-up polish

- P3: The source dashboard uses its own dynamic force/network layout; the portal intentionally retains its established draggable fixed layout.

final result: passed

## Teacher Card Fidelity Update (2058:12865)

- Replaced the previous approximate portrait treatment with the Figma card mask (`team-card-mask.svg`) and the exact bottom overlay (`team-card-overlay.svg`).
- Localized all five Figma portrait assets and mapped the ten cards in the same sequence as the source frame.
- Restored the measured 310.4 × 358.154px portrait frame, 47.754px top offset for the gradient surface, 32px role pill, 32px card title, and 16px card-to-copy spacing.
- The teacher section retains the source 5-column / 2-row arrangement at desktop widths and responsive reductions at narrower widths.

final result: passed

## Teaching Run Metric-Card Alignment Correction

- Source visual truth: the Figma metric information column is `83px` tall. Two-item cards place 24px rows at `11.5px` and `47.5px`; the materials card uses a two-column first row and a second-row item at `9.5px` and `49.5px`.
- Corrected implementation: removed the upward `11px` padding offset, made every right information column a border-box `83px` grid with centered content, and restored the material card's `16px` two-column/row-gap grid.
- Verification: reloaded `http://localhost:4173/task-panorama-portal.html#data`; all four right information columns measure `83px` tall and their rendered item coordinates match the Figma values above.

final result: passed

## Teaching Run Statistics

- Source visual truth: Figma node [`2058:11634`](https://www.figma.com/design/PY7TrkajaPqPCxAUJY3RKa/%E9%97%A8%E6%88%B7%E9%A1%B9%E7%9B%AE?node-id=2058-11634), with focused chart nodes `2058:12178` and `2058:12216`.
- Implementation: [task-panorama-portal.html](/Users/huangwen/Documents/ChatGPT/大象学堂门户%202/task-panorama-portal.html), rendered at [http://localhost:4173/task-panorama-portal.html#data](http://localhost:4173/task-panorama-portal.html#data).
- State and viewport: desktop default data state at 1280 CSS px wide. Source capture was 1024 x 715 px from Figma (natural node size 1920 x 1340 px); the implementation was inspected at the same unselected, desktop chart state using focused browser captures of `#data`.
- Full-view evidence: Figma node screenshot and rendered `#data` capture were inspected for the full metrics-to-charts composition. Focused captures covered both vertical-bar cards and the horizontal-bar plus five-piece donut cards. Browser console: no warnings or errors.

### Comparison history

1. [P1] The teaching-run wrapper had a white fill even though the requested module is backdrop-free. Fix: set `#data` to `background: transparent`; individual translucent statistic cards remain unchanged.
2. [P1] The donut's upper-right green segment had been narrowed from its Figma vector geometry. Fix: use the Figma 246.96px rotated canvas and its 96.28 x 135.82px vector slot.
3. [P2] Vertical bars used slightly different track and gradient tokens. Fix: `#e9ecf5` track and `#085fff` to `#a460ff` fill now match the Figma node.
4. [P2] Horizontal tracks used a non-source gray. Fix: set the track to `#e9ecf5`; retained the 60px bar height, 1.5px divider, and 395/265/500/394px data widths from the Figma layer structure.
5. Post-fix evidence: both focused browser captures show the corrected four charts with no remaining actionable P0/P1/P2 drift.

### Required fidelity surfaces

- Fonts and typography: chart labels remain 16px PingFang SC semibold; vertical values use 24px Roboto Black; donut total remains 36px/44px Roboto Black.
- Spacing and layout rhythm: chart content is 764 x 288px; vertical columns are 60px wide with a 40px gap; horizontal rows are 72px with 60px bars; donut/legend gap is 48px.
- Colors and visual tokens: vertical track `#e9ecf5`, fill `#085fff` to `#a460ff`; horizontal fills `#ff8126`, `#e94ca5`, `#2468f2`, `#26a555`; donut legend and arcs match the exported Figma vectors.
- Image quality and assets: the donut uses five local Figma-exported SVG arc assets at their measured slots, with no reconstructed replacement.
- Copy and content: all labels and figures match the selected Figma state, including `课程任务 / 课程资料 / 考试 / 题库` and `51` total questions.

### Findings

No actionable P0, P1, or P2 mismatches remain in the requested teaching-run statistics.

final result: passed

## Knowledge Graph Detail Card

- Source visual truth: Figma node [`2045:489`](https://www.figma.com/design/PY7TrkajaPqPCxAUJY3RKa/%E9%97%A8%E6%88%B7%E9%A1%B9%E7%9B%AE?node-id=2045-489), 480 x 600 px course-detail card.
- Implementation: [task-panorama-portal.html](/Users/huangwen/Documents/ChatGPT/大象学堂%E9%97%A8%E6%88%B7%202/task-panorama-portal.html), checked at `http://127.0.0.1:8765/task-panorama-portal.html#graph` in the in-app browser.
- Default state: Figma's full information set is present in the same order: course-center chip, `产品设计基础`, `18` bound resources, `0` bound questions, `已发布` state, divider, and the scrollable lower-knowledge-point list.
- Geometry: the right detail card is 480 x 600 px with 32px padding, 40px radius, three equal statistic tiles, and the lower-list viewport constrained to the design's scrollable area.
- Scope: the left graph canvas and its existing node/edge linkage were retained. Browser interaction verification confirmed that selecting the foundation node updates its detail content and connected-edge emphasis, then the course default state was restored.

## Findings

No actionable P0, P1, or P2 mismatches remain for the knowledge-graph detail card.

final result: passed
## Teacher Team Design QA

final result: passed

- source visual truth: Figma `PY7TrkajaPqPCxAUJY3RKa`, node `2058:12865`
- implementation: `http://127.0.0.1:8796/task-panorama-portal.html#team`
- viewport: Codex in-app browser, 1250 x 705 CSS px; source reference rendered at 1024 x 614 px
- state: default page, teacher-team anchor active
- full-view evidence: implementation visibly shows the title, transparent section surface, 5-column first row, Figma-sourced portrait assets, gradient portrait masks, black role pills, and 10 total cards

### Fidelity surfaces

- Fonts and typography: title uses the existing Chinese system stack with 44px/52px weight 900; card role and description hierarchy follows the Figma 16px and 20px values.
- Spacing and layout rhythm: 60px section top/bottom padding, 32px title-to-grid gap, 32px card gap, 358.154px portrait frame, 16px image-to-subtitle gap, and 8px subtitle-to-description gap match the source node.
- Colors and visual tokens: portrait gradient uses `#085fff`, `#a460ff`, and `#ca60ff`; role pills use `#101114`; team section background remains transparent as requested.
- Image quality and asset fidelity: all 10 portraits use the Figma MCP image assets from the source node; no placeholder portraits remain.
- Copy and content: title, role labels, card headings, and research descriptions are present and repeated to match the supplied design content.

Residual note: the teacher portrait, mask, and overlay assets are now stored locally under `assets/figma-teaching-run/team/`; no expiring remote asset is required for this module.

## Knowledge Graph Annotation QA

- Source visual truth: Figma node [`2045:489`](https://www.figma.com/design/PY7TrkajaPqPCxAUJY3RKa/%E9%97%A8%E6%88%B7%E9%A1%B9%E7%9B%AE?node-id=2045-489), captured through the Figma design-context response at its native 480 x 600 CSS-pixel frame.
- Implementation: [task-panorama-portal.html](/Users/huangwen/Documents/ChatGPT/%E5%A4%A7%E8%B1%A1%E5%AD%A6%E5%A0%82%E9%97%A8%E6%88%B7%202/task-panorama-portal.html), rendered at `http://127.0.0.1:8765/task-panorama-portal.html#graph` in the in-app browser.
- State: default course detail record, `产品设计基础` with the full repeated child-knowledge-point list.
- Focused evidence: browser-rendered DOM geometry confirmed all four local Figma SVGs loaded at 16 x 16 px; the first child tag measured 98 x 42 px; the list viewport measured 416 px wide; and the card-owned scroll thumb measured `right: 8px`, `top: 326px`, 12 x 120 px.

### Comparison history

1. [P1] The chip and three statistic labels used generic Lucide glyphs. Fix: replaced them with the four exact Figma SVG exports, stored locally in `assets/figma-knowledge-graph/` so the implementation does not depend on expiring MCP URLs.
2. [P2] Child tags did not preserve the source's fixed frame and regular text treatment. Fix: applied the 98 x 42 px flex-centered frame, 16/24 PingFang regular, `#dfe4ed` border, 12 px radius, and `#4e535f` text token.
3. [P2] Statistic captions inherited the old compact 11px muted style at the narrow breakpoint. Fix: explicitly retain Figma's 16px/24px regular `#4e535f` caption token at every breakpoint.
4. [P2] The native scrollbar's narrow in-list placement differed from the source. Fix: visually hide the native scrollbar while retaining scrolling, then render the design-owned 12 x 120 px thumb at the specified card-relative location; it tracks list scroll position.

### Required fidelity surfaces

- Fonts and typography: child tags use 16px/24px regular PingFang SC; source chip and statistic text retain their measured 16px/24px treatment.
- Spacing and layout rhythm: tags are 98 x 42px with 8px gaps, filling four columns across the 416px content width; the fixed design thumb is independent from the content width.
- Colors and visual tokens: tag border `#dfe4ed`, text `#4e535f`, thumb `#dadde5`.
- Image quality and asset fidelity: course-center, resource, question, and status glyphs are the exact local Figma 16px SVG exports.
- Copy and content: all labels and values remain unchanged.

### Findings

No actionable P0, P1, or P2 mismatches remain in the four annotated surfaces.

final result: passed

## Teaching Run Bar-Segment Correction

- Source visual truth: Figma node [`2058:12178`](https://www.figma.com/design/PY7TrkajaPqPCxAUJY3RKa/%E9%97%A8%E6%88%B7%E9%A1%B9%E7%9B%AE?node-id=2058-12178) defines each horizontal row as three sibling layers: color block, 1.5px `#525766` divider, and independent `#e9ecf5` gray block.
- Corrected implementation: the two vertical charts now render an independent top gray segment, a 1.5px divider, and a separate gradient segment. The four horizontal rows now render independent gray segments after their color and divider layers. The previous continuous gray backdrop was removed.
- Verification: reloaded `http://localhost:4173/task-panorama-portal.html#data`; the page contains 8 `.status-track` and 4 `.exam-track` elements. The chart meter parents and `#data` both compute to transparent backgrounds. The measured first chart segments are 126.5/70.5/30.5/70.5px gray above their 84/140/180/140px gradient fills, and the horizontal gray segments are separate after the `1.5px` divider.

final result: passed
