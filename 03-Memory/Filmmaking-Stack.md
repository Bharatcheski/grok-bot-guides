# Filmmaking Stack: Master Production Workflow

- **Date:** Oct 15, 2026
- **Source:** Gemini chat "Master Production", https://share.gemini.google/LStHxmi4ljFD
- **Scope:** The final workflow structure the chat ends on: a central state hub, 5 modality routes and 3 vertical stages, plus the L-SCAM prompt template. The chat states no prices or tool links, so none are listed here.

---

## 1. Central State Hub (Single Source of Truth)
Every production stage reads from and writes to this registry:

1. **Text Body**: Screenplay beats and ElevenLabs timestamps
2. **Lock Stack**: Face, wardrobe, product and brand vectors
3. **Contact Sheet / Storyboard PDF**: Visual index (Wan)
4. **Frame Registry**: Start (Frame 0) and End (Frame N) IDs

> "Compiler is the translator. Beat sheet is the story clock. They are not the same file."

## 2. The 5 Generative Modality Routes (Input → Output)

| Route | Primary Input | Secondary Conditioning | Output | Best Used For |
|---|---|---|---|---|
| 1: T2I | Text prompt (L-SCAM) | Reference slots (IP-Adapter / `--cref`) | Master plate still | Concept art, storyboard plates, contact sheets |
| 2: I2V (Single) | Master plate (Frame 0) | Δ motion prompt | 4–5s video clip | General cinematic action, controlled camera movement |
| 3: I2V (Dual) | Start Frame 0 + End Frame N | Transition prompt | Interpolated video clip | Exact start and stop poses, packaging unboxing |
| 4: R2V | Reference tokens (Slots 1–3) | Full scene prompt | Direct video clip | Free-angle shots that need strict identity continuity |
| 5: V2V | Source video plate | ControlNet (Depth/Pose) + style | Restyled video clip | Complex physical stunts, precise hand choreography |

**R2V vs I2V:** With R2V, the reference image does not lock the composition of frame 0. It injects visual embeddings (face structure, garment weave) and still allows free camera angles and scene changes.
**I2V prompting:** Don't describe what Frame 0 already shows, because that causes re-rendering or mutation. Put about 90% of the prompt on [Action] and [Camera].

## 3. Stages and Tools

### Stage 1: Pre-Production (Asset Locking & Plate Prep)
- **Inputs:** Script, brand guidelines, character seeds
- **Tools:**
  - **ElevenLabs** (audio): voiceover (TTS) and foley or atmosphere (SFX) stems, generated first so they set exact shot durations
  - **Midjourney / Flux** (T2I plates): high-resolution stills for every beat
  - **Photoshop** (retouch and inpaint): fix anatomy and fingers, clean up logos
  - **Wan**: listed as the visual index for the contact sheet / storyboard PDF
- **Outputs:** Master contact sheet / PDF, locked Frame 0 stills, voice stems

### Stage 2: Production (Motion Inference)
- **Inputs:** Cleaned keyframes, Δ motion prompts, reference slots
- **Tools:** Kling 1.5, Runway Gen-3, Google Flow, Veo 2
- **Outputs:** Raw 4–5s video plates

### Stage 3: Post-Production (Assembly & Finishing)
- **Inputs:** Rendered clips, audio stems, vector logos
- **Tools:**
  - **NLE** (DaVinci Resolve / Premiere / Final Cut Pro): timeline assembly, audio sync, color conform
  - **After Effects**: 2D motion graphics (MGFX)
- **Outputs:** Final master render (16:9 or 9:16)

## 4. Step-by-Step Workflow

**Stage 1: Pre-Production**
1. Audio synthesis (ElevenLabs): generate the VO and SFX to set exact shot durations.
2. Plate generation (T2I): generate high-resolution stills for all beats in Midjourney or Flux.
3. Retouch and inpaint: correct anatomical errors, repair fingers, clean up logos.
4. Assembly: compile the approved plates into a master contact sheet and PDF storyboard.

**Stage 2: Production**
1. Route selection: assign each beat to Route 2 (Single I2V), Route 3 (Dual I2V) or Route 4 (R2V).
2. Keyframe ingestion: load the approved Frame_0, plus Frame_N when dual-anchoring.
3. Motion prompting: compile isolated camera moves and action deltas, with static descriptions stripped out.
4. Diffusion render: run 4–5s passes within safe motion strength limits (0.2–0.5).

**Stage 3: Post-Production**
1. NLE assembly: sequence the video renders on the timeline to match the audio durations.
2. Typography and MGFX: overlay 2D vector badges, text stickers and CTAs instead of using AI text rendering.
3. Color conform: apply one master LUT across all shots to remove color and lighting drift between them.

## 5. Per-Shot Template (L-SCAM Compiler)

```
[PHASE 1: EDITORIAL & SCREENPLAY DIRECTIVE]
* Scene / Cut ID, Target Aspect Ratio (16:9, 9:16, 2.39:1)
* Dramatic Objective
* Dialogue / Voiceover (for post-sync, NOT in the visual prompt)
* Audio Cues / Foley / Score

[PHASE 2: INGESTION SLOTS & MODALITY SELECTOR]
* Active Modality: T2V | I2V | R2V | V2V | A2V
* Slot 1 [Wardrobe/Asset Lock]
* Slot 2 [Subject/Identity Lock]
* Slot 3 [Spatial/Motion Conditioning]: start frame (I2V) or source video (V2V)

[PHASE 3: L-SCAM COMPILATION]
[Location] [Subject] [Camera] [Action] [Mood]  --ar [ratio]
(omit Camera/Action for V2V)

[PHASE 4: POST-PRODUCTION & NLE ASSEMBLY]
1. Generation: slots + L-SCAM prompt into the video engine (4-second limit)
2. Editorial: NLE timeline (DaVinci Resolve / Premiere / Final Cut)
3. Audio conform: sync VO to mouth movement; layer foley and score
4. Color conform: master LUT across cuts
```

## 6. Worked Example: 30s Commercial Beat Matrix (6 beats × 3 stages)

| Beat | Stage 1 (Pre-Prod) | Stage 2 (Production) | Stage 3 (Post) |
|---|---|---|---|
| 01 Urban Walk (00–05s) | T2I: face_lock + street prompt → Plate_01; VO + street SFX | Route 2 I2V: 28mm low-angle track forward, energetic walk | Sync footstep foley; tracked sticker; warm LUT |
| 02 The Reveal (05–10s) | T2I dual-keyframe prep: Plate_02A (wrapped) & 02B (unwrapped) | Route 3 dual I2V: hands pull paper open | Align paper-tear SFX; pop-up text; balance neon greens |
| 03 Macro Food Hero (10–15s) | T2I + Photoshop inpaint → Plate_03_Cleaned | Route 2 low-motion, strength 0.25: macro push-in, sauce drip | Speed-ramp drip to 80%; callout badge; saturated grade |
| 04 The Bite (15–20s) | T2I + inpaint → Plate_04 | Route 2: 85mm close-up, takes bite | Snap crunch SFX to tooth contact; badge; skin smoothing |
| 05 Social Table (20–25s) | T2I multi-subject: face_lock + cast_lock → Plate_05 | Route 2: overhead slow descending drift | Duck chatter under VO; lower-left badge; warm daylight |
| 06 Brand Outro & CTA (25–30s) | T2I + 2D overlay: product_lock + logo_lock → Plate_06 | Route 2 ambient, strength 0.15 (prevents packaging warp) | Hard cut on beat drop; animated CTA card; high-contrast grade |

---
*Built only from the source chat. The source gives no costs or tool links.*
