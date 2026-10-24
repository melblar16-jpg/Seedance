---
name: seedance-assets
description: >
  Interactive pre-production asset factory for AI filming: characters, locations, props.
  Use whenever the user wants to create or design an asset for a shoot — "сделай персонажа",
  "нужна локация", "создай предмет/реквизит/проп", "персонаж — 30-летний футболист",
  "референс для сцены", "character sheet", "asset for the film" — or asks to prepare
  reference images for a Seedance/Seedream project. Works in ANY visual style — photoreal
  live-action, 3D-animated feature (Pixar/DreamWorks-like), 2D cartoon, anime, watercolor
  storybook, comic/graphic-novel, stop-motion puppet — the skill proposes styles in the
  interview («мультик», «в стиле пиксар», «2D-персонаж», «аниме», «нарисованный») and locks
  ONE style per project. Runs a staged interview (batched questions with recommended
  defaults), then outputs generation-ready English prompts: 4-view character sheets (16:9),
  product-style prop sheets, 3/4-depth location plates — plus variants and an @tag registry.
  Companion to seedance-clean / seedance-25 (which write the video prompts that consume these assets).
  Not for writing video/scene prompts themselves.
---

# Seedance Asset Factory

Pre-production for AI film: turn a one-line idea («персонаж — 30-летний футболист») into locked, reusable reference assets — through a structured interview, not guesswork.

**Interview language: the user's language (Russian for this user). All generated prompts: English only.**

---

## WHY SHEETS EXIST

A reference sheet is an **identity lock**. Whatever is baked into the sheet (face, outfit, materials, wear) stays identical in every video shot; whatever is left out gets reinvented per scene. So the interview's real job is deciding **what to bake in and what to leave free**.

Clean input is the main secret of cinematic AI video: gray seamless backdrop, soft near-shadowless light — no dark faces, no glare in the hair, nothing for the video model to misread. **This holds in every visual style** — a 3D-animated or 2D character sheet still uses the same neutral gray backdrop and clean lighting; only the render language changes.

---

## VISUAL STYLE — one decision, whole project

The sheet's **structure is style-invariant; its render is style-dependent.** The 4-view sheet layout, gray backdrop, feet-in-frame, LOCKS paragraph, non-IP rule, verbatim on-asset text, 60:30:10, scale anchors, 3/4 location depth — identical whether the film is photoreal or a cartoon. What changes is a thin **render layer**: how skin/materials, light, and the final look are described, plus which `NO`-constraints are legal (you must NOT write "NO 3D render" for a 3D style).

Two hard principles:

1. **Style is a PROJECT decision, not a per-asset one.** Every asset in one film must share a style or the character and the location won't read as the same world. Ask it **once**, as the very first T1 question (or infer it from the request — «мультик», «в стиле пиксар»). Then **register it on the project** and **inherit it silently** for every later asset — never re-ask. If the project already has a locked style (registry, master-style doc, prior assets), announce it and reuse it.
2. **Non-IP applies to style too.** Studio names («Pixar», «Ghibli», «Aardman») are fine in the *interview* as shorthand so the user knows what they're picking — but they go into the **prompt only as descriptive language** ("stylized 3D-animated feature look, soft subsurface skin, oversized expressive eyes"), never as a brand name.

The style bank, the picker options, and the per-style render substitutions (the **style packs**) live in [templates.md](templates.md). Default style when the user delegates: **photoreal** for live-action projects, but if the request itself implies animation, default to **3D-animated** and say so.

---

## THE RULE OF THE INTERVIEW

Ask what cannot be guessed; default what can; **announce every default so the user can veto it**.

- Batch questions with AskUserQuestion — up to 4 per round, 2–3 rounds is the normal maximum.
- Recommended option first, marked "(Recommended)". Always include a «Реши сам» option.
- Free-form questions (describe a face in your own words, exact text on clothing) go as a short numbered list in chat, not AskUserQuestion.
- If the user picks «Реши сам», answers briefly, or says «дальше сам» — stop asking. Fill the rest with scene-logical choices and list them in the final summary for veto.
- Phrase questions with visual anchors, not abstractions: not «какой характер лица?» but options like «уставший ветеран — тени под глазами, жёсткая щетина» / «свежий талант — гладкая кожа, открытый взгляд».

Priority tiers (full banks in [questions.md](questions.md)):
- **T1 — regen-critical.** Wrong answer = asset thrown away. Always ask. (Identity, era, role, recurrence, variants.)
- **T2 — visible detail.** Ask while the user is engaged; otherwise default loudly.
- **T3 — technique.** Never ask; standards below.

---

## WORKFLOW

### Stage 0 — Brief

From the request and project context (studio overview, existing asset registry, prior chat), establish:

1. Asset type: character / location / prop (or a batch of several).
2. Project it belongs to, the film's aspect ratio, **and the project's visual style** — inherit it if the project already locked one; otherwise it becomes the first T1 question (see [templates.md](templates.md) style bank).
3. Narrative role: which scenes it appears in, era/geography, emotional valence of those scenes.
4. Recurring or one-off. **One-off background characters need NO sheet** — they are described inline in the video prompt; say so and stop.
5. Variant tree: other outfits, other ages, other states (sweat, dirt, damage) needed later. Variants are planned now, generated after the base is approved.
6. Existing references: if the user already has a photo/image — skip appearance questions entirely, switch to variant/edit mode (deltas on top of the upload).

### Stage 1–2 — Interview

Open [questions.md](questions.md) and pick **only the questions relevant to this asset** — a speaking character needs a voice question, a silent one doesn't; a hero prop needs wear history, a background prop doesn't. Run T1 first (round 1), then T2 (round 2), respecting the escape hatch.

**Visual style is the first T1 question of a project** — but only once: if the project already has a locked style, skip it and announce the inherited style instead. For non-photoreal styles, one extra T2 question is worth asking: **degree of stylization** (realistic proportions vs. cartoon big-head/big-eyes) — it changes the sheet a lot.

### Stage 3 — Prompt assembly

Open [templates.md](templates.md) and assemble the prompt per type. Hard rules:

- English only, plain instruction-style language, observable wording (translate «уставший» into shadows under eyes, heavy lids, slack posture).
- **Apply the project's style pack** (templates.md → STYLE PACKS): swap the render / lighting / surface lines and the constraint delta for the chosen style. The template *structure* is identical across styles — only these slots change.
- Character sheets: 4 views on one 16:9 sheet on gray — the same two angles at two scales: two full-body panels (front / left profile) across the left two-thirds, plus two bust portraits (front / left profile) stacked one above the other in the right third. No back view. **Neutral expression, no smile, in every view** — emotion comes from the scene prompt, never from the sheet. End with a **LOCKS** paragraph restating identity-critical facts once.
- Any on-asset text (name on a shirt, number, sign) is spelled out **verbatim** in the prompt, with its rendering style (printed / hand-painted / embroidered — or, in a drawn style, painted/inked to match).
- Non-IP always: invented crests and brands, no real logos, no real club/federation/brand names — **and no studio names in the prompt** (describe the look, don't name Pixar/Ghibli/Aardman).
- Image prompts MAY use NO-constraints («NO text, NO people») — unlike Seedance video prompts, which stay positive-only. **But the constraint must match the style:** never write "NO 3D render" for a 3D style, "NO illustration" for a drawn style, etc. Use the style pack's constraint delta.
- Color via 60:30:10 (dominant / secondary / accent), tied to materials and light, not a flat list.

### Stage 4 — Deliver, register, generate

1. **Deliver:** короткое резюме принятых решений и дефолтов на русском → final English prompt in a code block → предложенный `@tag` (kebab-case).
2. **Register:** append to the project's asset manifest (`assets.md` next to the project docs, or the studio project notes): `@tag | type | role | hard locks | variants | status`. Tag names must match the names the references are uploaded under (studio assets / Higgsfield Elements) **exactly** — that's what lets video prompts auto-attach them.
3. **Generate (optional):** if the seedance-studio MCP is connected, offer to generate now — images via the studio's Seedream/banana endpoint; speaking characters additionally registered through the studio character flow so identity + voice get pinned. Estimate cost first when a cost tool exists. Otherwise hand the prompt off for manual generation.
4. **QC after generation:** face readable and lit? feet in frame in both full-body panels? no accidental text, logos, extra limbs? backdrop clean? If the base is good, iterate with **edit one-liners** (templates.md), not full regens.

---

## GEOMETRY LOCK — 3D-блокаут для точных планировок

Когда пользователь даёт **точный план** (скетч планировки), текстовые промпты промахиваются мимо расстановки, а edit-итерации не двигают крупные объекты. Рабочий пайплайн:

1. **Блокаут:** headless Blender (CLI, без GUI) — примитивы box/cylinder в реальных метрах по плану, камера ставится в точку **будущего кадра видео** (ракурс плейта = ракурс шота). Рендер EEVEE — секунды.
2. **Рестайл:** img2img (Seedream/banana) с блокаутом как **единственным** референсом + материалы/свет/стайлинг текстом. **Стилевой референс с другой геометрией НЕ добавлять** — он перетягивает композицию (объекты уезжают на его места). NO-констрейнты против прайоров обязательны (`NO steps inside the aisle`, `NO staircase above floor level`).
   **Блокаут пинует ПОЗИЦИИ, а не ФОРМЫ.** Формулировка «follow its geometry exactly, keep every object exactly where placed» на ближних/средних планах заставляет модель сохранить и силуэты примитивов — получаются стулья-коробки и стол-цилиндр с натянутой текстурой. На широких платах это незаметно (шкафы/стены и так прямоугольные), но всё, что ближе пары метров, пиши так: «the reference is a rough blockout of PLACEHOLDER primitives; keep the camera angle, the walls/openings and each object's POSITION and SIZE, but render every placeholder as its real-world counterpart with natural proportions and detail — do not copy the primitive shapes». Реальные предметы называть явно («a real round oak dining table on four legs, real dining chairs with slatted backs»).
3. **Бонус:** одна .blend-модель → камеры для всех шотов сцены → геометрически идентичная локация в каждом кадре, чего чистый text-to-image не гарантирует.

Пределы edit-правок: edit отлично делает **дельты** (убрать макияж, заменить серьги, удалить объект из зоны, поменять содержимое проёма), но **не переставляет крупные объекты** (холодильник через edit не переедет) — перестановка = свежая генерация с переработанным порядком описания или блокаут.

---

## STANDARDS (T3 — never ask)

**Style-invariant (identical in every style):**

- **Character sheet:** four views on one 16:9 sheet — two equal full-body panels (front / left profile) side by side across the left two-thirds, thin dark dividers, plus the same two angles as bust portraits (front / left profile) stacked in the right third; no back view; plain gray seamless backdrop, feet visible in both full-body panels, neutral expression (no smile) in every view, one identical person across all views.
- **Prop:** neutral mid-gray sweep, soft contact shadow, real-world scale stated (vs a hand or human height when ambiguous). Hero single view by default; 2-view sheet when front/back both matter. Aspect 4:3 or 16:9.
- **Location: 16:9 by default**, whatever the film's aspect — a wide plate carries more of the space, and a vertical shot is cropped from it at the video stage (a 9:16 plate cannot be widened back); compose so the subject survives a centre 9:16 crop. **Exception — shoot in the film's own vertical aspect when the subject is itself vertical and fills the frame:** a door, a window, a statue, a narrow passage, an extreme close-up. There a 16:9 plate wastes most of its width on wall and forces the model into a wide shot. Rule of thumb: wide space → 16:9; tall object → the film's aspect. 3/4 vantage so depth reads (two walls / receding layers), foreground–midground–background stated separately, **NO people, NO readable text**, generated EMPTY — crowds and extras are added at the video stage, and the video prompt overrides the plate. Light carries the scene's emotional temperature (warm = good memory, cold = painful one).
- **Wardrobe/materials:** material + condition + color, scene-logical (era-correct, weather-correct).
- **Character sellers:** asymmetric marks (moles, freckles, scars), worn edges, uneven hand-made details — flawless symmetry reads as a template. In a drawn/3D style these become *design* marks (a signature mole, a scar shape) rather than photo texture, but they stay.

**Style-dependent (comes from the project's style pack — never invent per asset):**

- **Render line, lighting language, surface/skin language, and the NO-constraint delta** are all taken from [templates.md](templates.md) → STYLE PACKS for the locked style. Photoreal → `pore-level skin, WB 5600K, 8K photoreal, NO 3D render`. 3D-animated → `soft SSS skin, oversized expressive eyes, stylized render, NO photo`. 2D/anime → `flat cel fills, bold lineart, NO 3D`. Etc.
- WB in Kelvin is a **photoreal-only** parameter; drawn and stop-motion styles use their own light description, not a color temperature.

---

## HANDOFF TO seedance-clean / seedance-25

The sheet is the identity; the video prompt keeps character text minimal: `@tag — age + role + current state + action-critical details. 100% matches the reference.` Never re-describe the full appearance in a video prompt — long appearance text conflicts with the image and degrades it. State in words only what the video model tends to drop: small text, numbers, logos, critical colors.

**Исключение — люди в студии BytePlus:** видеомодерация студии режет референсы с фотореал-лицами (включая сгенерированные листы и кропы с мелким лицом). Листы людей там в видео не подкладываются — идентичность несёт **полный текст-лок** (дословно повторяемый в каждом промпте), а лист остаётся полезен как источник этого описания, для Higgsfield Elements и для image-пайплайна. Локации, пропсы и животные проходят как референсы нормально. Надёжный путь к реальному лицу в видео — верифицированный актёр BytePlus.

**Style rides on the reference automatically** — the video prompt does not re-describe the render style, since the sheet already carries it. But because the whole project shares one style, a one-line global style tag at the top of the shot list keeps a mixed sequence coherent. seedance-clean and seedance-25 mirror this from the video side (its PROJECT VISUAL STYLE section): it swaps its performance/micro-realism language to the project's style and keeps photoreal vocabulary (pore-level skin, WB Kelvin, film grain) out of stylized projects.

---

## CHECKLIST (before delivering a prompt)

- Style locked and consistent with the project? Right style pack applied (render + light + surface + constraint delta)?
- Constraint delta matches the style (no "NO 3D render" on a 3D asset, no studio names in the prompt)?
- T1 answered or explicitly delegated; every silent default (incl. inherited style) listed in the summary?
- Observable wording throughout (no mood-word abstractions)?
- On-asset text verbatim + rendering style? Non-IP everywhere?
- 60:30:10 stated? WB in Kelvin (photoreal only)? Scale anchored?
- Character: 4 views (2 full-body + 2 stacked busts), feet in frame, neutral face (no smile), LOCKS paragraph?
- Location: 3/4 depth, layers, empty, no text?
- Prop: contact shadow, wear consistent with its story?
- `@tag` proposed, registry updated (with style), variant tree noted?
