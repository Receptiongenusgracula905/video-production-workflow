# Brand System

How to define and apply a brand's visual identity across all video components.

## Brand Intake Checklist

Before building anything, gather these from the brand:

| Asset | Format | Used For |
|-------|--------|----------|
| Primary color | Hex | Headlines, CTAs, gradient start |
| Secondary color | Hex | Accents, gradient middle |
| Tertiary color | Hex | Info, links, gradient end |
| Background color | Hex | Dark/light mode base |
| Brand gradient | CSS string | Key phrases, decorative elements |
| Logo (dark bg) | SVG + PNG | Scene headers, intros |
| Logo (light bg) | SVG + PNG | Card overlays |
| Icon/mark | SVG + PNG | Compact spaces, badges |
| Primary typeface | TTF/WOFF2 | All text |
| Font weight range | Numbers | Weight tokens |

## Color Architecture

Every brand system needs these color roles:

```typescript
const BRAND_COLORS = {
  // === Brand identity ===
  primary: '#...',           // Hero color — CTAs, key phrases
  secondary: '#...',         // Supporting — accents, badges
  tertiary: '#...',          // Third color — links, info states

  // === Neutrals (adapt to dark/light mode) ===
  white: '#FFFFFF',
  offWhite: '#E0E0E6',      // Body text on dark backgrounds
  lightGray: '#A0A0B0',     // Secondary text, descriptions
  darkBg: '#0A0A0F',        // Main background
  darkCard: '#111118',       // Card surfaces (slightly lighter than bg)
  darkSurface: '#1A1A24',   // Elevated surfaces

  // === Semantic colors ===
  success: '#4ADE80',
  warning: '#FBBF24',
  error: '#F87171',
};
```

### Light Mode Adaptation

If the brand uses a light interface:

```typescript
const LIGHT_COLORS = {
  white: '#FFFFFF',          // Card backgrounds
  offWhite: '#1A1A2E',      // Body text (dark on light)
  lightGray: '#6B7280',     // Secondary text
  darkBg: '#F8F9FA',        // Main background (light)
  darkCard: '#FFFFFF',       // Cards (white)
  darkSurface: '#F1F3F5',   // Elevated surfaces
};
```

## Gradient System

The signature gradient is the most recognizable brand element in motion graphics.

```typescript
const BRAND_GRADIENT = {
  // Linear gradient — used for text, bars, accents
  css: `linear-gradient(${angle}deg, ${color1} 0%, ${color2} 50%, ${color3} 100%)`,
  cssVertical: `linear-gradient(180deg, ${color1} 0%, ${color2} 50%, ${color3} 100%)`,

  // Radial gradient — used for background glows
  radial: `radial-gradient(ellipse at center, ${color1}40 0%, transparent 70%)`,

  // Individual stops for programmatic use
  stops: [color1, color2, color3],
  angle: 135,
};
```

### Gradient Usage Rules

1. **GradientText** — one key phrase per scene, never entire paragraphs
2. **Left bar highlights** — vertical accent bar on active content sections
3. **Background aurora** — soft radial glows using gradient colors at 10-25% opacity
4. **Badge/pill accents** — gradient border or subtle gradient background

## Typography Scale

Define a consistent type scale:

```typescript
const TYPE_SCALE = {
  hero: 72,          // Full-screen headlines
  title: 54,         // Scene titles
  heading: 42,       // Section headings
  subheading: 32,    // Card titles
  body: 24,          // Body text, descriptions
  caption: 18,       // Labels, timestamps
  badge: 14,         // Pill badges, tags
};
```

### Font Weight Tokens

Map semantic names to actual weights (adjust per typeface):

```typescript
const FONT_WEIGHTS = {
  light: 300,
  regular: 400,
  medium: 500,
  heading: 480,      // Tune this — some typefaces look best at non-standard weights
  semibold: 600,
  bold: 700,
};
```

## Semi-Transparent Surfaces

For dark-mode UIs, these opacity levels create depth:

| Surface | Value | Use |
|---------|-------|-----|
| Card background | `rgba(255,255,255,0.06)` | Content cards |
| Card border | `rgba(255,255,255,0.08)` | Subtle card edges |
| Pill background | `rgba(255,255,255,0.08)` | Badges, tags |
| Pill border | `rgba(255,255,255,0.25)` | Badge outlines |
| Overlay | `rgba(255,255,255,0.04)` | Hover states |
| Active highlight | `rgba(255,255,255,0.12)` | Selected items |

## Background Glow (Aurora Effect)

Create atmospheric depth with positioned radial gradients:

```tsx
// Bottom-center glow
<div style={{
  position: 'absolute',
  bottom: '-20%',
  left: '20%',
  width: '60%',
  height: '50%',
  background: `radial-gradient(ellipse at center, ${primary}25 0%, transparent 70%)`,
  filter: 'blur(80px)',
  pointerEvents: 'none',
}} />
```

### Glow Positions

| Position | Use Case |
|----------|----------|
| `bottom` | Most scenes — grounds the content |
| `bottom-right` | Callouts, notifications — directional energy |
| `top-left` | Alternate compositions — visual variety |
| `center` | Hero moments — maximum impact |

## Applying Brand to Scenes

Every scene automatically inherits the brand through imports:

```tsx
import { BRAND_COLORS, BRAND_GRADIENT } from '../brand';
import { brandFont, FONT_WEIGHTS } from '../fonts';
import { BrandBackground } from '../components/BrandBackground';
```

No hardcoded colors anywhere in scene files. Everything references brand constants.
