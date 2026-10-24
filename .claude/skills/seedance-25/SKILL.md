---
name: seedance-25
description: >
  Seedance 2.5 prompt-writing system (dreamina-seedance-2-5 on BytePlus ModelArk). Use whenever the user wants a Seedance 2.5 prompt, or the brief needs anything 2.0 cannot do: clips longer than 15 seconds (2.5 goes to 30), second-level timing, up to 50 reference assets, video EDITING of an existing clip (add/remove/replace elements, change dialogue or music), video EXTENSION forward or backward, KEYFRAME control, multi-panel STORYBOARDS, or 3D BLOCKOUT/previz rendering. Also use it for ordinary text-to-video and reference-to-video when the user has picked the 2.5 model.
  Trigger on "промпт для 2.5", "Seedance 2.5", "нужен ролик на 20 секунд", "доснять / продлить видео", "поменять в видео", "убрать музыку из ролика", "раскадровка по кадрам", "по таймкодам", "оживить блокинг из Blender", "keyframes", "storyboard", "extend the video", "edit this video".
  ROUTING: for the Seedance 2.0 family (2.0 / 2.0 Fast / 2.0 Mini — 15s max, no timestamps) use seedance-clean instead; the two grammars are not interchangeable. For MiniMax H3 use minimax-h3; for Gemini Omni use gemini-omni. Assets/character sheets are made by seedance-assets.
---

# Seedance 2.5 Prompt Writer

Seedance 2.5 is not "2.0 but nicer" — it is a different instrument with a different prompt grammar. 2.0 was a leap in raw generation; 2.5 is a leap in **production control**: long form, many references, editing, extension, second-level timing.

**Source of truth.** BytePlus's official *Dreamina Seedance 2.5 prompt guide* (docs.byteplus.com/en/docs/ModelArk/2607689) and their published `sd25-pe` skill, merged with our own tested practice. Sections marked **[official]** come from the vendor. Where a rule is ours and not theirs, it says so.

**Output: always a single standalone prompt in a code block.** One prompt = one primary task. A brief that both edits a clip *and* extends it is two prompts, run in sequence.

---

## WHAT CHANGED FROM 2.0 [official]

| | 2.0 | 2.5 |
|---|---|---|
| Max clip | 15 s | **30 s** |
| Reference assets | 9 images / 3 audio | **50 total** — up to 30 images, 10 videos, 10 audio |
| Timestamps | **ignored** — shot numbers only | **integer-second timestamps supported** |
| Multi-view character sheets | counter-recommended (causes twins) | **supported** |
| Aspect ratio | six fixed ratios | any ratio in **[0.4 … 2.5]**, driven by the input assets |
| Editing / extension | basic | first-class, with `MOV` output preserving colour, brightness and A/V sync |
| Languages | EN/JA/ID/ES/PT | 10+ (still **no Russian, no Chinese** in the native list — write prompts in English) |

Practical read: if the brief fits comfortably in 15 seconds with 4–5 references and no timing requirement, 2.0 is cheaper and just as good. Reach for 2.5 when you need **length, precise timing, many characters, or an operation on an existing clip.**

---

## LOCKED VS UNLOCKED — WHICH PARAMETERS YOU STILL CONTROL [official]

2.5 splits tasks by whether the input asset dictates the output's shape. This decides what you may and may not put in the prompt.

**Locked** — the asset is placed on the output timeline, so aspect ratio (and sometimes duration) is taken from it and cannot be set:
- video **editing**
- **first frame** / **first + last frame** generation
- video **extension**

**Unlocked** — the asset is only a semantic reference, so you set ratio and duration yourself:
- reference tasks (omni reference)
- storyboards
- keyframes

⚠️ **Never write output parameters into the prompt** — no "generate a 20-second video", no "16:9", no resolution or fps. They are API/page settings; writing them wastes attention and can fight the real parameters. The one thing that *is* prompt content is the user's own creative timing ("she waits three seconds before answering").

⚠️ **First + last frame should share an aspect ratio**, or the last frame gets stretched. That is an input check, not a prompt line.

---

## PROMPT ARCHITECTURE

### The four-part spine [official]

Every 2.5 prompt, however complex, is built from these four parts in order:

1. **Asset referencing** — which asset is what, by upload order.
2. **One-sentence summary** — `Subject + Location + Event + Genre/Style + Camera movement`.
3. **Detailed plot** — segmented by `Shot N` or by timestamps; per segment: visuals, camera, action, dialogue, sound.
4. **Additional notes** — everything that must stay constant throughout: camera character, environment, lighting, sound bed, style, continuity locks.

Anything that does not belong to one of those four parts is probably noise.

### The block template for reference tasks [official]

When there are references, use the labelled-block form — it is what the vendor's own optimiser emits, and it survives long prompts far better than prose:

```
【Generation Goal】
Generate <video type / core event>. The core subject is <subject>, the principal event is <summary>.

【Reference Asset Roles】
@Image1 — <subject>'s <appearance / clothing / structure / material>; do not use <its background>.
@Video1 — <action / camera movement / pacing>; do not use <its identity, wardrobe, location>.
@Audio1 — <character>'s <timbre / dialogue / ambience / music>.

【Unused Assets】
@Image2, @Video2 are not used in this task — not for people, scenes, props, actions, camera or sound.

【Subjects and Relationships】
<Subject A> maps to @Image1 and always keeps <fixed features>.
The spatial / prop / identity relationship between <A> and <B> is <relationship>.

【Event Script】
At the start: <state of people, props, scene>.
Principal event: <continuous action>.
At the end: <positions, ownership, final visual state>.

【Maintain Consistency】
Keep <identities and counts, clothing, prop ownership, spatial directions, sound relationships> stable.
```

Simple tasks may merge adjacent blocks, but never drop the roles of assets that are actually used. Do not invent a style, a camera move or a sound just to fill a block.

### Basic template — text-only or a simple event [official]

```
<Subject> <performs the main action> in <scene and environment>.
The visuals present <style or mood>.
The camera uses <shot size, position, movement or cuts>.
The sound includes <dialogue, ambience, sound effects, music>.
```
Drop any line that isn't needed; subject and main action are mandatory.

---

## ACCOUNTING FOR ASSETS — THE 2.5 DISCIPLINE [official]

With up to 50 assets, mapping *is* the prompt. The failure mode isn't a weak description, it's an unattributed asset bleeding into the wrong subject.

- **Number by upload order.** `Image 1 / Video 1 / Audio 1` — the model maps by position. Never renumber the user's existing references, never translate them.
- **Bind every mention.** `The knight in Image 1`; `Images 1–2 are Character 1 and correspond to Audio 1; Images 3–4 are Character 2 and correspond to Audio 2`.
- **Do not put mapping inside the picture.** Writing "John" onto the character sheet and then saying "John is at school" causes confusion and duplicates. The binding lives in the text.
- **State what each asset is FOR, and what NOT to take from it.** `Refer to the spell-casting action in Video 1 and the orbit move in Video 2`; `Refer to Image 1 for lighting and filters only`.
- **When the reference is accurate, point — don't re-describe.** `Strictly refer to the actions and camera movement in Video 1 and keep the sequence consistent` beats a paragraph re-narrating the raised hand and the slow orbit.
- **List every unused asset** under `【Unused Assets】`, individually. An asset with no role gets reactivated by prompt enhancement and shows up where it shouldn't.
- **One design image = one character.** A single-person reference must not define two on-screen characters. Multi-angle images of the *same* subject are fine on 2.5 (unlike 2.0) — say explicitly how many entities exist in the final video.

**Recommended ranges** (not hard limits): 1–8 subject images; 1–5 subject videos/audios of 5–10 s each; for editing, a source clip ≤20 s plus 1–5 reference images. Over the recommendation but under the cap → proceed, but assign a role to every asset and **activate assets scene by scene** rather than letting them all fire at once.

### Scene-by-scene activation [official]

For long, many-asset pieces:

```
【Subject Definition: Character A】
Appearance and clothing: @Image1.
Fixed prop: <Prop A> from @Image3.
Allowed scenes: <Scene A>, <Scene B>.
Action reference: <action> from @Video1.
Do not use: <another subject's clothing, props or voice>.

Scene 1 | <name>
Use: <the subjects, props, scene, actions and sounds active in this scene>.
Event: <one main event>.
At the end: <observable state>.
```

**Spatial relations anchor to objects, not screen sides.** [official] "The clerk stands on the inside of the glass counter, facing out; customers A and B stand side by side on the outside" survives cuts; "screen-left" does not.

---

## TIME: STAGES BY DEFAULT, TIMESTAMPS ON PURPOSE

2.5 does obey integer-second timestamps — which makes it tempting to over-specify. The vendor's own default is the opposite.

**Default: stages.** One major state change per stage, with its end state written down:

```
【Stage 1】
At the start: <initial state>.
Main event: <one action>.
At the end: <observable state>.

【Stage 2】
Continuing from the previous stage: <what must persist>.
Main event: <one action>.
At the end: <observable state>.
```
The number of stages follows the number of events — it is not fixed at three.

**Use numeric timestamps only when** the user already wrote them, or explicitly wants timed control of handoffs, entrances/exits or beats: [official]
- whole seconds, **1-second granularity**, continuous and non-overlapping — `0-3s … 3-7s … 7-15s`, or `[1s-4s]…[4s-8s]`. No gaps like "0–3s … 5–6s".
- too little plot in a range → the model improvises; too much → excess cuts or dropped beats.
- **not** for high-frequency action ("shake the head three times a second").
- **do not claim half-second precision**, and do not invent time ranges just to fill a target duration set on the page.

**Point-in-time and relative time also work:** "at the 5-second mark, a fast left-wipe transition"; "he stands blank; after three seconds everyone around him shakes their head"; "the frame freezes for one second after the shutter clicks".

**Long clip, one shot:** if the target duration is longer than the events you have, stretch *processes* — gazes shifting, breathing, picking something up, reactions after entering — never add characters, plot events or outcomes, and never pad with repeated actions or empty shots.

**Handoffs need ownership statements.** [official] After a handover the original holder no longer has the object; at the end it is *only* in the recipient's hands. Say it in those words.

---

## SYMBOL GRAMMAR [official]

| Content | Symbol | Example |
|---|---|---|
| Music | `( )` | `(soft piano plays under the scene)` |
| Sound effect | `< >` | `<a bell rings in the distance>` |
| Dialogue | `{ }` | `{Hello, welcome back}` |
| Subtitle | `【 】` | `【Chapter One: Departure】` |

- **Dialogue language:** `language + optional accent + delivery + speaker + {line}`. Label every speaker separately — no blanket declaration at the top. If English lines risk coming out in Chinese, write "Use English." Do not add an American/British/regional accent unless asked, and never infer a language from the writing system.
- **Multi-speaker:** bind speaker + audio at each stage, and state that the others keep their mouths naturally closed while listening.
- **Silent pieces:** constrain speech, lips, sound sources and visible text together — "characters keep their mouths naturally closed, no narration, only the specified ambience, no subtitles or signage on screen".
- If `< >` marks sound effects, do not also wrap subject names in angle brackets.

**Negative control is allowed for subtitles and audio only:** `no subtitles`, `no BGM; only ambience and action sounds`, `no audio`. Everything else stays positive. Do not bolt on generic "quality packs" or stability boilerplate the user didn't ask for.

---

## SOUND DISCIPLINE — TWO SOURCES, THEN A NAMED BAN LIST

Ours, learned the hard way: the model treats score and sound design as part of "being cinematic" and adds them unasked. Left loose, the audio block is where a good take dies.

1. **At most TWO sound sources per take:** an ambience bed (wind, room tone) plus whatever physically happens in frame (footsteps, hinges, a hand on fabric). Everything else — birds, passers-by voices, bells, traffic, city atmosphere — is **added in the edit**, where it is cleaner, quieter and fully controllable. Ask a video model for "atmosphere" and you get a soundtrack.
2. **Close every audio block with a named ban list**, not a general clause: `Nothing else at any point.` followed by the categories one by one — music / score / strings / piano / drone / swell / riser / whoosh / boom / impact; crowd roar / murmur / cheering / chanting / anthem / applause; bells / traffic / horses / birds; dialogue / voiceover / narration; subtitles. A bare `no music` held on four Mini takes in the studio's own test (21.09.2026, clean spectrograms): keep the category list as insurance, not because the short form fails.
3. **Never name small everyday SFX** — a latch click, a rustle, a cable, a camera shutter, a notification. The model inflates them into designed effects with reverb. If the action must be quiet, write it silent in the action line instead: *"slowly and gently, without any snap or tug."*
4. **Never describe breathing in the performance block** — `breath catching`, `held breath`, `breathing fast` all come back as a loud theatrical gasp. Write *"her breathing stays silent, quiet and shallow"* and ban it in audio: `no breath sounds, no gasp, no sigh, no audible inhale`.
5. **Mass sounds (crowd roar, anthem, applause) push the scene into pathos** and fight the voiceover. Keep the bed small and local even in a huge exterior — wind and one distant element carry scale better than a roar.
6. **Voiceover and narration are never generated by the model.** They are recorded separately (TTS) and laid in the edit — full control of text, pace and timbre, and the model spends no attention on lip-sync nobody needs. Ban them in every prompt. The exception is an on-screen character's own line, which is dialogue, not narration.

---

## CAMERA AND PERFORMANCE

### Camera language [official]

Standard terms go in directly: shot sizes (EWS / WS / MS / MCU / CU / ECU), moves (push in, pull out, pan, track, follow, orbit, dive, tilt up, handheld shake), angles (low, overhead, first-person). Named techniques work too: one-take/long take, dolly zoom (Hitchcock), aerial, FPV, bullet time, handheld, speed ramp.

Formula when several subjects share the frame:
```
camera move + target subject + starting position/state + direction + arrival position/state
```
Never a move detached from its subject.

For niche or ambiguous terms, keep the term **and** expand it into the visible result:
```
term + target subject + visual change + foreground/background relationship + direction or speed
```
— rack focus: which object loses sharpness, which gains it. Shallow DOF: subject stays sharp, background blurs how much. Tracking shot: camera matches the subject's speed, background streaks in which direction. Focal lengths and apertures may *supplement* this; they cannot replace the visible result.

Our house additions (not vendor rules, but they hold on 2.5 too): FOV in degrees from the anchor table below; one camera move per shot; `no drift mid-shot` in multi-shot pieces; handheld documentary presence as the default realism carrier — shoulder-mounted, a real operator's breath, constant fine 1–2 cm tremor, small organic weight-shift reframes; pure locked-off reads synthetic unless the dread/surveillance/formality is motivated.

**FOV anchor table** (use the discrete steps; write `CU 85mm (~29° FOV)`):

| FOV | mm | Use |
|---|---|---|
| 180° | fisheye | POV, dream-state |
| 107° | 14–16mm | architectural ultra-wide |
| 84° | 20–24mm | establish, group blocking |
| 63° | 28–35mm | observational, reportage |
| 47° | 40–50mm | neutral human perspective |
| 29° | 75–85mm | dialogue bust, portrait compression |
| 18° | 100–135mm | close portrait, identity-preserving |
| 12° | 180–200mm | hands, objects, detail-on-wide |
| 8° | 300–400mm | broadcast, extreme compression |

### Emotion as observable performance [official]

When only a mood is given ("tense", "warm"), let the model decide the performance. When the acting must be controlled:

```
emotional direction + triggering event + observable performance + observable change in camera, light or sound
```

```
The overall emotion shifts from <start> to <end>.
After <trigger>, <subject> first shows <immediate reaction>.
Then <eyes / brows / mouth / breathing / gaze / hands> gradually <change>.
Finally <subject> expresses <target emotion> through <outward performance>.
```

Pick a few of the clearest cues — eyes, brows, mouth, breathing, gaze, hands, posture. Do not pile on every microexpression. Stage the performance by trigger only when the emotion changes more than once.

**Actions:** general first ("several sets of high knees and a tumble", "the two trade close-quarters blows"); write specifics only for the few memorable beats, and don't repeat the same action. **Expressions:** descriptive sentences, not idioms.

### Our house directing rules that still apply

- **State before acting.** Derive the character's physiological state from context — time of day, hours awake, exertion, temperature, what just happened — and hold it in every shot where the face reads. Models default to a pleasant smile; an unmotivated smile is a continuity error. Name the real expression and repeat it in the consistency block.
- **Continuous secondary motion.** Between beats a real body never freezes. Give each person a `throughout:` line — what each hand does at all times, weight transfer, head micro-turns, breathing, hair moving. Frozen gliding bodies are the loudest AI tell.
- **Hands in close-up:** one atomic action per beat, each hand's job named, physically possible grip. Plural nouns ("places the berries one by one") collapse into impossible chains.
- **Frame exits reset object state.** A hand leaving frame comes back re-rolled. Keep source containers in frame, keep hands in frame, lock the carried count ("exactly ONE berry at any moment").
- **Off-screen doesn't exist.** Whatever the references never showed gets re-invented per shot — name the visible surfaces behind the subject, and place strong light sources either in frame or explicitly beyond the frame edge.
- **Unique fixtures need a count lock.** "Exactly ONE cooktop, on the sink run; the opposite run is flat counter only."
- **Don't fight a strong prior in frame — remove its trigger.** If stairs keep growing where they shouldn't, take them out of frame and say where they are.

---

## TASK TEMPLATES

### Keyframes [official]

Keyframe images are ordinary reference images with an exact role sentence. **The role sentence is verbatim and stands alone** — do not weaken it to "reference the opening composition", and do not merge it with the action sentence.

```
Use @Image1 as the first frame.
This first frame defines the starting composition, subject positions, poses, prop states, scene and camera direction.
Use @Image2 as the last frame.
This last frame defines the ending composition, subject positions, poses, prop states, scene and camera direction.
Use @Image3 for <Subject A>'s <appearance / clothing / structure> without changing the first-frame composition in @Image1 or the last-frame composition in @Image2.

<one continuous action or event>.
The shot begins naturally from the first frame defined by @Image1 and, after a continuous action, arrives at the last frame defined by @Image2.
Maintain continuity from first to last in <identities, prop structure and ownership, scene layout, camera direction>.
```

For an ordered series, declare the order in the first sentence, then define each state:
```
Use @Image1 through @Image7 as keyframes in that order.
Use @Image1 as the first frame. This first frame defines <…>.
@Image2 defines the second keyframe: <visible state at the end of stage 1>.
…
Use @Image7 as the last frame. This last frame defines <…>.
The shot passes through those states in order, transitioning with continuous action.
```
Keyframes control **stage order and visible states** — they are semantic anchors, not frame-by-frame replication and not static holds.

### Multi-panel storyboards [official]

```
@Image1 provides the shot order and approximate compositions for an <N-panel storyboard grid>. Read it left to right, top to bottom; do not adopt its sketch style, text annotations or placeholder characters.
@Image2 defines <Subject A>'s appearance and clothing.

Shot 1: <shot size, action, scene state>.
Shot 2: <shot size, action, camera move or transition>.
…
The final visuals use <style>. The audio includes <dialogue / ambience / sfx / music>.
```
- **≤15 panels.** 18 panels produce frozen frames and scrambled order.
- Clean line-art or stick-figure boards work best; avoid cluttered, over-sharpened AI boards and heavy text.
- A grid is a plan, not a contract — the model keeps some autonomy. If exact alignment is required, use **keyframes** instead.
- For a loose concept board, the prompt can be minimal: *"construct a complete story according to the storyboard sequence and use the shots coherently."*

### 3D blockout / previz rendering [official]

First decide which kind you have.

**Coarse blockout** — simple geometry carrying paths, blocking, entrances/exits, camera, cuts, lighting changes, audio rhythm:
```
@Video1 is a coarse-blockout reference. Use it only for <action paths, blocking, camera position, camera movement, cuts, lighting changes, spatial relationships>; do not adopt its blockout appearance, materials or scene.
<Blockout subject A> in @Video1 corresponds to <Subject A>.
@Image1 defines <Subject A>'s appearance and clothing.
<Subject> completes <the action> in <the scene>.
Preserve <paths, blocking, camera movement, cuts, lighting> from @Video1.
The final visuals use <characters, scene, materials, style>. The audio includes <…>.
```

**Fine blockout** — complete structure, used for re-rendering appearance/materials/style:
```
@Video1 is a fine-blockout reference. Preserve <subject structure, actions, spatial layout, camera position, camera movement, cuts>; do not adopt its gray blockout materials, blank background or production markers.
Rerender <the subject> in @Video1 as <the final subject>, and the scene as <the final scene>.
```
- Map **every** geometric object individually to a final subject or prop.
- Exclude production markers explicitly — trajectory lines, coordinate axes, controllers, camera frustums, text.
- Simple primitives work better than half-finished detail.
- A blockout video is a *generation reference*, not an editing master — it does not become one just because it is a video.
- ⚠️ Ours: this is the Blender previz path (see the blender-previz memo) — grayboxed camera + blocking, then rendered here.

### Video editing [official]

An edit must name **one source clip as the sole editing master**, or it degenerates into a regeneration.

```
【Editing Goal】
Edit @Video1, changing only <object or region> to <target>.

【Role of the Source Video】
@Video1 is the sole editing master and governs the original scene, camera position, camera movement, action trajectories, occlusion relationships and event order.

【Role of the Target Material】
@Image1 is used for the <appearance / structure / material> of <target>; do not use <its background, people, composition>.

【Edit Objects and Scope】
Modify only <objects and regions>. The number of target objects throughout is <count>. Do not modify <what must be preserved>.
Except for the objects explicitly modified above, all other visible characters, props and background elements in @Video1 remain unchanged and are not to be replaced or removed.

【Timeline Inheritance】
<Target> inherits the timing, duration, path and speed of every appearance, movement, occlusion and exit of <original>.
All other actions, camera movement, shot changes and event order remain as in @Video1.
```

- **The scope-closure sentence is mandatory.** Either the "everything else unchanged" line above, or — when the user wants only the named objects kept — *"Except for the objects explicitly retained above, remove all other visible subjects from @Video1; do not add any unspecified objects."*
- **Motion-slot replacement** for swapping a moving subject: remove the original explicitly, have the target inherit its exact appearance timing, path, speed and occlusions, and state that the original no longer appears.
- Don't invent time segments from memory or from sparse previews of the source. A prompt raises the probability of timeline fidelity; it cannot guarantee frame alignment.

**Audio editing** — same master discipline, visuals untouched:
```
【Editing Goal】 Edit @Video1, applying <removal / replacement / adjustment> only to <speaker or sound category> in <the whole video or a time range>.
【Role of the Source Video】 @Video1 is the sole editing master and governs the visuals, actions, lip-sync timing, shots, editing rhythm, all other audio and event order.
【Audio Edit Scope】 Modify only <speaker / sound category / time range>.
【Content to Preserve】 Keep <all other dialogue, lip-sync, ambience, action sounds, visuals, shots, rhythm> from @Video1 unchanged.
```
To drop background music, say so directly — do not fabricate a replacement track. Changing language or timbre preserves the original dialogue content and timing unless the user asks otherwise.

### Video extension [official]

Ask the direction if it isn't clear — it changes everything. Keep the user's own word for the subject ("the person"), don't infer a profession from posture or clothing.

**Forward** (after the source ends):
```
@Video1 is the source video to extend forward.

Extend @Video1 forward. The first frame of the extended segment directly continues from the final frame of @Video1: maintain continuity in <subject posture and orientation>, <prop positions>, <background and spatial relations>, <camera position and composition>, <lighting>, <audio state> and <motion trajectory>.

Then, <the new action or event>.

Throughout the extension maintain continuity in <identity and clothing>, <key props>, <background layout>, <camera axis> and <the original audio environment>.
The same subject remains one continuous object throughout, without duplication or splitting; its structure and part count stay stable.
```

**Backward** (before the source begins): describe what happens first, then define the source's first frame as the explicit **end state** of the new segment — otherwise the model introduces later characters and effects too early, or keeps changing the image after reaching the target. Add: *"characters, props or effects that belong only to later parts of the source video must not appear early."*

- The single-instance / topology sentence is mandatory and must appear in the final prompt, not just in your reasoning.
- An extension only creates a new segment — it does not edit the source at the same time.
- The boundary goal is natural continuity, not pixel identity; volume may differ slightly between segments.
- Chained extensions degrade image quality (worst on faces). Keep chains short; `MOV` output preserves colour and A/V sync better than mp4.

---

## PROJECT VISUAL STYLE — MATCH THE ASSETS

A project has ONE visual style, locked at the asset stage (seedance-assets records it as a `STYLE:` line). The video prompt speaks that language and never re-describes the character's render — the sheet already carries it.

- Inherit, never invent; photoreal is the default only when nothing says otherwise.
- Photoreal-only vocabulary — pore-level skin, capillary flush, vellus hair, film grain, WB in Kelvin — stays out of stylized projects entirely.
- Photoreal projects keep the full stack, never a bare "photoreal": anti-CGI clause (`no 3D render, no game engine, no game-cutscene aesthetic, no plastic CGI skin`), skin line, acting line, physics line, image-quality line. This matters most when characters are text-described with no sheet.
- Stylized swap-ins: 3D-animated feature · flat 2D cartoon with clean lineart · cel-shaded anime · hand-painted watercolour · inked graphic-novel with halftone · handcrafted stop-motion. Match performance and physics language to the style (see seedance-clean for the full table — it is model-independent).

**Moderation on reference images** (ModelArk-wide, applies to 2.5 too): the filter judges *photorealism*, not identity, and rejects synthetic photoreal faces as well. In descending reliability: a full-sheet noise pass (Figma → Effects → Noise: Mono, 3×3, density 100%, black 60%, PNG 1×) — the grain does not carry into the video; a grid drawn by the image model itself; a hand-drawn thick red grid (can print through onto faces); explicit cartoon styling (always passes). Everything but cartoon is probabilistic — a refusal means retry. Non-human references pass freely. The deterministic path to a photoreal face is a verified actor (`asset://`).

---

## STUDIO NOTES (Longtake / Seedance Studio)

- Our 2.5 entry is `dreamina-seedance-2-5-260628`: **480p / 720p / 1080p** (no 4K), duration **4–30 s**, **30 images / 10 audio**, each audio clip 2–30 s with a **30 s combined** ceiling.
- **Video references and edits are wired into the studio for Seedance (since 21.09.2026).** A library clip mentioned as `@name` goes out as `reference_video` — up to 10 clips on 2.5, 3 on the 2.0 family, 480p or 720p, 2–30 s each and 30 s combined, billed as input seconds on top of the take: use it for motion, camera and blockouts. «Fix a detail…» on a finished take runs a 2.5 edit with the take as `@Video1` (a 1080p or 4K take is shrunk to 720p first). Extension is not implemented: that still goes through the ModelArk API or console.
- In first-frame and first+last-frame modes our server forces `ratio: adaptive` (2.5 accepts nothing else there) — matching the "locked" rule above.
- **Prompts written FOR THE STUDIO reference assets by `@name`, NEVER by `@ImageN`.** Write `@traveler`, `@loc-balcony-1914` — the studio attaches the right references itself and rewrites them to the canonical `@ImageN` on the way to the model (`buildContent`). Writing `@Image1/@Image2` forces the user to upload assets manually in the exact order and defeats the whole library; it is correct ONLY when the prompt goes straight to the ModelArk API or console. The `@name` must match the asset's name in the studio library exactly — that is what the asset registry's `@tag` column is for. First/last frame are separate slate slots, not mentions. The vendor block templates above show `@Image1` because they are written for the raw API; translate them to `@name` for studio use.
- `asset://` verified actors still need to be called `Image N` in the text; the model cannot connect an Asset ID to reference content.
- Cost is roughly 1.5× the 2.0 rate per second at the same resolution — worth a thought before generating a 30-second take at 1080p. Draft at 480p, then upscale.

---

## CHECKLIST

- **Right model?** Under 15 s, ≤5 references, no timing requirement, no operation on an existing clip → use seedance-clean (2.0) instead.
- **Exactly one primary task** — generation, editing, or extension. A hybrid brief is split into two sequential prompts.
- **Intent preserved** — character identities and counts, key props, scene, causality, spatial relations, edit target, extension direction, story outcome all unchanged.
- **No output parameters in the prompt** (duration, aspect ratio, resolution, fps). Locked-task rules respected.
- **Every used asset has one explicit role**, and every unused asset is listed individually under `【Unused Assets】`.
- **User's asset references untouched** — not renumbered, translated, deleted, or invented.
- **One design image ≠ two characters.** Entity count in the final video stated.
- **Stages by default**; numeric timestamps only when asked for, whole seconds, continuous, non-overlapping.
- **Each stage has one main state change and a written end state.** Handoffs state the ownership change.
- **Symbol grammar used** — `{dialogue}` `(music)` `<sfx>` `【subtitle】`; speaker, language and delivery bound per stage; no unrequested accent.
- **Negatives only for subtitles and audio.** No generic quality/stability packs the user didn't ask for.
- **Camera moves attached to a subject**, with start and end positions; niche terms expanded into visible results; one move per shot; FOV from the anchor table.
- **Emotion written as observable performance**, staged by trigger, without piling on microexpressions.
- **Character state derived from context — no unmotivated smiles.** `throughout:` line of continuous secondary motion per person.
- **Close-ups:** one atomic hand action per beat, each hand's job named, carried count locked, containers in frame.
- **Keyframes:** exact standalone role sentence, first and last defined separately, no promise of frame-by-frame replication.
- **Storyboard:** reading order stated, ≤15 panels, sketch style / annotations / placeholders excluded.
- **Blockout:** identified as coarse or fine, every geometric object mapped, production markers excluded.
- **Editing:** sole master named, scope + target count + preserved content + timeline inheritance, and the mandatory scope-closure sentence present.
- **Extension:** direction correct, boundary frame continuity listed, single-instance sentence present, source not edited at the same time.
- **Project visual style inherited from the assets**, photoreal stack complete or stylized language swapped in.
- **English prompt**, no studio/equipment names (the `Cinematography: Emmanuel Lubezki × Roger Deakins` header line is the one proven exception in photoreal projects).
