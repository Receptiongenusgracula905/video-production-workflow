# Reusable Components

Core component library that every video project needs. These wrap brand constants into composable building blocks.

## BrandBackground

The atmospheric background layer used in every scene. Provides dark base + optional aurora glow.

```tsx
// src/components/BrandBackground.tsx
import React from 'react';
import { AbsoluteFill } from 'remotion';
import { BRAND_COLORS, BRAND_GRADIENT } from '../brand';

type GlowPosition = 'bottom' | 'bottom-right' | 'top-left' | 'center';

type Props = {
  aurora?: GlowPosition;
  animated?: boolean;  // Subtle breathing on the glow
};

export const BrandBackground: React.FC<Props> = ({ aurora = 'bottom', animated = false }) => {
  // Position the glow based on aurora prop
  const glowStyles: Record<GlowPosition, React.CSSProperties> = {
    bottom: { bottom: '-20%', left: '20%', width: '60%', height: '50%' },
    'bottom-right': { bottom: '-15%', right: '-10%', width: '50%', height: '50%' },
    'top-left': { top: '-15%', left: '-10%', width: '50%', height: '50%' },
    center: { top: '10%', left: '15%', width: '70%', height: '60%' },
  };

  return (
    <AbsoluteFill style={{ backgroundColor: BRAND_COLORS.darkBg }}>
      {aurora && (
        <div style={{
          position: 'absolute',
          ...glowStyles[aurora],
          background: `radial-gradient(ellipse at center, ${BRAND_COLORS.primary}25 0%, transparent 70%)`,
          filter: 'blur(80px)',
          pointerEvents: 'none',
        }} />
      )}
    </AbsoluteFill>
  );
};
```

**Usage**: `<BrandBackground aurora="bottom" />`

## GradientText

Applies the brand gradient to a text span. Use for ONE key phrase per scene.

```tsx
// src/components/GradientText.tsx
import React from 'react';
import { BRAND_GRADIENT } from '../brand';

export const GradientText: React.FC<{ children: React.ReactNode }> = ({ children }) => (
  <span style={{
    background: BRAND_GRADIENT.css,
    WebkitBackgroundClip: 'text',
    WebkitTextFillColor: 'transparent',
    backgroundClip: 'text',
  }}>
    {children}
  </span>
);
```

**Usage**: `<GradientText>key phrase here</GradientText>`

## PillBadge

Semi-transparent pill for category labels, tags, section markers.

```tsx
// src/components/PillBadge.tsx
import React from 'react';
import { BRAND_COLORS, SURFACES } from '../brand';
import { brandFont, FONT_WEIGHTS } from '../fonts';

type Props = {
  children: React.ReactNode;
  color?: string;  // Override border/text color
};

export const PillBadge: React.FC<Props> = ({ children, color }) => (
  <div style={{
    display: 'inline-block',
    padding: '10px 28px',
    borderRadius: 100,
    border: `1px solid ${color ? `${color}40` : SURFACES.pillBorder}`,
    backgroundColor: color ? `${color}15` : SURFACES.pillBg,
    fontSize: 22,
    fontFamily: brandFont,
    fontWeight: FONT_WEIGHTS.medium,
    color: color || BRAND_COLORS.offWhite,
    letterSpacing: 1.5,
    textTransform: 'uppercase' as const,
  }}>
    {children}
  </div>
);
```

**Usage**: `<PillBadge>Category</PillBadge>` or `<PillBadge color={BRAND_COLORS.primary}>Alert</PillBadge>`

**Important**: PillBadge is ALWAYS semi-transparent with subtle border. Never gradient-filled. This is a common design mistake to avoid.

## AnimatedText

Word-by-word or line-by-line text reveal with spring physics.

```tsx
// src/components/AnimatedText.tsx
import React from 'react';
import { useCurrentFrame, useVideoConfig, spring, interpolate } from 'remotion';
import { BRAND_COLORS } from '../brand';
import { brandFont, FONT_WEIGHTS } from '../fonts';

type Props = {
  text: string;
  delay?: number;
  fontSize?: number;
  fontWeight?: number;
  color?: string;
  mode?: 'word' | 'line';
};

export const AnimatedText: React.FC<Props> = ({
  text,
  delay = 0,
  fontSize = 54,
  fontWeight = FONT_WEIGHTS.heading,
  color = BRAND_COLORS.white,
  mode = 'word',
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const units = mode === 'word' ? text.split(' ') : text.split('\n');

  return (
    <div style={{ display: 'flex', flexWrap: 'wrap', gap: mode === 'word' ? '0.3em' : 0 }}>
      {units.map((unit, i) => {
        const entrance = spring({
          frame,
          fps,
          delay: delay + i * 3,
          config: { damping: 20, stiffness: 80 },
        });
        const opacity = interpolate(entrance, [0, 1], [0, 1]);
        const y = interpolate(entrance, [0, 1], [15, 0]);

        return (
          <span key={i} style={{
            fontFamily: brandFont,
            fontSize,
            fontWeight,
            color,
            opacity,
            transform: `translateY(${y}px)`,
            display: 'inline-block',
          }}>
            {unit}{mode === 'word' ? '' : ''}
          </span>
        );
      })}
    </div>
  );
};
```

## BrandLogo

Renders the brand logo with optional entrance animation.

```tsx
// src/components/BrandLogo.tsx
import React from 'react';
import { Img, staticFile, useCurrentFrame, useVideoConfig, spring, interpolate } from 'remotion';

type Props = {
  variant?: 'full' | 'icon';
  theme?: 'dark' | 'light';
  size?: number;
  delay?: number;
};

export const BrandLogo: React.FC<Props> = ({
  variant = 'full',
  theme = 'dark',
  size = 200,
  delay = 0,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const entrance = spring({ frame, fps, delay, config: { damping: 20, stiffness: 80 } });
  const opacity = interpolate(entrance, [0, 1], [0, 1]);
  const scale = interpolate(entrance, [0, 1], [0.9, 1]);

  // Naming convention: logo-{variant}-{theme}.png
  const src = staticFile(`brand/logo-${variant}-${theme}.png`);

  return (
    <Img src={src} style={{
      width: size,
      height: 'auto',
      opacity,
      transform: `scale(${scale})`,
    }} />
  );
};
```

## Component Design Rules

1. **Every component imports from `brand.ts`** — no hardcoded colors
2. **All text uses `fontFamily: brandFont`** — consistency
3. **Animations use `spring()`** — never CSS transitions in Remotion (they don't work frame-by-frame)
4. **Components are stateless** — animation state comes from `useCurrentFrame()`
5. **`willChange: 'transform, opacity'`** on animated elements for GPU acceleration
6. **Dark card pattern**: `backgroundColor: BRAND_COLORS.darkCard` + `border: 1px solid rgba(255,255,255,0.08)` + `borderRadius: 24` + `boxShadow: 0 16px 48px rgba(0,0,0,0.5)`
