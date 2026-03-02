# Scene Templates

Nine proven templates that cover most video production needs. Templates live in `src/templates/` and are generic — scenes import them and pass brand-specific content as props.

## Template Index

| Template | Use Case | Key Props |
|----------|----------|-----------|
| FullScreenTitleQuestion | Opening/transition title cards | `badge`, `question` |
| FullScreenCallOut | Bullet point overviews | `title`, `items[]` |
| FullScreenHeadlineSubtext | Bold headline + supporting text | `headline`, `subtext` |
| Quote | Attributed quotes, key statements | `quote`, `attribution?` |
| SplitScreenCallOut | Side-by-side layout with icons | `items[]` |
| PromptDisplay | Short-to-medium text blocks | `prompt` |
| HighlightedPrompt | Long prompts with timed section highlighting | `sections[]` |
| CenterVideo | Embedded video with glow frame | `videoSrc` |
| FullScreenBrandMotion | Logo-only brand moment | (no props) |

---

## FullScreenTitleQuestion

Badge pill at top + large question text below. Perfect for scene openers and transitions.

```tsx
type Props = {
  badge: string;       // "THE SCENARIO", "COMPETITIVE INTEL"
  question: string;    // "What if your competitive intelligence updated itself?"
};
```

**Layout**: Centered vertically. Badge at top with PillBadge styling, question below at 54px.
**Background**: Aurora `bottom`
**Duration**: 3-6s
**Animation**: Badge pops at delay 5, question slides up at delay 12

---

## FullScreenCallOut

Dark card with gradient-icon bullets. For feature overviews, recaps, capability lists.

```tsx
type CallOutItem = {
  icon: string;        // Emoji or symbol
  text: string;        // Bullet text
};

type Props = {
  title: string;
  items: CallOutItem[];
};
```

**Layout**: Full-width card (maxWidth 1600), title at top, staggered bullet rows.
**Background**: Aurora `bottom`
**Duration**: 4-15s (depends on number of bullets)
**Animation**: Card entrance at delay 5, bullets stagger at `10 + i*5` frames

---

## FullScreenHeadlineSubtext

Large bold headline with smaller supporting text. For key statements, section titles.

```tsx
type Props = {
  headline: string;
  subtext: string;
};
```

**Layout**: Centered, headline at 64px, subtext at 28px in lightGray.
**Background**: Aurora `bottom`
**Duration**: 4-8s

---

## Quote

Gradient quotation marks flanking centered quote text. Optional attribution line.

```tsx
type Props = {
  quote: string;
  attribution?: string;   // "— Marketing Lead" or "Slack Alert — 9:00 AM"
};
```

**Layout**: Centered, quote at 42px with `maxWidth: 1100`, gradient `"` marks.
**Background**: Aurora `bottom-right`
**Duration**: 3-8s
**Animation**: Quote marks pop at delay 3, text fades at delay 8

---

## SplitScreenCallOut

Icon + headline + body rows, stacked vertically with left alignment. Good for multi-point explanations.

```tsx
type SplitScreenItem = {
  icon: string;
  headline: string;
  body: string;
};

type Props = {
  items: SplitScreenItem[];
};
```

**Layout**: Left-aligned column, each item: icon (gradient circle) + headline + body text.
**Background**: Aurora `bottom-right`
**Duration**: 10-18s
**Animation**: Items stagger at `5s` intervals — each slides up and fades in

---

## PromptDisplay

Shows a text prompt in a dark card with optional scroll for long content. Auto-sizes for short prompts.

```tsx
type Props = {
  prompt: string;
};
```

**Layout**: Full-width card, monospace-ish display, auto-scroll if content exceeds 940px height.
**Background**: Aurora `bottom-right`
**Duration**: Match to voiceover length
**Auto-sizing**: If content fits, card shrinks to content height (no empty space). If content overflows, card is fixed at 940px with scroll animation and top/bottom fade overlays.

---

## HighlightedPrompt

The most sophisticated template. Shows all prompt text at once, with a walking highlight that moves through sections in sync with voiceover timing.

```tsx
type PromptSection = {
  id: string;
  lines: string[];           // Text lines in this section
  headers?: string[];        // Lines to bold (matched by startsWith)
  startSec: number;          // When highlight begins (seconds)
  endSec: number;            // When highlight ends (seconds)
};

type Props = {
  sections: PromptSection[];
};
```

**Layout**: Full-width card, all text visible at all times, gradient left bar appears next to active section.
**Inactive text**: opacity 0.2
**Active text**: opacity 1.0 (spring animated)
**Left bar**: 3px wide, brand gradient, extends to card edge
**Sound**: Swoosh SFX on each section transition (configurable)
**Background**: Aurora `bottom-right`
**Duration**: Last section's `endSec` + 2-3s buffer

### Section Timing Guidelines

- 3-5 sections per prompt
- Each section: 4-8s duration
- First section starts at 0s
- Use `headers` array to bold section titles
- Empty string `''` in lines creates a vertical spacer

---

## CenterVideo

Video frame centered on screen with colored glow behind it.

```tsx
type Props = {
  videoSrc: string;   // staticFile path
};
```

**Layout**: Centered video with rounded corners, gradient glow behind.
**Background**: Dark, no aurora (the video is the focus)
**Duration**: Match video length

---

## FullScreenBrandMotion

Logo-only scene for brand moments, intros, outros. No text.

**Layout**: Large centered logo with ambient glows.
**Background**: Pure dark with positioned brand-colored glows
**Duration**: 3-5s
**Animation**: Logo scales from 0.8 to 1.0, glows pulse softly

---

## Creating a Scene from a Template

### Simple (template with props)

```tsx
// src/scenes/my-video/S04_PlanMode.tsx
import { Quote } from '../../templates/Quote';

export const S04_PlanMode: React.FC = () => (
  <Quote
    quote="Start in plan mode so the AI can help you execute more efficiently."
    attribution="Best Practice"
  />
);
```

### Custom (unique layout)

```tsx
// src/scenes/my-video/S01b_PainPoints.tsx
import { AbsoluteFill, useCurrentFrame, useVideoConfig, spring, interpolate } from 'remotion';
import { BRAND_COLORS } from '../../brand';
import { BrandBackground } from '../../components/BrandBackground';
import { brandFont, FONT_WEIGHTS } from '../../fonts';

export const S01b_PainPoints: React.FC = () => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  // Custom animation logic...
  return (
    <AbsoluteFill style={{ fontFamily: brandFont }}>
      <BrandBackground aurora="bottom" />
      {/* Scene content */}
    </AbsoluteFill>
  );
};
```

## Template Design Rules

1. **Templates are generic** — no brand-specific content hardcoded
2. **Scenes are specific** — content hardcoded, imports template
3. **One idea per template** — keep them focused
4. **All text uses brand font** — `fontFamily: brandFont` on the root `AbsoluteFill`
5. **All colors from brand.ts** — no hex literals in templates
