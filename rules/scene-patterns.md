# Scene Composition Patterns

Proven scene patterns developed through production. Use as blueprints for new videos. These go beyond templates — they're full animation choreographies.

## Pro Tip Scene

A punchy one-liner tip with gradient accent on the key phrase.

**Structure**:
1. PillBadge-style category badge (semi-transparent, NOT gradient-filled)
2. Large text with one key phrase wrapped in GradientText

**Example**:
```tsx
// Badge: semi-transparent pill (white text, subtle border)
<PillBadge>Pro Tip</PillBadge>

// Tip text with gradient key phrase
<div style={{ fontSize: 54, fontWeight: FONT_WEIGHTS.heading }}>
  Think of your prompt as a{' '}
  <GradientText>product requirements doc</GradientText>
</div>
```

**Background**: Aurora `bottom`
**Duration**: 6-12s
**Animation**: Badge pop at delay 5, text slide-up at delay 15

---

## Pain Points / Scattered Cards Scene

Multiple source cards fly in from different directions, pile up chaotically, then scatter as a punchline lands.

**Structure**:
1. Optional header text (fades out before scatter)
2. 4-5 cards with icon + label + sublabel, each from different origin points
3. Cards float with idle drift after landing
4. Cards scatter outward (Easing.in cubic, ~1.5s)
5. Punchline text appears: main line + gradient accent line

**Key data structure**:
```typescript
type SourceCard = {
  icon: string;
  label: string;
  sublabel: string;
  fromX: number;    // Origin offset
  fromY: number;
  x: number;        // Resting position from center
  y: number;
  rotate: number;   // Slight tilt at rest
  delay: number;    // Entrance delay in frames
};
```

**Idle float pattern** (after landing):
```tsx
const t = frame / fps;
const floatY = Math.sin(t * 1.2 + cardIndex * 2) * 6;
const floatX = Math.cos(t * 0.8 + cardIndex * 3) * 4;
```

**Background**: Aurora `bottom`
**Duration**: 17-22s
**Animation**: Cards fly in staggered -> idle float -> scatter -> punchline

---

## Competitor Cards + Questions Scene

Named cards slam in from different directions, then question pills appear below.

**Structure**:
1. 3 cards in a row, each with logo/icon + name
2. Each card flies from a different direction (left, top, right)
3. Question pills appear below in a flex-wrap row
4. Optional: button alert sound on each card entrance

**Card entrance pattern**:
```tsx
const CARDS = [
  { name: "Brand A", fromX: -800, fromY: -100, rotate: -6 },
  { name: "Brand B", fromX: 0, fromY: -600, rotate: 2 },
  { name: "Brand C", fromX: 800, fromY: -100, rotate: 5 },
];

// Staggered entrance
const entrance = spring({
  frame, fps,
  delay: 12 + i * 17,
  config: { damping: 14, stiffness: 60, mass: 1.1 },
});
```

**Sound**: Button alert SFX on each card entrance using `<Audio>` + `<Sequence>`
```tsx
{CARDS.map((_, i) => (
  <Sequence key={i} from={12 + i * 17}>
    <Audio src={staticFile('audio/Button Alert.mp3')} volume={0.15} />
  </Sequence>
))}
```

**Background**: Aurora `bottom`
**Duration**: 16s
**Animation**: Cards at `12 + i*17` frames, questions at `114 + i*30` frames

---

## Side-by-Side Comparison Scene

Two option cards with focus/highlight states that alternate attention.

**Structure**:
1. Title text with gradient key phrase
2. Two cards appear side by side
3. Cards alternate between highlighted and dimmed states based on voiceover timing
4. Each card: icon/logo, name, tagline pill, description

**Highlight state machine**:
```tsx
// Timeline phases
const highlightCardAStart = 80;    // ~2.6s
const highlightCardBStart = 220;   // ~7.3s
const highlightBothStart = 380;    // ~12.6s

// Per-card focus transitions
if (frame >= highlightBothStart) {
  highlightState = 0;      // Both normal
} else if (frame >= highlightCardBStart) {
  highlightState = i === 1 ? 1 : -1;  // B focused, A dimmed
} else if (frame >= highlightCardAStart) {
  highlightState = i === 0 ? 1 : -1;  // A focused, B dimmed
}
```

**Dimmed state**: scale 0.95, opacity 0.4, blur 4px
**Focused state**: scale 1.05, colored border glow, full opacity
**Background**: Aurora `bottom`
**Duration**: 15-18s

---

## Notification / Alert Scene

Slack-style or app notification card.

**Structure**:
1. Dark card with channel header (`#channel-name` + timestamp)
2. Divider line
3. Alert badge (colored accent background + text)
4. Message body with link text in brand blue
5. Optional: link back to app/detail page

**Visual recipe**:
```tsx
// Alert badge
<div style={{
  display: 'inline-block',
  padding: '6px 16px',
  borderRadius: 8,
  backgroundColor: `${BRAND_COLORS.primary}15`,
  color: BRAND_COLORS.primary,
  fontWeight: FONT_WEIGHTS.semibold,
  fontSize: 16,
  textTransform: 'uppercase',
  letterSpacing: 0.5,
}}>
  COMPETITOR ALERT
</div>
```

**Sound**: Notification chime SFX at card entrance
**Background**: Aurora `bottom-right`
**Duration**: 4-19s
**Animation**: Card slides up at delay 3, content fades at delay 10

---

## Animated Gradient Background

A standalone background scene for use as B-roll during screencasts.

**Structure**: Multiple positioned radial gradient blobs with breathing animation (scale + opacity on sine waves).

**Key technique** — in-place breathing, NOT lateral movement:
```tsx
// Each glow blob breathes independently
const t = frame / fps;
const scale1 = 1 + Math.sin(t * 0.3) * 0.15;
const opacity1 = 0.25 + Math.sin(t * 0.2 + 1) * 0.08;
```

**Important**: Use `transformOrigin` anchored to each glow's center. Do NOT translate the blobs left/right — that looks like a sliding background, not atmospheric breathing.

**Duration**: 60s+ (render in 15s chunks to avoid timeout, concatenate with ffmpeg)

---

## Key Composition Rules

1. **One idea per scene** — don't overload
2. **Gradient text for THE key phrase** — one per scene, not everything
3. **PillBadge for category labels** — semi-transparent, never gradient-filled
4. **Dark card backgrounds**: `darkCard` + `rgba(255,255,255,0.08)` border
5. **Card shadow**: `0 12px 40px rgba(0,0,0,0.5)` or `0 16px 48px rgba(0,0,0,0.5)`
6. **Consistent padding**: 40-50px outer, 44-56px card inner
7. **maxWidth**: 1600 for full-width cards, 1100 for centered text
8. **Sound design per scene**: Button alerts on card entrances, swooshes on section transitions, notification chimes on alerts
