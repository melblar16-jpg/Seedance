---
name: seedance-clean
description: >
  Seedance 2.0 prompt-writing system (Seedance 2.0 / 2.0 Fast / 2.0 Mini on BytePlus ModelArk — and the same models resold by Higgsfield/Runway). Use whenever the user wants to write a Seedance 2.0 prompt, build a cinematic shot or scene, turn an idea or script beat into a prompt, set up references/assets, or asks about Seedance prompting technique. Handles both single-shot oners AND multishot sequences that cut between several distinct shots (establish → close → broadcast, montages), in any project visual style — photoreal live-action or animated (3D CGI, 2D cartoon, anime, etc.), matching the project's assets. Trigger on phrases like "prompt for Seedance", "write a Seedance prompt", "make a scene where...", "build a shot", "нужна сцена с переключением кадров / раскадровка / несколько планов", "сцена для мультика", "montage", "how to write a Seedance prompt". Output is a single standalone prompt — one shot, or a multishot sequence written as ONE prompt document.
  ROUTING: this skill is for the 2.0 family ONLY. For Seedance 2.5 (30-second clips, up to 50 references, integer-second timestamps, video editing / extension / keyframes / storyboards / blockouts) use the seedance-25 skill instead — the two models obey different prompt grammars and the formats are not interchangeable. For MiniMax H3 use minimax-h3; for Gemini Omni (editing an existing clip, extending to 40 s, video references, on-screen text) use gemini-omni.
  Always consult this skill before writing any Seedance 2.0 prompt.
---

# Seedance 2.0 Prompt Writer

Turn scripts, scenes, and ideas into Seedance 2.0 prompts. Block, light, and pace each shot like a film.

**Output: always a single standalone prompt in a code block.** That one prompt may be a single continuous shot OR a multishot sequence that cuts between several distinct shots — both are still one prompt document. "Single prompt" ≠ "single camera setup." A scene with 6 shots is one prompt with six `[SHOT]` blocks inside it, not six prompts and not one flattened shot. Default to the shape the scene needs: a confession is one held oner; an establish→faces→broadcast beat wants a multishot.

**Source of truth.** This skill merges two things: BytePlus's official *Dreamina Seedance 2.0 series prompt guide* (docs.byteplus.com/en/docs/modelark/2222480, last updated 2026-08-11), and our own A/B-tested practice. Where they disagreed, the vendor's rule won and the reason is written down. Sections marked **[official]** come from the vendor guide.

---

## CORE PRINCIPLE: WRITE THE VISIBLE

The model reacts to what can be **seen and measured**, not to mood words.

Translate every abstraction into something observable.
- ❌ "tense scene" → ✅ "man freezes, slowly clenches his fist, light only from the side, half his face in shadow"
- ❌ "cool cinematic shot of a car, epic, fast" → ✅ "low tracking shot alongside the car as it powers through a wet curve, headlights glowing, spray off the tyres, hard buffeting camera shake"

Write in plain, clear, instruction-style language. Fewer precise words beat many vague ones. **Detail scales with shot size:** the tighter the frame, the more words it needs — a wide tolerates economy, but a close-up of a face or hands demands exhaustive micro-description (state of the person, grip, object counts, what each hand does), because the frame magnifies every error.

Before generating, mentally "watch" the prompt as a viewer: is everything unambiguous, is the first frame non-empty, is it clear where the subject is and where it looks, where is the light coming from.

---

## WHAT 2.0 ACTUALLY OBEYS — HARD MODEL FACTS [official]

Read this before choosing a prompt shape. These are model properties, not style preferences.

**1. No timestamps. Shot numbers only.**
Seedance 2.0 does not respond to timecodes — the vendor states it plainly ("2.0 does not respond to timestamps and only responds to shot numbers"; the 2.0 guide adds that support for precise timing such as "0–3 seconds" is unstable and *forcing* durations produces broken output). So a 2.0 multishot is ordered with `Shot 1 / Shot 2 / Shot 3`, never with seconds. Do not impose a per-shot duration; let pacing follow the plot. Timecoded prompts still *generate* — the model just reads them as ordering and drops the clock, so they buy nothing and can misfire. (Seedance 2.5 does support integer-second timestamps — that's the seedance-25 skill.)

**2. Three task types, chosen by grammar.**
The verb you use routes the task, so the wrong verb silently changes what the model does.
- **Omni reference** (build something new from assets): `Reference <the subject> in <Image 1> to generate …`, `Reference <the camera movement> in <Video 1> to generate …`, `Reference the timbre in <Audio 1> to generate …`
- **Video editing** (change a source clip): `Strictly edit <Video 1>, and change <original> to <new>` — parts not mentioned stay unchanged by default.
- **Video extension** (continue a clip): `Extend <Video 1> forward/backward to generate …`; track completion is `<Video 1> + <transition> + followed by <Video 2>`.
- **Combined**: `Reference <dimension> of <Image/Video N>, strictly edit <Video X>, <specific edits>`.
- ⚠️ **Trap:** for edit and extend tasks write the bare `<Video N>` — writing "reference Video 1" reroutes the job to a reference task and you get a new video instead of an edited one.
- Input limit for track completion: max 3 clips, combined ≤15 s.

**3. Define the subject before you use it.**
`Define [2–3 stable visible features] in <Image N> as <Subject N>` — e.g. *"Define the woman in the red dress and straw hat in Image 1 as Subject 1"* (a name works too: *"…as Zhang Hong"*). After that, use **the same label every single time** the subject appears. Two supported forms:
- undefined subjects: bind inline every mention — `Zhang San@Image 1`
- predefined subjects: reuse the label — define "police officer" and "thief" once, then always say "police officer" / "thief".
- **Asset-library IDs do not self-identify.** With a verified actor / `asset://` asset you must still refer to it as `Image N` in the text; the model cannot connect an Asset ID to the reference content. (Our studio already rewrites `@name` → `@ImageN`, so keep writing `@tag` and it lands correctly.)
- Keep the definition short. Redundancy and contradictory features about the same subject degrade it.

**4. Reference budget — 4–5 assets, not the limit.** [official]
Recommended set: **1–2 character images + 1 scene image + 1 camera-movement video + 1 audio clip**. Filling the cap (2.0 allows 9 images / 3 audio) makes the model unable to rank feature priority — style conflicts, blurry subject identity, results drifting off-brief. Assets have four functional roles: character anchoring, scene tone-setting, camera-movement reference, rhythm/timbre.

**5. Character references: headshot + full-body. NOT a multi-view sheet.** [official]
The vendor explicitly counter-recommends three-view / multi-view character images for 2.0: the model reads the different angles as *different people*, which causes ID drift and literal twins in one frame. Use a clean **head-only close-up** (face large, no expression, minimal shoulders/neck/background) **plus** one full-body/wardrobe photo, and state which is which: *"Subject 1's facial features reference Image 1 (headshot), makeup and styling reference Image 2 (full body)."* Place the asset that needs the most precise reference **earliest** in the prompt.
- More than **4 reference people** destabilises the count (extra or duplicated characters). For a larger cast, generate grouped stills of ≤4 people each, then use those stills as the video references.
- ⚠️ **Studio note:** our four-panel mockup sheet (front / profile / back / portrait) is exactly the multi-view asset this rule warns about. On 2.0 prefer a portrait + a full-body still over the sheet. (2.5 *does* support multi-view.)

**6. Symbol grammar for audio and text.** [official]
Seedance parses these brackets as content types — use them, don't paraphrase.

| Content | Symbol | Example |
|---|---|---|
| Music | `( )` | `(fast-paced rock music plays in the background)` |
| Sound effect | `< >` | `<a dog barks in the distance>` |
| Dialogue | `{ }` | `{Hello, world}` |
| Subtitle | `【 】` | `【Chapter One: Departure】` |

**Sound discipline (ours, learned the hard way — same rules on 2.0 and 2.5).** The model treats score and sound design as part of "being cinematic" and adds them unasked, so the AUDIO block needs to be written defensively:

1. **At most TWO sound sources per take** — an ambience bed (wind, room tone) plus what physically happens in frame. Birds, passers-by voices, bells, traffic, city atmosphere are **added in the edit**, never ordered from the model: ask for "atmosphere" and you get a soundtrack.
2. **Close the block with a named ban list**, not a general clause: `Nothing else at any point.` then the categories one by one — music / score / strings / piano / drone / swell / riser / whoosh / boom / impact; crowd roar / murmur / cheering / chanting / anthem / applause; bells / traffic / horses / birds; dialogue / voiceover / narration; subtitles. A bare `no music` held on four Mini takes in the studio's own test (21.09.2026, clean spectrograms): keep the category list as insurance, not because the short form fails.
3. **Never name small everyday SFX** (a latch click, a rustle, a cable, a shutter, a notification) — they come back inflated into designed effects with reverb. Write the action silent instead: *"slowly and gently, without any snap or tug."*
4. **Never describe breathing in the directing block** (`breath catching`, `held breath`) — the model voices it as a loud gasp. Write *"her breathing stays silent"* plus `no breath sounds, no gasp, no sigh, no audible inhale` in audio.
5. **Mass sounds (crowd roar, anthem, applause) push the scene into pathos** and fight the voiceover; wind plus one distant element carries scale better than a roar.
6. **Voiceover and narration are never generated** — record them separately (TTS) and lay them in the edit; ban them in every prompt. A character's own on-screen line is dialogue, not narration, and stays.

- Dialogue in a language other than Chinese/English must name the language: `says in Japanese {こんにちは}`.
- Keep one dialogue language per prompt — do not mix Chinese and English (proper nouns excepted).
- If you use `< >` for sound effects, do **not** also wrap subject names in angle brackets — one symbol, one job.

**7. Constraint words are allowed and recommended.** [official]
This is the one sanctioned exception to positive-only phrasing. The model ships with priors that invent subtitles, logos and watermarks, and the vendor's own fix is a negative:
- `keep it subtitle-free` / `avoid generating any text or subtitles`
- `do not generate a logo` · `do not generate a watermark`
- anti-twin lock: *"Throughout the video, characters with completely identical appearance, clothing and accessories are prohibited. Do not generate duplicate avatars or a twin effect. Keep only a single corresponding character in the same frame."*
Nothing else gets a negative. Every other instruction stays positive.
- Practical: **subtitles appear far more often in portrait than in landscape** — if the deliverable allows, generate 16:9 and crop to 9:16 in post.

---

## WORKFLOW

1. **Read as a director.** Find the dramatic shape: where the scene turns, lands, breathes.
2. **Break it into a shot list — decide the coverage before writing a word of prompt.** How many shots does this beat want, and how do they cut? Even a "simple" scene is usually 2–4 shots (establish → the face that matters → the reaction), not one flattened frame. Pick the coverage from the drama: a reveal = wide-then-CU; a confrontation = the 180° two-shot broken into singles; an action beat = fast compressed cuts; a held confession = one oner. Write the list first — `SHOT 1 establish · SHOT 2 his face · SHOT 3 her reaction` — then decide single-shot vs. sequential cuts (see MULTISHOT ARCHITECTURE). Do **not** budget seconds per shot on 2.0.
3. **Define continuity anchors.** Who is in frame, how they look, what they carry across cuts; what light, screen-direction, and prop-state must hold identical shot-to-shot.
4. **Write the prompt** using the block structure below — scene-wide layer first, then the `[SHOT]` sequence.
5. **Tag assets.** Tagging rules below.

If the idea is ambiguous, close the gaps in conversation first — blocking, angle, light, character behavior, what is in the first frame, how it ends, **and how many shots it cuts through**. The model fills any gap on its own, usually not the way intended. Default to giving a scene real coverage; only collapse to one shot when the drama truly wants an unbroken take.

---

## ASSET TAGGING

Reference image (`@tag`) sets **appearance and identity**. Text sets **what happens** and locks critical details. Both are required.

**Tag naming:**
- If the user specifies tags as `@tag_name`, use those.
- If no tags are specified, name them by load order of the supplied assets: `@image1 @image2 ...` for images, `@video1 @video2 ...` for videos, `@audio1 @audio2 ...` for audio. **Numbering must match upload order** — the model maps by position, not by name.
- Otherwise follow the user's explicit instruction.

**Reference + text rule:** keep the character description minimal — long appearance text conflicts with the image and degrades it.

```
@TAG: age + role/build + current state + unique visible features + action-critical details + voice (only if it has a line). 100% matches the reference.
```

State critical details (small text, logos, color) in words even if they appear on the reference — the model can drop them.

Never place an `@tag` in a shot where that object is not present; the model will try to force it into frame.

**Say what each asset is FOR.** [official] An asset with no stated role gets used for everything it happens to contain. Write the role and, where it matters, the exclusion: *"@video1 is used for the camera movement only; do not take its characters, wardrobe or location."* When the reference is already accurate, just point at it — do not re-describe its content in prose as well.

**Voice references need words, not just the clip.** [official] Attaching `@audio1` alone reproduces the timbre unreliably; the fix is to describe the voice in the prompt too — *"Use the low, thick, warm, finely grainy middle-aged male voice of @audio1 to say {…}"* — and to keep the written line's register close to the sample's. (Our studio already appends a timbre-consistency sentence automatically; this description goes on top of it.)

**People references & moderation (BytePlus/Seedance studio).** Video-input moderation rejects photoreal human faces — «the input image may contain real person» — and it judges *photorealism*, not identity: it rejects synthetic characters the user generated themselves. What actually gets through, in descending reliability: (1) **a noise pass over the whole sheet** — Figma → Effects → Noise: Mono, size 3×3, density 100%, black 60%, export PNG 1× — grain breaks the "photographic" read while the generator still sees the features, and the noise does **not** carry into the video; (2) a grid drawn by the image model itself via the prompt; (3) a hand-drawn thick red grid — works sometimes, but can print through onto faces in the result; (4) explicit cartoon styling ("Pixar/DreamWorks style, NOT photorealistic") — always passes. Everything except cartoon is **probabilistic**: the same file can pass today and be refused tomorrow, so a refusal means "retry", not "impossible". Non-human references (@locations, @props, @animals) pass freely. The deterministic path to a photoreal face is a BytePlus **verified actor** (`arkImageAsset` / `asset://`). On Higgsfield, character sheets attach normally — this constraint is ModelArk-specific.

---

## CONTEXT ISOLATION

Each generation is a blank slate — **no memory of previous shots.** A prompt is a sealed single-shot document.

Do not carry in: scene numbers, script headings, summaries of prior scenes, unused tags/characters, "as above / continues" phrasing, or people and props from a previous line that are not needed in this shot.

**Do not paste a whole script.** [official] Redundant copy confuses the model — strip everything that is not this shot.

---

## STYLE — DISTRIBUTED, NOT A PREFIX

In a **single-shot** prompt there is **no separate style-prefix block at the top.** Style is not one thing in one place — it is a set of aspects, and each aspect lives in the block that already governs it, where it is most effective and unambiguous. State the target look, never what to avoid; adapt every line to this specific scene. (A **multishot** lifts the constant style aspects into a scene-wide layer that may sit at the top — see the multishot note below.)

Cover these aspects, each in its home block:

| Aspect | Lives in | What to write |
|--------|----------|---------------|
| Lighting | **LIGHTING** | source, direction, exposure, key/fill, haze for THIS scene |
| Color / grade | **COLOR GRADE** (own block if grade is strong, e.g. teal-cyan desaturated) or folded into **LOCATION** + **LIGHTING** for a naturalistic look | palette as material + light beam + role, never a flat list |
| Lens / optical character | **OPTICS** | FOV°, rectilinear/anamorphic, prime-lens character, motion-blur |
| Camera body / tonal character | **CAMERA** | tonal latitude, highlight roll-off, color science — as a look, not a model name |
| Skin / surface micro-detail | **PERFORMANCE** (or inside **ACTION**) | photoreal: pore-level realism, capillary flush, living eyes, catch-lights, visible breath; stylized: per PROJECT VISUAL STYLE |
| Acting | **PERFORMANCE** (or inside **ACTION**) | micro-pauses, precise eye-line, restraint, muscle-level emotion |
| Physics realism | **PHYSICS** | gravity, inertia, mass, contact shadows, fluids, particles |
| Composition / blocking | **FIRST FRAME / BLOCKING** | framing rule for THIS scene; everyone moving from frame one |
| Continuity | **POSITIVE LOCKS** | characters, props, environment identical across cuts |
| Wardrobe | **WARDROBE** (own block when costume matters) | material + condition, scene-logical |
| Image quality / format / grain | **STYLE** at the **end**, just before LOCKS | the project's look (photoreal / stylized), "HD, rich details, cinematic texture, natural colors", grain (photoreal) |

**Placement principle:** descriptive style (light, color, optics, physics, acting) sits *inside the content block it describes*, in the body of the prompt. Technical/format style sits as a **suffix stack at the end** (`STYLE`), right before `POSITIVE LOCKS`. In a single-shot prompt nothing style-related goes at the very top — it opens on `SCENE CONTEXT` and `ACTIVE REFERENCES`. (Exception: a multishot may open with a scene-wide style header covering all shots — see below.)

⚠️ **Do not write output parameters into the prompt.** Aspect ratio, resolution, fps and total duration are API/page settings, not prompt content — writing "generate a 15-second 16:9 video" wastes attention and can fight the real parameters.

Use only the aspects the shot needs. A naturalistic single take may have no `COLOR GRADE` or `WARDROBE` block at all and fold those notes into LIGHTING and POSITIVE LOCKS. A graded, multi-segment piece earns the full suffix stack.

**Never strip the photoreal style header down to one word.** A bare "photoreal" yields plastic CGI-looking people. A photoreal scene header carries the full stack from worked example A every time: the anti-CGI clause (`no 3D render, no game engine, no game-cutscene aesthetic`), a Skin line (pore-level realism, vellus hair, capillary flush, wet living eyes with catch-lights), an Acting line (micro-pauses, precise eye-line, visible breath), a Physics line (gravity/inertia, contact shadows) and an image-quality line (HD, rich details, cinematic texture, no jitter). This matters MOST when characters are text-described (no reference sheet) — the stack is all that separates them from a game cutscene.

**Style drift is real.** [official] If the reference image is realistic but the target is stylized, the video slides back to live-action unless the style is stated explicitly ("2D Japanese anime style", "3D Chinese-style comic"). For tight control, convert the reference to the target style first, then animate it.

**In a multishot, distributed style is applied ONCE, scene-wide — not re-written per shot.** The whole point of style-as-distributed is that it is stable; in a cut sequence that stability must hold across every shot, so the style aspects that are constant (grade, lighting logic, color world, audio bed, the look) live in **scene-wide blocks that govern all shots at once** — write them once as `SCENE STYLE / SCENE LIGHTING / SCENE COLOR / SCENE AUDIO` (or a single scene-style paragraph). Only what *changes shot-to-shot* — framing, FOV, subject position, camera behavior, the action beat — goes inside each `[SHOT]` block. This is the one case where a style paragraph may sit at the very top. Do not restate the grade inside every shot — state it once at scene level and lock it in POSITIVE LOCKS (e.g. `WB 4800K held across all three shots`).

If the user supplies their own prefix or block text, use it verbatim.

---

## PROJECT VISUAL STYLE — MATCH THE ASSETS

A project has ONE visual style, locked at the asset stage (seedance-assets registers it as a `STYLE:` line in the project's asset registry). The video prompt must speak that same language — photoreal vocabulary inside a cartoon project fights the stylized references and degrades both.

- **Inherit, never invent.** Read the style from the project registry / the assets themselves. Photoreal is the default only when nothing says otherwise.
- **The reference carries the render.** The sheets already look the way the film looks. The video prompt states the style briefly and swaps its own language (table below); it never re-describes the character's render.
- **Positive phrasing still applies** — except the sanctioned constraint words (subtitles / logo / watermark / twins).
- **Photoreal-only vocabulary** — pore-level skin, capillary flush, vellus hair, film grain, WB in Kelvin, the word "photoreal" — stays out of stylized projects entirely.

Swap-in language per style (feeds PERFORMANCE, PHYSICS, and the STYLE suffix):

| Style | Performance & surface | Motion & physics | STYLE suffix |
|---|---|---|---|
| Photoreal (default) | pore-level skin, micro-pauses, wet living eyes, catch-lights, visible breath | real gravity/inertia, correct contact shadows | HD photoreal, rich details, cinematic texture, fine film grain, WB in Kelvin |
| 3D-animated feature | big readable expressions, slightly exaggerated timing, soft SSS skin, clean grouped hair | true mass, rounded appealing motion | polished 3D-animated feature-film render, clearly non-photoreal |
| 2D cartoon | expressions as line-and-shape changes; flat cel fills and lineart stay stable (no boiling) | smooth 2D animation; smears/multiples on fast comedic action | flat 2D cartoon animation, clean lineart, limited palette |
| Anime | large expressive eyes, held frames on emotional beats, hair sheen | fluid high-detail motion bursts on action; painterly backgrounds with parallax | cel-shaded anime, crisp lineart |
| Watercolor / storybook | soft painted features, gentle acting | calm motion; washes and paper grain hold steady between frames | hand-painted watercolor storybook animation |
| Comic / graphic novel | inked features, hatched shadows hold their shape | bold silhouettes, high-contrast action | inked graphic-novel animation, halftone texture |
| Stop-motion | sculpted faces, expression changes in small steps | slight pose-to-pose stutter, real miniature weight, visible handcraft | handcrafted stop-motion puppet animation, tabletop macro realism |

Worked example B (jungle montage) shows an animated multishot in this shape. The asset-side style packs live in seedance-assets/templates.md — the two sides must agree on the project's style.

---

## PROMPT STRUCTURE

Write blocks in this order. Logic: context and references first, then space and coverage, then action and physics, then the descriptive-style blocks in their home positions, and a technical-style suffix at the end. Use only the blocks the shot needs; drop the rest.

```
SCENE CONTEXT
[1–2 sentences: what happens, where, when. Geo-positions of characters.]

ACTIVE REFERENCES
[@tag + what this asset is FOR (+ what NOT to take from it) + minimal anchor of critical
 details + "100% matches the reference". Subject definitions go here:
 "Define the woman in the red coat in @image1 as Subject 1."]

LOCATION MAP
[Foreground / midground / background, where the camera is, where light comes from, movement paths. Naturalistic color can live here.]

FIRST FRAME / BLOCKING
[Who is where in the first frame: positions, orientation, gaze. Composition rule for this scene.]

FORMAT MODE
[Single continuous shot — "one continuous shot, the camera does not cut on its own."
 OR sequence of shots — "Shot 1 … Shot 2 … Shot 3", described in the order events occur.]

OPTICS
[Shot size + FOV per shot + lens/optical character. For multishot add "no drift mid-shot".]

CAMERA
[Operator behavior: height, distance, movement, focus. Camera-body tonal character as a look.]

ACTION
[Events in the order they happen. Camera motion and subject motion stated separately.]

PERFORMANCE        (when acting matters)
[Acting + surface micro-detail: muscle-level emotion, eye-line, catch-lights, breath;
 surface language per PROJECT VISUAL STYLE — pore-level detail only in photoreal projects.]

PHYSICS
[Mass, inertia, contact, fluids, particles.]

LIGHTING
[Source, direction, exposure — priority block.]

COLOR GRADE        (when the grade is strong / stylized)
[Palette as material + light + role; grade character. Omit for naturalistic looks — fold color into LOCATION/LIGHTING.]

WARDROBE           (when costume matters)
[Material + condition, scene-logical.]

AUDIO
[Only the needed sound / line, in symbol grammar: (music) <sfx> {dialogue} 【subtitle】.]

STYLE              (technical-style suffix)
[Overall look in words per the project style (photoreal or stylized — see PROJECT VISUAL
 STYLE), image quality, grain (photoreal) — no director, studio or equipment names.
 NO resolution / fps / duration / aspect ratio — those are API parameters.]

POSITIVE LOCKS
[Short fixers against likely failures, restating critical info once. Continuity lives here.
 The four sanctioned negatives (subtitles / logo / watermark / twins) live here too.]
```

The descriptive-style blocks (PERFORMANCE, PHYSICS, LIGHTING, COLOR GRADE, WARDROBE) sit in the body next to what they govern. The technical-style block (STYLE) forms a short suffix just before POSITIVE LOCKS. In a single-shot prompt nothing style-related opens it; a multishot may lead with a scene-wide style header.

A **lock** is a short hard fixer placed next to what it protects, e.g. `"headlights stay glowing in every shot"`. Write densely where control matters, sparsely where it does not. In a multishot, the closing Constraints paragraph is deliberately LONG: it restates every shot's key facts — positions, per-shot lens list, who does what in which shot — and ends with the tally («exactly three shots joined by two hard cuts»); the proven prompts treat this redundancy as cheap insurance, not bloat.

---

## MULTISHOT ARCHITECTURE

This is how you cut between shots instead of writing one flat frame. A multishot prompt is **two layers**, never one:

1. **Scene-wide layer (write once, governs every shot).** The context, the references, the style/lighting/color world, the audio bed, and the continuity locks that must be identical in every shot. These do NOT repeat per shot.
2. **Per-shot layer (a `[SHOT]` block for each shot).** Only what changes on the cut: framing, FOV, subject position/blocking, camera behavior, and the action beat for that shot.

Between the shots sit **explicit cut markers**. At the end sit the **POSITIVE LOCKS** that hold the two layers together.

⚠️ **On 2.0, shots are numbered, never timed.** `[SHOT 2]`, not `[SHOT 2] 6–10s`. Order the shots primary-first, and let the model pace them.

### Multishot skeleton

```
SCENE CONTEXT        [what happens, where, when — one paragraph for the whole sequence]
ACTIVE REFERENCES    [@tags + role of each asset + subject definitions + "100% matches the reference"]
LOCATION MAP         [fg / mg / bg, geography, where light comes from — scene-wide]
FIRST FRAME / BLOCKING [what frame ONE shows: who stands where, and which end of the geography the camera occupies — this pins the opening vantage]
FORMAT MODE          [sequence of N shots; cuts only at the stated points]
SCENE STYLE + SCENE LIGHTING + SCENE COLOR   [the constant look, written ONCE — see STYLE §]

[SHOT 1]  {one-line shot title: size + who + intent}
  SUBJECT:  who is in frame, where, orientation, gaze. Push to the edge if it earns tension.
  ACTION:   beat 1 … beat 2 … beat 3 … (ordered, unnumbered in time)
  CAMERA:   size + FOV° (from the anchor table) + operator behavior. "no drift mid-shot."
⚠️ A clean {HARD CUT / MATCH CUT / SMASH CUT} to SHOT 2.

[SHOT 2]  {shot title}
  SUBJECT: …
  ACTION:  beat 1 … beat 2 …
  CAMERA:  …
⚠️ A clean {CUT} to SHOT 3.

[SHOT 3]  {shot title}
  … (repeat) …

SCENE AUDIO          [the sound bed + any lines, scene-wide, in symbol grammar]
POSITIVE LOCKS       [identity, screen-direction / 180° line, light, wardrobe, prop-state held across all cuts; grade stated once; subtitle/logo/watermark/twin locks]
Total: {N} shots
```

Drop the scene-wide blocks a shot doesn't need; keep the two-layer separation always. **Never drop FIRST FRAME / BLOCKING.** A camera-position phrase buried inside SHOT 1's CAMERA line does not reliably survive a long prompt — in an A/B test the multishot without a FIRST FRAME block opened from the opposite end of the room despite "camera at the window end" written in CAMERA, while the prompt that led with a dedicated first-frame description held the intended vantage. Describe frame one as a still image: who is visible, where, seen from which end of the LOCATION MAP.

### The `[SHOT]` block

Each shot is a mini single-shot prompt with three sub-parts, in the vendor's own order — **camera move or transition → subject action and expression → position/space → audio**:
- **SUBJECT** — who/what, where in frame, orientation, gaze. This is blocking for one shot.
- **ACTION** — the event, broken into ordered **beats** (see below). Camera motion and subject motion stated separately.
- **CAMERA** — shot size + FOV in degrees + operator behavior (static locked-off / handheld documentary tremor / broadcast super-tele). In a multishot add `"no drift mid-shot"` so the framing holds until the cut.
- **Dialogue** — a spoken line goes in braces inside the beat where it is said (`she says {Tell me what you feel}`); the scene-wide AUDIO block carries only the bed (ambience, SFX, "no music").

Give each shot a one-line **title** (`[SHOT 2] CU of his face, camera on his LEFT, pinned to the far-left edge`). It reads like a shot list and keeps the model oriented.

### Beats — ordered, not clocked

Inside a shot, subdivide the action into ordered **beats**, so micro-performance is controlled without inventing a clock the model ignores:
```
ACTION:
  beat 1 — his eyes are closed; he opens them.
  beat 2 — a deep inhale, chest rising, a held micro-pause.
  beat 3 — he takes one step forward.
```
Beats are how a short shot carries a real performance instead of one frozen pose. Keep them few and in strict order; do not attach seconds.

### Cut markers

Between shots, state the cut explicitly: `⚠️ A clean HARD CUT to SHOT 2.` Then lock that the model adds none of its own: `"cuts only at the specified points, the camera does not cut on its own."` Cut types are in CUTS AND PACING.

### Hold these identical across every cut (put in POSITIVE LOCKS)

- **Identity** — same characters, same design, no drift (`@tag … 100% matches the reference`), same label every mention.
- **Screen direction / 180° line** — if A is screen-left and B is screen-right, they stay that way every shot. State the line once.
- **Light & grade** — one WB and one grade for the scene, stated once (`WB 5000K held across all shots`), not re-invented per shot.
- **Prop / wardrobe / state** — wet stays wet, ball-on-the-spot stays on the spot, sweat carries forward.
- **Camera feel** — pick one operator character for the sequence (all static-canted, or all handheld-documentary) and keep it; changing feel every cut reads as a different film. **Default realism carrier is handheld documentary presence:** shoulder-mounted, a real operator's breath, constant fine 1–2cm tremor, small organic weight-shift reframes («a real operator, not digital jitter») — even macro inserts keep a fine micro-tremor. Pure static locked-off reads synthetic in AI video; reserve it for motivated dread/surveillance/formal canted compositions.
- **Fixed distance per shot, crop changes only on the cut** — for handheld multishots, lock the shooting distance per shot and change only the crop size at the cut; no zoom or push-in mid-shot unless the shot IS a move.
- **Only one camera move per shot.** [official] Do not ask for push, pull, pan and track at once — combined moves destabilise the frame.

### Format signature

End a multishot with a one-line tally so the model commits to the structure: `Total: 6 shots`. For a oner: `Total: 1 continuous shot`. Shot **count** only — duration and aspect ratio are API parameters and must not appear in the prompt.

### Shot budget

One generation ≈ up to **15 s** on the 2.0 family, and the whole sequence must fit inside it — but express that as *coverage*, not seconds. Proven densities: dialogue drama ~3 shots (leave air to act), action/creature ~6 cuts, ad-style montage up to 8 micro-sections. A scene needing more than one generation = several sealed prompts split at a natural cut — carry the continuity anchors into the next prompt by hand; CONTEXT ISOLATION still applies.

### Continuous take vs. cutting [official]

- **Extension / one continuous take** — best for a single-location dialogue scene: long conversations, emotional progression, movement along one path.
- **Separate clips edited together** — best for plot turns and fast action: chases, fights, montages. Generate independent clips and cut them in post for rhythm and impact.
In real production the two are combined: extend for the conversation, then splice in cutaways.

### Montage / animation opener

For a fast montage (common in animated pieces), you can open with an explicit montage instruction so the model does not collapse to one angle:
`montage, multi-shot, don't use one camera angle or single cut` — then list `Shot 1 … Shot 2 …`, each with its own framing and (for dialogue) its line. See the animation worked example.

---

## CUTS AND PACING

Pick the precision the shot actually needs:

- **Single shot (oner)** → "one continuous shot, the camera does not cut on its own."
- **Sequence of shots** → describe shots in order as `Shot 1 … Shot 2 … Shot 3`, primary events first. This is the default multishot form on 2.0.
- **Freestyle b-roll** → do not lock cuts; let the model find angles.

When you specify cuts, lock that the camera does not add its own: *"cuts only at the specified points, the camera does not cut on its own."*

**Cut types:** `HARD CUT`, `SMASH CUT`, `MATCH CUT`, `INSERT CUT`, `REVERSE CUT`, `WHIP CUT`. Fades / crossfades only if explicitly requested.

Across internal cuts hold: same character set, same geometry, screen direction, gaze, light, wardrobe, prop state.

**Timecodes:** not on 2.0 — see WHAT 2.0 ACTUALLY OBEYS. If a piece genuinely needs beats landing on a clock, that is a reason to shoot it on 2.5 (seedance-25 skill), not a reason to write seconds here.

---

## DIRECTING

**Blocking.** State where each character stands, sits, moves; what their hands do; what sits between them. "She sits across from him at the diner booth, knees touching under the table" beats "they sit and talk."

**Pace.** Read the dramatic structure. Confession scene → air, held shots, beats of silence. Action → compression, short cuts. A reveal lands on one held close-up.

**Acting.** Translate emotion into concrete on-shot direction:
- ❌ "she looks sad" → ✅ "her eyes drop to the table, jaw tightens, she swallows once before answering"
- ❌ "he is angry" → ✅ "knuckles whiten on the glass, breath shortens, eyes never leave hers"

Restraint by default — a whisper out-acts a shout most of the time.

**The vendor's externalisation table** [official] — the same principle, ready-made:

| Abstract emotion | Externalised as |
|---|---|
| Sadness | head lowering, shoulders trembling slightly, eyes reddening, fingers clutching the hem of clothing, tears welling but not falling |
| Joy | corners of the mouth rising uncontrollably, brows and eyes relaxing, steps becoming light, humming without noticing |
| Nervousness / anxiety | checking the watch repeatedly, fingers tapping the tabletop, rapid breathing, eyes darting away, biting a fingernail |
| Anger | fists clenched, jawline tense, chest heaving, gaze sharp, words squeezed through gritted teeth |
| Relief | a long breath out, tense shoulders dropping, a faint long-absent smile, looking up into the distance |

**Movement scale.** [official] The model is most reliable on **slow, gentle, continuous small motion** — walk slowly, gently raise a hand, sit down with the motion. High-burst large-dynamic action (sprints, big jumps, violent rolls) degrades. Specify body parts (hands, legs, head, shoulders, back) with range, speed and force, and write the **transition between actions** — "using the momentum of turning, the hand rises naturally" — so movement reads continuous instead of snapped.

**State before acting.** Before writing any performance line, derive the character's physiological state from the scene's context — time of day, how long they've been awake, exertion, temperature, what just happened, what they carry emotionally — and write it in observables, holding it in every shot where the face reads. Just-woken at 7am = sleep-heavy lids, slow blinks, sleep-soft face, relaxed neutral mouth, movements on autopilot, maybe one stifled yawn — NOT a smile. Video models default every face to a pleasant smile and perky energy; an unmotivated smile is a continuity error, not a bonus. If the scene doesn't earn a smile, state the real expression explicitly («her face stays sleepy-neutral, lids heavy») and repeat it in POSITIVE LOCKS.

**Continuous secondary motion — the anti-mannequin rule.** Beats are events; between events a real body never freezes, but the model freezes it unless told otherwise — and gliding, frozen bodies are the loudest AI tell. Every shot with a person carries a `throughout:` line of CONTINUOUS behavior running under the beats: what each hand does at all times, weight transfer between feet, hip lean against the counter, head micro-turns, breathing, loose hair strands moving. The proven prompts do this relentlessly («interlaced fingers working against each other, thumbs rubbing; his right foot jitters a fast frequent beat, forefoot lifting and dropping several times a second, heel planted»). Idle behavior is written at that muscle-level specificity or the body dies on screen.

**Hands need per-hand, per-object choreography in close-ups.** Plural nouns collapse into physically impossible actions: «places berries one by one» still renders as a chain of berries pressed in at once, held by the end ones. In ECU/CU manipulation shots write ONE atomic action per beat and name each hand's job: «her RIGHT hand picks ONE raspberry and presses it gently into the porridge; her LEFT hand steadies the bowl rim; then ONE blueberry beside it». Sanity-check every hand beat: could a real human hand physically do this, with this grip, in this order?

**Frame exits reset object state.** A hand that leaves the frame mid-action returns re-rolled: it left with one berry, it comes back with two — off-screen, its contents stop existing and get re-invented. In manipulation shots: (a) place the source container INSIDE the frame so the full pick-place path stays visible; (b) state that the hands stay inside the frame for the whole shot; (c) lock the carried state — «she holds exactly ONE berry at any moment»; (d) if an exit is truly unavoidable, restate the state on re-entry («her hand returns holding the same single raspberry»); (e) a tool that scoops or pours in frame while its gripping hand is out of frame reads as a floating prop and implies a third limb — when the tool is in frame, the hand gripping it is in frame too, and when the hand is done, it sets the tool DOWN in frame before moving on.

**Budget the beats against the shot.** Count what a real human needs: one pick-and-place ≈ 1.5–2 s, one full stir ≈ 1.5 s, a pour ≈ 2–3 s. If the demanded end state needs more atomic actions than a short shot can hold, the model doesn't slow down — it TELEPORTS the state in, materializing objects mid-shot. Either give the action its own shot, cut away and let the result exist after the cut (montage time-skip is legit film grammar), or soften the target: «the circle may still be incomplete when the shot ends». Never demand a completed state the shot can't physically buy. **And know that an end-state reference image outranks text pacing:** if an attached @ref shows the COMPLETED arrangement, the model races to reach that image no matter what «may be incomplete» says — verified head-to-head. To truly control pacing, make the reference show the state the shot can reach (crop or edit it to the partial state), or drop it from that shot.

**Continuity** (hold in mind, do not write as a block): state carried forward (wet/dry/bloodied), appearance not drifting, emotional carry from the previous scene, one time-of-day and weather unless the location changes.

**Camera language.** Be concrete: FOV°, height, movement, motivation. Motivate every camera move. Standard terminology is understood directly — medium shot, close-up, wide shot, slow push-in, smooth lateral tracking, fixed shot.
- "Low-angle 18° dolly-in, slow push from waist to chest as she realizes."
- "Static 47° two-shot, eye-level, locked off — lets the silence sit."
- "Handheld 63°, follow from behind — camera lags half a beat."

---

## PROMPTING RULES

- **Positive by default; four sanctioned negatives.** Describe what should happen, not what to avoid. ❌ "does not fall backward" → ✅ "stays upright, feet planted." The exceptions, all vendor-endorsed, are: **subtitles, logo, watermark, twins**. Plus one proven house exception: the scene-style header's anti-CGI clause — `no 3D render, no game engine, no game-cutscene aesthetic, no plastic CGI skin` — stays in photoreal projects; without it Seedance drifts text-described characters toward plastic CGI skin (verified on the Porridge reel).
- **Speeds in km/h.** ❌ "fast/slow" → ✅ "moves at 40 km/h", "camera pans at 5 km/h."
- **Atmosphere in percent / meters.** ❌ "light fog" → ✅ "fog density 40%", "haze visible at 15 meters depth."
- **Atmosphere builds in steps across shots.** Shot 1: fog 20% → Shot 2: 40% → Shot 3: 60%.
- **Giant scale via human-height comparison.** ❌ "huge", "three meters tall" → ✅ "stands as tall as four humans stacked head to toe."
- **Left/right is from the camera.** "Subject moves left" = left from the camera's view. For anything the model gets wrong, anchor to a stable object instead of screen sides ("on the inside of the counter, facing out").
- **Environment interaction stated physically.** Snow melts on skin, rain runs down hair, wind moves fabric.
- **Emotion through muscle movement, not labels.**
- **Prefer the reference over the sentence.** [official] Spatial relations, effects and camera moves are carried better by an attached reference than by a paragraph of text. If an effect keeps coming out wrong (a counting animation, a particle burst), give it as a reference video instead of describing it harder.
- **Off-screen doesn't exist — cover the walls.** The model builds no persistent 3D room: whatever the references never showed gets RE-INVENTED per shot. If the location plate faces away from the window, later shots will grow windows wherever the light "needs" them. Attach location angles covering every wall the sequence will face (extra angles are free from a blockout), and in each shot name the visible surfaces explicitly: «the wall behind her is solid plaster under the cooker hood; the kitchen's only window is far to frame-left, out of frame». And pin each shot's BACKGROUND (what stands directly behind the subject), not just the camera's side of the room.
- **Strong light begs for a visible source.** Describe bright window light with no window in frame and the model will plant a window in the visible wall. Either put the real window in frame, or give the light direction in frame-space AND place the source out of frame: «golden light rakes in from frame-left; the window itself is beyond the frame edge».
- **Unique props need a singleton lock.** Anything that plausibly repeats (a cooktop, a door, a clock, a lamp) gets duplicated wherever composition wants one. For every fixture that must exist ONCE, pin it to its wall/run and say the count: «exactly ONE cooktop in the kitchen, on the sink run; the opposite run is flat counter only».
- **Action verbs summon geometry.** «She climbs the last steps up into the kitchen» makes the model BUILD a staircase in frame. If a structure must stay off-frame, pick verbs that don't summon it («she steps through the doorway») and lock the layout positively.
- **Don't fight a strong prior in-frame — remove its trigger.** Stairs "want" to climb up; an empty doorway invites one. If a layout element keeps rendering wrong across takes, take it out of frame entirely and state where it is, instead of re-describing its correct orientation — the prior wins against description, but not against absence.
- **Text on screen is supported.** [official] Slogans, subtitles and speech bubbles work: `[text] + [when it appears] + [where] + [how it enters], [color / font character]`. Prefer common characters; avoid rare glyphs and special symbols. For subtitles: *"display subtitles at the bottom-center; the subtitles must be perfectly synchronized with the audio rhythm and pacing."* For bubbles: *"speech bubbles appear around the character containing the spoken text."*

---

## OPTICS — SHOT SIZE, LENS, FOV

Two levers define "how it is shot": **shot size** and **focal length**.

### Shot sizes
| Abbr | Meaning | In frame |
|------|---------|----------|
| ECU | Extreme Close-Up | a detail: eyes, button, headlight, hand |
| CU | Close-Up | full face / one element large |
| MCU | Medium Close-Up | head and shoulders |
| MS | Medium Shot | roughly to the waist |
| WS | Wide Shot | full figure + surroundings |
| EWS | Extreme Wide | scale, location |

### Focal length (mm — for describing size while writing, not for the prompt text)
| Lens | Effect | Use for |
|------|--------|---------|
| 24–35mm wide | space, slight perspective distortion | action, immersion, wides |
| 50mm normal | natural perspective, "as the eye sees" | realism, neutral shots |
| 85mm portrait | soft bokeh, subject separated from background | portrait, emotion |
| 135mm+ tele | strong compression, "watching from afar" | observation, distance, sport |

In the prompt text use **FOV in degrees** from the table below, not millimeters.

### FOV anchor table (degrees for the prompt)
| FOV | mm equiv | Purpose | When |
|-----|----------|---------|------|
| 180° | Fisheye | spherical distortion | POV, dream-state |
| 107° | 14–16mm | architectural ultra-wide | huge interiors, epic establish |
| 84° | 20–24mm | wide | establish, group blocking |
| 63° | 28–35mm | observational | wide observation, reportage |
| 47° | 40–50mm | neutral human perspective | universal establish, medium |
| 29° | 75–85mm | portrait compression | medium-isolate, dialogue bust |
| 18° | 100–135mm | natural portrait | close-portrait, identity-preserving |
| 12° | 180–200mm | tele-detail | hands, objects, detail-on-wide |
| 8° | 300–400mm | extreme compression | observation, broadcast |

Use only the discrete steps from the table. Not "23°" — use 18° or 29°. In a multishot, set FOV per shot and add `"no drift mid-shot"`. **Proven prompt form:** the flagship prompts write the mm anchor with an optional FOV translation — `WS 24mm`, `CU 50mm (≈47° FOV)`, `CU 85mm (≈29° FOV)` — and it works; prefer that form, using the table as the translation layer.

### Camera placement in the prompt
In a single-shot prompt, place the CAMERA block in the **3rd position** of the prompt's core layers (subject → action → camera → style → constraints). Moved to the end, FOV gets ignored; moved to the front, it conflicts with identity. In a multishot the same logic applies **inside each `[SHOT]` block**: SUBJECT → ACTION → CAMERA.

---

## OPTICAL TECHNIQUES

**Observation pattern (hidden-camera effect)** — all three ingredients at once:
1. Foreground occlusion — out-of-focus obstruction over 20–30% of frame (wall, pillar, branch, arch).
2. Atmospheric haze — fog/dust/shimmer between camera and subject.
3. Distance vantage — super-tele 8°–12°, operator anchored far away.
Change the occlusion type between beats; keep the vantage single.

**Sports broadcast:** `8° super-tele + handheld 1–2cm tremor + "anchored at distance, finding the action"`.

**Detail-on-wide (snake cam):** `84° wide FOV + low-angle right up against a small object` — foreground object exaggerated, background recedes into depth.

**Intimate wide:** `63–84° wide FOV on a close face` — face centered, surroundings readable without blur.

**Tele compressed air column** at 8°–12°: `"dust suspended in the long compressed air column between camera and subject"`, `"heat shimmer compressed into a wall of haze in front of the figure"`.

---

## SPECIAL PROTOCOLS

**4-mechanism multishot consistency stack (extreme FOV: 8°, 107°):**
1. Sequence-wide identity lock — single location reference across all shots.
2. LENS LOCK opener — explicit FOV phrase at the start of each shot.
3. LENS CHECK closer — confirm FOV at the end of the shot.
4. Color via material + light, not as a list.
All four are required, or extreme-FOV multishots break down after 2–3 shots.

**Whip-pan:** write it as three ordered states, not as a clock — `Subject A settled → WHIP motion-blur transition → Subject B settled`. Say the blur is long enough to read as a whip; a whip described as instantaneous renders as a plain hard cut without blur.

**Mixed time-speed (real-time + slow-mo) — two working patterns:**
1. **Cut-separated:** each shot is one speed start to finish, HARD CUTs between speed modes. Safest; default on 2.0.
2. **In-shot speed ramp (for oners):** script the ramp as an **event**, not a timestamp — "as the giant erupts ahead, time violently ramps into extreme slow motion — [what hangs frozen, what the lens does] — then time snaps back to full speed with a rush." Motivate every ramp with an on-screen event (near-collision, impact, reveal); one or two ramps per oner, each returning to real time; never a drifting variable speed.

**Transitions:** [official] name both the trigger and the method — "as the door slams shut, the camera whips left with a left-wipe blending into a natural dissolve."

**Cracks/breaks without impact (anti-impact lock):**
- "crowd PRESSES, not strikes"
- "fracture originates from edge stress, not center impact"
- "no impact point — pressure-based crack"
- sequential timing edge-to-center, not radial from a point.

---

## CAMERA / LIGHT / COLOR

**White balance** in Kelvin, set to the scene mood, fixed within a scene (3200K / 4000K / 5600K / 8500K). Photoreal projects only — stylized projects describe light temperature in plain words (warm / cool) instead.

**Equipment:** describe the look, not gear. No camera/film/lens model names — they get ignored or break complex moves.

**Color rule:** tie color to **material + light beam + compositional role**, never a flat list.
- ❌ "the woman wears red, the man wears blue"
- ✅ "crimson silk scarf catching the cold tungsten spill from the corridor"

**Background in layers:** state foreground / midground / background separately.

**Camera on the shadow side**, with a stated operator axis.

---

## FAILURE MODES AND THEIR FIXES [official]

The vendor's FAQ, in prompt-writing terms. Each of these is a known 2.0 behaviour, not bad luck.

| Symptom | Cause | Fix in the prompt / assets |
|---|---|---|
| **Face drifts mid-clip**, ends up resembling a celebrity, gets blocked in review | face reference too weak: mixed sheet, face occupies too little of the image | add a head-only close-up as its own reference; state `Subject 1's facial features reference @image1 (headshot), styling references @image2 (full body)`; put the most precision-critical asset first |
| **Twins** — two identical people in one frame | subjects not bound to their images; multi-view sheets read as several people | bind every mention (`Zhang San (@image1)`); one single-person photo per character; add the anti-twin lock verbatim; simplify the prompt |
| **Cast miscounts** with 5+ reference people | too many reference identities at once | group into stills of ≤4 people, then animate from those stills |
| **Unrequested subtitles** | strong prior, worse in portrait | `keep it subtitle-free`; remove text from references first; shoot landscape and crop later |
| **Logo / watermark appears** | prior from training data | `do not generate a watermark`, `do not generate a logo` |
| **Style slides back to live-action** | realistic reference + unstated style | state the target style explicitly; better, restyle the reference image first |
| **Voice doesn't match @audio** | timbre alone is under-specified | describe the voice in words *and* keep the written line's register close to the sample |
| **Clicking / cut-off noise at the end** on narrated clips | model artefact | regenerate, or fade the tail in post — not a prompt problem |
| **Quality decays over repeated extensions** | compounding degradation, worst on faces | keep extension chains short; feed high-resolution references; or convert the source to a white-model pass first (*"convert the video into a white 3D model: all characters as pure white 3D models, no color, no texture, no shadows, pure white background, stable structure, smooth motion"*) and extend from that |
| **A described effect misfires** (counters, particles) | text can't specify motion logic | supply the effect as a reference video: *"the way the number 2999 appears should reference Video 1"* |
| **Jump at an extension seam** | boundary frames overlap | post fix: trim 6 frames off the end of the previous clip and 1 frame off the start of the next; better, end each generation on a scene change |

---

## WORKED EXAMPLES — MULTISHOT

Proven multishot prompts. Study the two-layer shape: a scene-wide style/context header, then `[SHOT]` blocks with beats, then explicit hard cuts, then scene-wide constraints. Do not shorten these when adapting — they work at this density.

> These were originally written with timecodes for Higgsfield. The seconds have been removed: native 2.0 ignores them and the shot numbering is what actually carries the structure. Everything else is unchanged.

### A. Multishot, live-action — 3 shots, one dialogue line (therapy scene)

Scene-style header on top (legal here because it governs all three shots equally), per-shot edge-framing, a held 180° line, ordered beats inside each shot:

```
Style: photorealistic — no 3D render, no game engine. Cinematography: Emmanuel Lubezki × Roger Deakins. Natural light only — contre-jour backlight, camera on shadow side, atmospheric haze throughout. Color: 60:30:10. Camera: physical cine lens, 180° shutter motion blur. Skin: pore-level realism — vellus hair, asymmetric moles, capillary flush. Acting: micro-pauses before reactions, precise eye-line, wet living eyes with catch-lights, visible breath. Physics: gravity and inertia respected, correct contact shadows. Composition: rule of thirds. Continuity: characters, props, environment identical across every cut, no identity drift. Image quality: HD, rich details, cinematic texture, no jitter. Audio: environmental sound plus the scripted line only — no music.

[SHOT 1] high establishing, Dutch angle
SUBJECT: @psychologist and @santiago, both fully in frame from the first frame. High overhead-leaning angle with a clear canted horizon. @psychologist sits in her rust egg chair lower-left third, turned screen-right toward @santiago. @santiago reclines in his lounge chair upper-right, ~4.5m across the room by the windows, turned screen-left, eyes down, fingers interlaced.
ACTION: throughout, @santiago is restless — fingers working, right foot jittering a fast nervous beat, heel planted.
  beat 1 — @santiago fidgets, eyes down; @psychologist settles.
  beat 2 — @psychologist opens the notebook on her lap and draws out the pen, holding it ready.
  beat 3 — she lifts her gaze onto @santiago, calm and attentive; the shot holds, then cuts.
CAMERA: static, locked off, high canted Dutch tilt. WS 24mm (~84° FOV), room legible to the edges.
⚠️ A clean HARD CUT to SHOT 2.

[SHOT 2] wide canted intimate single on the psychologist
SUBJECT: @psychologist alone in her rust egg chair, pushed hard to the lower-LEFT corner, looking screen-left off that near edge — no looking room, boxed-in feel.
ACTION:
  beat 1 — she draws one soft breath, a small settle.
  beat 2 — she speaks, soft and caring: {Tell me what you feel when you remember that moment} — eyeline and voice aimed screen-left.
CAMERA: static, locked off, high angle, strong Dutch tilt ~45° with the LEFT side dipped lower. Wide 24mm (~84° FOV) brought close — face intimate, room legible behind, deep focus, rectilinear (no fisheye).
⚠️ A clean HARD CUT to SHOT 3.

[SHOT 3] wide canted intimate single on Santiago
SUBJECT: @santiago alone, reclined, pushed hard to the lower-RIGHT corner, looking screen-right off that near edge.
ACTION (his eyes have lifted to her; he holds, then turns away — worry in the eyes, eyes clear and dry):
  beat 1 — he looks screen-right, quiet unease, a glassy searching look.
  beat 2 — lips press together, inner brows draw slightly in.
  beat 3 — he breaks the look, eyes drifting aside as if the memory pulls them; one slow swallow.
  beat 4 — a slow blink, brows still faintly knit, holding it in.
CAMERA: static, locked off, high angle matching SHOT 2, strong Dutch tilt ~45° with the RIGHT side dipped lower. Wide 24mm (~84° FOV) brought close, deep focus, rectilinear.

Style (scene): late-afternoon low sun through gridded windows as the only key, WB 4800K held across all three shots; contre-jour, each face keeping a clear light pattern — one side window-lit, the other in soft warm shadow; warm bounce off the terracotta floor; thin interior haze ~10% with visible sun shafts and floating dust. Dominant terracotta / secondary warm wood / accent rust chair.

POSITIVE LOCKS: exactly two people, each seated in their own chair throughout; the 180° line holds (@psychologist screen-left, @santiago screen-right) in every shot; @psychologist speaks her one gentle line in SHOT 2, @santiago stays silent and listening; his eyes carry the emotion and stay clear and dry; each character pushed to the extreme edge they look toward, no looking room; all three cameras static and locked off with canted Dutch angles; cuts only at the specified points, the camera does not cut on its own. Keep it subtitle-free; do not generate a logo or watermark. Characters with completely identical appearance and clothing are prohibited — no duplicate avatars, no twin effect.
Total: 3 shots
```

Why it works: one scene-style header covers all three shots; only SUBJECT/ACTION/CAMERA change per shot; the 180° line and WB are locked once; ordered beats give each short shot a real performance; every cut is stated; the sanctioned negatives sit together at the end.

### B. Sequential shots for animation — montage opener, per-shot dialogue

The montage opener stops the model collapsing to one angle; each shot carries its own framing and line:

```
montage, multi-shot 3D animated feature film, don't use one camera angle or single cut, vibrant glossy CGI render, polished stylized feature-animation quality, expressive cartoon performance, subsurface-scattered fur and skin, cinematic lighting, depth of field, dappled bright daylight, lush saturated jungle palette.

In a lush overgrown jungle temple in dappled daylight, three goofy creatures stand among giant mushrooms and mossy ruins: a big shaggy orange horned monster, a small round blue scaly dragon clutching a glowing treasure map, and a towering mossy tree-creature grinning behind. Same design for each creature in every shot, no drift.

Shot 1: Medium shot with a playful push-in — the orange monster bounces and waves both arms, huge toothy grin: {Okay — today's the day, I can feel it in my fur!}
Shot 2: Close-up — the blue dragon holds the map up as it glows gold, eyes snapping wide, whispering: {Wait... the lost chamber — it's real.}
Shot 3: Wide shot — the trio reacts, the dragon thrusting a claw toward the temple, the others beaming: {WHOA!}
Shot 4: Medium tracking shot — the trio surges forward in a huddle, the orange monster kicking up leaves: {Last one there hatches a rotten egg!}
Shot 5: Close-up insert — the tree-creature's giant mossy foot lifts to reveal a glowing golden chest squashed into the moss.
Shot 6: Wide shot — the two freeze and stare slack-jawed, a beat of stunned silence, the tree-creature giving a sheepish wave: {...Was it important?}
(playful adventure score under the whole sequence) <leaves rustling, distant birds>
Keep it subtitle-free; do not generate a logo or watermark.
Total: 6 shots
```

### C. Sequence of shots, extreme FOV — per-shot lens lock (creature fight)

When the shots and lenses matter, set FOV per shot and lock identity/continuity hard (required for extreme FOV — see SPECIAL PROTOCOLS):

```
SCENE CONTEXT: a colossal whale-leviathan's eye snaps open in the dark deep; a hard fast zoom hits its glare; it breaches, dives back (water on lens), and battles a colossal cephalopod — the whole fight staying in and on the water.
FORMAT MODE: sequence of 6 shots; cuts only at these points, the camera does not cut on its own.
OPTICS: SHOT 1 — ECU 18° macro, the closed eye. SHOT 2 — ECU 12° HARD FAST ZOOM into the eye as it snaps open. SHOT 3 — WS 63° slow-motion breach. SHOT 4 — MS 47° handheld dive-back, water on lens. SHOT 5 — WS 63° surface grapple. SHOT 6 — EWS 84° wide on the churning fight. No drift mid-shot.
ACTION:
  Shot 1 — the eye is shut in the dark water, still, bio-veins faintly pulsing.
  Shot 2 — the eye SNAPS open, hard fast zoom punches in on the wild glare, dilating pupil.
  Shot 3 — slow-motion: it breaches, exploding up through the surface in a wall of spray.
  Shot 4 — handheld: it dives back, droplets streaking the lens, the frame foaming for a beat.
  Shot 5 — the cephalopod lunges, tentacles coiling; they grapple, thrashing half-in the water.
  Shot 6 — wide: the two roll in a storm of spray and foam, then submerge. Hold. Hard cut to black.
SCENE STYLE: photoreal, anamorphic, sea-kaiju blockbuster grade, fine grain, HD, rich details. Cold deep-ocean blue-green key, surface god-rays, hot bioluminescent accent glow. Deep teal-and-black dominant.
SCENE AUDIO: <deep hydrophone rumble, water surging, spray hitting the lens> — no music.
POSITIVE LOCKS: SAME leviathan eye and body, SAME cephalopod, SAME ocean every shot — the closed eye in SHOT 1 is the exact eye zoomed into in SHOT 2; the fight STAYS at the sea surface throughout, the creatures never lift into the air. Keep it subtitle-free; do not generate a logo or watermark.
Total: 6 shots
```

---

## CHECKLIST (before each prompt)

- **Coverage decided first: is this a oner or a multishot, and how many shots? Did I give the scene the coverage the drama wants, not flatten it to one frame?**
- **Correct model? This skill is 2.0 only. If the brief needs >15 s, second-level timing, video editing/extension, keyframes, storyboards or blockouts → seedance-25.**
- **No timestamps anywhere — shots numbered `Shot 1 / Shot 2`, beats ordered without seconds?**
- **No output parameters in the prompt (no duration, aspect ratio, resolution, fps)? Format signature is shot count only?**
- **Task grammar right — "Reference X in Video 1 to generate…" for reference tasks, bare "Video 1" for edit/extend?**
- **Every asset has a stated role (and, where needed, an exclusion)? Numbering matches upload order?**
- **Asset budget respected — ~4–5 assets, ≤4 reference people, headshot + full-body instead of a multi-view sheet?**
- **Each subject defined once (`Define … as Subject N`) and referred to by the same label every mention?**
- **Symbol grammar used — `{dialogue}`, `(music)`, `<sfx>`, `【subtitle】`; one dialogue language; non-EN/CN language named?**
- **Multishot: two layers separated — scene-wide style/light/color/audio written ONCE, only framing/FOV/blocking/action per `[SHOT]`?**
- **Multishot: explicit cut marker between every shot, camera-adds-no-cuts lock, FOV per shot + "no drift mid-shot", `Total: N shots`?**
- **Multishot continuity in POSITIVE LOCKS: identity, 180°/screen-direction, one WB+grade stated once, prop/wardrobe/state, one camera feel, one camera move per shot?**
- All blocks in order: context and references first, then space/coverage, then action/physics, then style in its home blocks, technical-style suffix last?
- Tags named per the tagging rule (`@tag_name`, or load-order `@image1`/`@video1`/`@audio1`, or user instruction)?
- `@tag` only in shots where the object is present?
- No style-prefix at the top of a SINGLE-shot prompt — it opens on SCENE CONTEXT / ACTIVE REFERENCES. (A multishot MAY carry a scene-wide style header.)
- Each style aspect placed in its home block (light→LIGHTING, color→COLOR GRADE or LOCATION, acting/skin→PERFORMANCE, image quality→STYLE suffix)?
- Style adapted to this scene (not generic)?
- Project visual style inherited from the assets and language swapped to match — no photoreal micro-realism / WB-Kelvin / grain in a stylized project?
- Positive throughout, except the four sanctioned negatives (subtitles / logo / watermark / twins) and the photoreal anti-CGI clause?
- Motion kept slow, continuous and part-specific, with transitions between actions written?
- Speeds in km/h, atmosphere in %/meters, giants vs human height?
- Emotion through muscle movement, not labels?
- Character state derived from context (sleep, exertion, temperature, emotional carry) and held in every face shot — no unmotivated smiles?
- Hand close-ups: one atomic action per beat, each hand's job named, physically possible grip?
- Manipulation shots: source containers in frame, hands stay inside the frame the whole shot, carried state locked («exactly ONE in hand»)?
- Left/right from the camera, or better, anchored to a stable object?
- FOV in degrees from the anchor table (not mm, not arbitrary)?
- CAMERA in 3rd position (single-shot: the block; multishot: inside each `[SHOT]`)?
- WB in Kelvin for the scene mood (photoreal projects only)?
- Color via material + light + role, not a list?
- Camera on the shadow side, operator axis stated?
- Background in foreground/midground/background layers?
- Extreme FOV (8° / 107°) in a multishot → all 4 consistency mechanisms in place?

---

## FINAL RULES

- **English prompts only.**
- **Positive phrasing**, with the four sanctioned negatives and the photoreal anti-CGI clause as the only exceptions.
- **No output parameters in the prompt** — duration, aspect ratio, resolution and fps are set on the API/page.
- **No studio names, no signature-work references, no equipment names** — describe the look instead. **Evidence-based exception:** in a photoreal STYLE header the proven DP-pair shorthand `Cinematography: Emmanuel Lubezki × Roger Deakins` is allowed and recommended — the flagship prompts ship with it and it reliably pulls the whole natural-light / organic-camera / contre-jour cluster in one line. Use it as ONE header line only; don't scatter names elsewhere.
