# End-to-End Production Workflow

The complete pipeline from concept to final deliverable.

## Phase 1: Pre-Production

### 1.1 Gather Brand Assets
- [ ] Brand colors (primary, secondary, tertiary)
- [ ] Brand gradient definition
- [ ] Logo files (SVG + PNG, dark/light variants)
- [ ] Brand typeface files
- [ ] Brand guidelines document (if available)
- [ ] Reference videos or style examples

### 1.2 Write the Script
- Outline the video structure (sections, key points)
- Write narration script with natural pacing
- Mark emphasis points and transitions
- Identify where title cards / motion graphics are needed

### 1.3 Record Voiceover
- Human narrator (recommended) or Eleven Labs AI
- Edit in Descript — remove filler, tighten pacing
- Export transcript with timestamps
- This timing drives everything downstream

## Phase 2: Project Setup

### 2.1 Scaffold Remotion Project
See [project-setup.md](project-setup.md)

```bash
npx create-video@latest brand-video --template blank
cd brand-video
npm install
```

### 2.2 Configure Brand System
See [brand-system.md](brand-system.md)

1. Create `src/brand.ts` with brand colors, gradients, video constants
2. Create `src/fonts.ts` with brand typeface
3. Copy logos to `public/brand/`
4. Copy fonts to `public/fonts/`

### 2.3 Build Component Library
See [components.md](components.md)

Create the core 5 components:
- BrandBackground
- GradientText
- PillBadge
- AnimatedText
- BrandLogo

### 2.4 Set Up Templates
See [templates.md](templates.md)

Implement the templates you'll need (usually 4-6 of the 9).

## Phase 3: Scene Production

### 3.1 Map Transcript to Scenes
Walk through the timestamped transcript and create a scene plan:

```markdown
| # | Timestamp | Template | Content | Duration |
|---|-----------|----------|---------|----------|
| 1 | 00:00:00 | FullScreenTitleQuestion | "What if..." | 3s |
| 2 | 00:00:35 | FullScreenCallOut | 4 bullets overview | 15s |
| 3 | 00:01:08 | HighlightedPrompt | Dashboard prompt | 24s |
```

### 3.2 Create Scene Files
See [scene-patterns.md](scene-patterns.md)

For each scene:
1. Create `S01_Name.tsx` in `src/scenes/video-name/`
2. Either import a template and pass props, or build custom layout
3. Add to barrel export `index.ts`
4. Register in `Root.tsx` with correct duration

### 3.3 Iterate on Animation
See [animation.md](animation.md)

1. Run `npm start` to open Remotion Studio
2. Scrub through each scene
3. Adjust timing, spring configs, stagger delays
4. Match animation rhythm to voiceover pacing

### 3.4 Add Sound to Scenes
See [sound-design.md](sound-design.md)

- Swoosh on section transitions in HighlightedPrompt
- Button alert on card entrances
- Notification chime on alert scenes

## Phase 4: Sound Design

### 4.1 Analyze Transcript for Sound Cues
Identify 10-20 key moments that need sound.

### 4.2 Generate Background Music
Use Song Generation API for the underscore track.

### 4.3 Shape Volume Envelope
Apply dynamic volume: louder intro, quieter middle, build at ending.

### 4.4 Generate Individual SFX
Use MMAudio V2 for swooshes, impacts, chimes, ambient beds.

### 4.5 Mix Everything
Use FFmpeg to combine all tracks with `adelay` positioning.

## Phase 5: Rendering

### 5.1 Render All Scenes
See [rendering.md](rendering.md)

```bash
for id in S01-Intro S02-Content ...; do
  npx remotion render src/index.ts "$id" "out/video-name/${id}.mp4" --width 1920 --height 1080
done
```

### 5.2 Render Background (if needed)
Chunk render for 60s+ animated backgrounds.

### 5.3 Verify All Renders
Check each output for correct duration and visual quality.

## Phase 6: Assembly & Delivery

### 6.1 Import to Video Editor
- Import all rendered scenes
- Import screencast footage (if demo video)
- Import voiceover audio
- Import background music

### 6.2 Timeline Assembly
1. Lay down voiceover on primary audio track
2. Place screencast footage on video track
3. Overlay title card scenes at appropriate timestamps
4. Place background music on secondary audio track
5. Add transitions between sections

### 6.3 Final Mix
- Voiceover at full volume
- Background music at 10-15% under narration, 25-35% during transitions
- SFX at appropriate levels (see sound-design.md volume reference)

### 6.4 Export
- YouTube: H.264, 1080p, 10-20 Mbps, AAC audio
- Social: H.264, 1080p, 8-12 Mbps
- Archive: ProRes 4444 for future re-edits

## Tips from Production Experience

1. **Voiceover first** — record/generate narration before building any scenes. Timing drives everything.
2. **Plan mode** — for complex videos (10+ scenes), create a scene plan before coding. Map every scene to its template and timestamp.
3. **Iterate in Studio** — Remotion Studio is your best friend. Scrub, adjust, repeat.
4. **Don't over-animate** — subtle spring physics > flashy transitions. The content is the star.
5. **One gradient phrase per scene** — resist the urge to gradient everything.
6. **Chunk render long scenes** — don't fight the timeout, work with it.
7. **Sound is 50% of the experience** — even subtle background music and swooshes dramatically improve perceived quality.
8. **Volume envelope matters** — flat volume feels lifeless. Shape it to match the energy arc of the video.
9. **Store reusable patterns** — when you develop a new scene pattern (pain points, competitor cards, etc.), document it for reuse.
10. **Brand consistency > creativity** — every element should feel like it belongs to the same family.
