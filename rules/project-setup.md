# Project Setup

How to scaffold a new Remotion video project for any brand.

## Prerequisites

- Node.js 18+
- FFmpeg 7+ (for rendering and audio mixing)

## Create the Repo

```bash
npx create-video@latest my-video --template blank
cd my-video
npm install
```

## Directory Structure

Every project follows this layout:

```
my-video/
├── public/
│   ├── brand/              # Logo assets (SVG, PNG — light/dark variants)
│   ├── fonts/              # Brand typeface files (.ttf, .woff2)
│   └── audio/              # Sound effects and music
├── src/
│   ├── index.ts            # Remotion entry point
│   ├── Root.tsx             # Composition registry
│   ├── brand.ts            # Color/gradient/video constants
│   ├── fonts.ts            # Font loading config
│   ├── components/         # Reusable brand components
│   │   ├── BrandBackground.tsx
│   │   ├── GradientText.tsx
│   │   ├── PillBadge.tsx
│   │   ├── AnimatedText.tsx
│   │   └── BrandLogo.tsx
│   ├── templates/          # Base scene templates (generic, reusable)
│   │   ├── CenterVideo.tsx
│   │   ├── FullScreenBrandMotion.tsx
│   │   ├── FullScreenCallOut.tsx
│   │   ├── FullScreenTitleQuestion.tsx
│   │   ├── FullScreenHeadlineSubtext.tsx
│   │   ├── Quote.tsx
│   │   ├── SplitScreenCallOut.tsx
│   │   ├── PromptDisplay.tsx
│   │   └── HighlightedPrompt.tsx
│   └── scenes/             # Video-specific scenes
│       └── my-first-video/
│           ├── index.ts    # Barrel export
│           ├── BG_Gradient.tsx
│           ├── S01_Intro.tsx
│           └── ...
└── out/                    # Rendered output
    └── my-first-video/
```

## brand.ts — The Brand Constants File

This is the single source of truth for all visual constants. Every component and template imports from here.

```typescript
// src/brand.ts

// === COLORS ===
// Replace these with your brand's palette
export const BRAND_COLORS = {
  // Primary brand colors
  primary: '#FF8E63',        // Your hero color
  secondary: '#FF7EB0',      // Accent color
  tertiary: '#4B73FF',       // Third brand color

  // Neutrals
  white: '#FFFFFF',
  offWhite: '#E0E0E6',
  lightGray: '#A0A0B0',
  darkBg: '#0A0A0F',         // Main background
  darkCard: '#111118',       // Card backgrounds
  darkSurface: '#1A1A24',    // Elevated surfaces

  // Semantic
  orange: '#FF8E63',         // Alerts, CTAs
  blue: '#4B73FF',           // Links, info
  green: '#4ADE80',          // Success states
} as const;

// === GRADIENTS ===
// Your brand's signature gradient
export const BRAND_GRADIENT = {
  // CSS linear-gradient string
  css: 'linear-gradient(135deg, #FF8E63 0%, #FF7EB0 50%, #4B73FF 100%)',
  cssVertical: 'linear-gradient(180deg, #FF8E63 0%, #FF7EB0 50%, #4B73FF 100%)',
  // Gradient stops for programmatic use
  stops: ['#FF8E63', '#FF7EB0', '#4B73FF'],
  angle: 135,
} as const;

// === VIDEO DIMENSIONS ===
export const VIDEO = {
  WIDTH: 1920,
  HEIGHT: 1080,
  FPS: 30,
} as const;

// === SEMI-TRANSPARENT SURFACES ===
export const SURFACES = {
  cardBg: 'rgba(255,255,255,0.06)',
  cardBorder: 'rgba(255,255,255,0.08)',
  pillBg: 'rgba(255,255,255,0.08)',
  pillBorder: 'rgba(255,255,255,0.25)',
  overlayLight: 'rgba(255,255,255,0.04)',
} as const;
```

## fonts.ts — Font Configuration

```typescript
// src/fonts.ts
import { staticFile } from 'remotion';
import { loadFont } from '@remotion/fonts';

// Load your brand font
const fontFamily = 'BrandFont';

loadFont({
  family: fontFamily,
  url: staticFile('fonts/YourBrandFont.ttf'),
  weight: '100 900',         // Variable font range
});

export const brandFont = fontFamily;

// Define semantic weight tokens
export const FONT_WEIGHTS = {
  light: 300,
  regular: 400,
  medium: 500,
  semibold: 600,
  heading: 480,              // Tuned for your typeface
  bold: 700,
} as const;
```

## Root.tsx — Composition Registry

```tsx
import { Composition, Folder } from 'remotion';
import { VIDEO } from './brand';
import { S01_Intro, S02_Content } from './scenes/my-video';

export const RemotionRoot: React.FC = () => (
  <>
    <Folder name="My-Video">
      <Composition
        id="S01-Intro"
        component={S01_Intro}
        durationInFrames={3 * VIDEO.FPS}
        fps={VIDEO.FPS}
        width={VIDEO.WIDTH}
        height={VIDEO.HEIGHT}
      />
      <Composition
        id="S02-Content"
        component={S02_Content}
        durationInFrames={15 * VIDEO.FPS}
        fps={VIDEO.FPS}
        width={VIDEO.WIDTH}
        height={VIDEO.HEIGHT}
      />
    </Folder>
  </>
);
```

## Key Rules

1. **Duration** = seconds x `VIDEO.FPS` (30)
2. **Always use** `VIDEO.WIDTH` (1920) and `VIDEO.HEIGHT` (1080)
3. **Template compositions** go in a `Templates` folder with `defaultProps`
4. **Scene compositions** go in their video-specific folder without `defaultProps` (content is hardcoded)
5. **One barrel export** per video directory (`index.ts`)
6. **Composition IDs** use hyphens: `S01-Intro`, `S08b-StoreResults`

## Scene File Naming

- Primary scenes: `S01_Name.tsx`, `S02_Name.tsx`
- Supplementary scenes: `S01b_Name.tsx`, `S08c_Name.tsx` (letter suffix)
- Utility scenes: `BG_Gradient.tsx` (descriptive prefix)
- PascalCase component names, underscore-separated file names

## Asset Pipeline

1. Brand logos live in the brand's asset repo (SVG + PNG, light/dark variants)
2. Copy needed assets to `public/brand/` in the Remotion project
3. Reference with `staticFile('brand/logo.png')`
4. Audio files go in `public/audio/`
5. Font files go in `public/fonts/`
