# Prompt templates

Skeletons + proven verbatim examples (from the Higgsfield "Santiago" breakdown). Fill slots from the interview; drop irrelevant slots; keep the block order. English only.

**The skeletons below are written in photoreal by default. For any other style, keep the whole structure and swap only three lines — the surface/skin line, the lighting line, the render/style line — plus the closing constraint, using the STYLE PACKS table. Everything else (panels, gray backdrop, feet, LOCKS, layers, scale) is identical.**

---

## STYLE PACKS — the render layer, one per project

Pick ONCE per project (see questions.md). Each pack is four drop-in substitutions:
- **SURFACE** → replaces the skin/material wording (photoreal "pore-level skin…").
- **LIGHT** → replaces the lighting line (photoreal "neutral soft wrap-around, WB 5600K…").
- **RENDER** → replaces the final style/optics line (photoreal "8K photorealistic, clean realistic photographic image…").
- **CONSTRAINT** → the tail `NO …` clause. **Must not contradict the style.**

Backdrop stays neutral gray, layout/scale/LOCKS stay identical, no studio brand names in any pack.

### 1. Photoreal / live-action  *(default for live drama)*
- SURFACE: `pore-level skin texture, real fabric weave, real-world wear and contact`
- LIGHT: `neutral soft even wrap-around light, near-shadowless, WB 5600K, no color cast, subtle eye catchlights`
- RENDER: `8K photorealistic, clean realistic photographic image, real materials and real weight, sharp focus`
- CONSTRAINT: `NO 3D render, NO game-engine look, NO illustration, no text, no extra people`

### 2. 3D-animated feature (CGI)  *(default for family/fable; clears Seedance face moderation)*
- SURFACE: `smooth stylized skin with soft subsurface scattering, slightly oversized expressive eyes, hair in clean grouped strands, simplified pores`
- LIGHT: `soft three-point studio light, gentle ambient occlusion, warm key + cool rim, clean even exposure`
- RENDER: `polished 3D-animated feature-film render, stylized-realistic character design, rounded appealing forms, cinematic but clearly non-photoreal`
- CONSTRAINT: `NO photo, NO live-action, NO real human skin texture; stylized CGI only; no text, no extra characters`

### 3. 2D cartoon (cel / flat)
- SURFACE: `flat cel-shaded fills, bold clean outlines, one or two flat shadow tones, no pores; hair as flat shapes with a few highlight streaks`
- LIGHT: `simple two-tone cel shading, one flat shadow shape, no realistic light falloff`
- RENDER: `flat 2D cartoon illustration, clean vector-like lineart, limited flat color palette, minimal gradients`
- CONSTRAINT: `NO 3D, NO photoreal, NO realistic shading; flat cartoon only; no text, no extra characters`

### 4. Anime
- SURFACE: `smooth cel skin, large detailed expressive eyes with catchlights, layered hair strands with soft sheen, crisp lineart`
- LIGHT: `anime cel lighting, one shadow tone plus a bright highlight, soft rim light`
- RENDER: `clean anime illustration, sharp lineart, cel-shading, painterly background style`
- CONSTRAINT: `NO 3D, NO photoreal; anime illustration only; no text, no extra characters`

### 5. Watercolor / storybook
- SURFACE: `soft washy skin with color bleeds, loose brush linework, visible paper grain`
- LIGHT: `soft ambient illustration light, no hard shadows`
- RENDER: `hand-painted watercolor and gouache storybook illustration, gentle edges, textured paper`
- CONSTRAINT: `NO 3D, NO photo, NO sharp digital lines; painted illustration only; no text, no extra characters`

### 6. Comic / graphic novel
- SURFACE: `inked skin with hatched shading, spot blacks, halftone dots`
- LIGHT: `dramatic ink chiaroscuro, hatched shadows, hard highlights`
- RENDER: `bold inked comic / graphic-novel illustration, strong black linework, cross-hatching, high contrast`
- CONSTRAINT: `NO 3D, NO photoreal; inked comic art only; no text, no extra characters`

### 7. Stop-motion / puppet
- SURFACE: `handcrafted felt, sculpted clay and fabric surfaces, visible stitched seams and tiny fingerprints, real fibers`
- LIGHT: `soft tabletop miniature studio light, gentle real shadows, shallow macro depth of field`
- RENDER: `physical stop-motion puppet, tangible handmade materials, macro tabletop realism, clearly a crafted figure not a drawing`
- CONSTRAINT: `NO smooth CGI, NO 2D drawing; real handcrafted puppet only; no text, no extra characters`

> Slot map: in every skeleton below, the "Lighting:" line ← **LIGHT**, the skin/material clause ← **SURFACE**, the "Optics/STYLE" line ← **RENDER**, and the final `No …` line ← **CONSTRAINT**. Degree of stylization (from questions.md) tunes proportions inside SURFACE — bigger head/eyes for "выраженная/максимальная".

---

## CHARACTER SHEET (4 views, 16:9)

```
CHARACTER SHEET — {NAME}, {AGE} ({ROLE}) (4 VIEWS, GRAY BACKDROP, 16:9)
A single 16:9 character sheet of one {man/woman/boy/girl} on a plain neutral gray studio
backdrop, laid out as four views:
Across the left two-thirds of the frame, two equal full-body panels side by side with a thin
dark divider, each framing the figure from the top of the head to the feet, shoes fully in
frame:
Panel 1 — full-body front view, standing straight, arms relaxed at his/her sides, facing camera.
Panel 2 — full-body left profile view, same standing pose, in strict side profile facing left.
In the right third of the frame, the same two angles as bust portraits (head and shoulders),
stacked one above the other:
Top — bust portrait, front view, looking straight into the lens.
Bottom — bust portrait in strict left profile.
Neutral relaxed expression in every view: calm direct gaze, lips closed, no smile — a clean
identity reference; emotion comes from the scene prompt, never from the sheet.
{NAME}: {age} y.o. {region}, {build}, ~{height} m. {face shape, cheekbones, chin}; {eyes: color,
shape, default gaze}; {brows}; {nose}; {lips}; {skin tone + asymmetric marks: freckles/moles/scars}.
{Hair: color, length, texture, styling}. {Facial hair or "no facial hair"}. {Posture}.
Outfit: {top — cut, material, condition, colors}; {bottom}; {footwear}; {accessories}.
{On-garment text verbatim + rendering: e.g. hand-painted "NAME" in wobbly capitals across the
upper back, paint smudged and uneven, not printed}.
Layout and consistency: identical lighting and identical gray backdrop in every view; same
person, same wardrobe, same hair everywhere.
Lighting: {LIGHT — style pack}.
Optics/Style: {RENDER — style pack}; full head-to-toe bodies in both full-body panels,
fine facial detail in both busts.
LOCKS: exactly one identical person shown in four views (full-body front and full-body left
profile, both head-to-toe, plus two stacked bust portraits: front and left profile); feet and
shoes visible in both full-body panels; neutral expression, no smile, in every view; plain gray
backdrop; {2–4 identity-critical facts: outfit, hair, marks}.
{CONSTRAINT — style pack}, no cropping of the body.
```

The `{skin tone + asymmetric marks}` clause in the body description is where **SURFACE** lands (pore-level for photoreal, soft SSS for 3D, flat cel for 2D, etc.).

> ⚠️ Образцы ниже записаны в СТАРОЙ 3-панельной раскладке (со спиной) — бери из них плотность и язык описания персонажа, а раскладку панелей, нейтральное лицо и LOCKS — из актуального скелета выше (4 вида: анфас и профиль в рост + те же два ракурса бюстами, 16:9).

Проверенный образец (Santiago, сокращать нельзя было — работает как есть):

```
Character sheet of one man, three panels side by side, plain gray studio backdrop, thin dark dividers. Left: full-body front, head to toe, shoes in frame, standing straight, arms relaxed, facing camera. Center: full-body back, same pose, facing away. Right: close-up of face and shoulders, calm gaze. Santiago: 25 y.o. Spanish, lean athletic, ~1.82 m. Long oval face, defined cheekbones, straight nose, full brows, warm hazel-brown eyes, calm tired gaze with faint shadows beneath. Olive skin. Short dark brown curly hair — cropped sides, tight tousled curls on top. Uneven dark stubble, lived-in look. Relaxed posture. Outfit: loose open-collar short-sleeve camp shirt, glossy marbled swirl print (cobalt blue, burnt orange, cream); relaxed olive pleated trousers pooling at ankle; low retro suede sneakers in blue/orange/cream. Lighting: neutral soft even wrap-around, near-shadowless, 5600K, no color cast, readable skin texture, eye catchlights. LOCKS: one identical person in all three views — same identity, outfit, hair, proportions; feet visible in both full-body panels; identical backdrop and light; sharp realistic photo.
```

**Same sheet, 3D-animated** — identical structure/identity, only SURFACE+LIGHT+RENDER+CONSTRAINT swapped:

```
Character sheet of one man, three panels side by side, plain neutral gray studio backdrop, thin dark dividers. Left: full-body front, head to toe, shoes in frame, standing straight, arms relaxed, facing camera. Center: full-body back, same pose, facing away. Right: close-up of face and shoulders, calm gaze. Santiago: stylized 25 y.o. Spanish man, lean athletic, ~1.82 m, appealing 3D-animated character design. Smooth stylized skin with soft subsurface scattering, slightly oversized warm hazel eyes with a calm tired look, clean grouped dark-brown curls cropped at the sides, light stubble suggested. Outfit: loose camp shirt with a glossy cobalt/burnt-orange/cream marbled print, olive pleated trousers, retro sneakers. Lighting: soft three-point studio light, gentle ambient occlusion, warm key with a cool rim, clean even exposure. Polished 3D-animated feature-film render, rounded appealing forms, cinematic but clearly non-photoreal. LOCKS: one identical stylized person in all three views — same identity, outfit, hair; feet visible in both full-body panels; identical gray backdrop. NO photo, NO live-action, NO real skin texture; no text, no extra characters.
```

**Same sheet, 2D cartoon** — swap again:

```
Character sheet of one man, three panels side by side, plain neutral gray backdrop, thin dark dividers, same three poses (full-body front, full-body back, close-up). Santiago: lean athletic 25 y.o. Spanish man drawn as a flat 2D cartoon — bold clean outlines, flat color fills, oval face, warm hazel eyes, dark-brown curly hair as flat shapes with a couple of highlight streaks, hint of stubble. Outfit: camp shirt with a simplified cobalt/orange/cream swirl print, olive trousers, retro sneakers, all as flat color blocks. Simple two-tone cel shading, one flat shadow tone, no realistic falloff. Flat 2D cartoon illustration, clean vector-like lineart, limited flat palette. LOCKS: one identical character in all three views — same identity, outfit, hair; feet visible in both full-body panels; identical gray backdrop. NO 3D, NO photoreal, NO realistic shading; flat cartoon only; no text, no extra characters.
```

---

## VARIANTS (edits on top of an approved base)

Короткие правки одним предложением, с базовым ассетом как референсом (Seedream edit / GPT Image). **Пределы edit:** отлично делает дельты (цвет, макияж, серьги, состояние, содержимое проёма, удаление объекта), но НЕ переставляет крупные объекты (мебель/техника через edit не переезжают) — перестановка = свежая генерация с переработанным описанием или 3D-блокаут (GEOMETRY LOCK в SKILL.md):

- Смена номера/лого: `Change the logo, and swap the number 7 to 23`
- Персонаж в форме (2 референса — персонаж + форма): `Create a character in this uniform. Make him clean-shaven, with neat hair, looking fresh.`
- Смена наряда у ребёнка (2 референса): `Keep the kid, change the uniform.`
- Состояние: `Make him lightly sweat-damp: faintly glistening skin, hair damp at the roots, a light sheen at the temples — tired, NOT drenched.`

**Другой возраст — не правка, а отдельный полный sheet:** приложи взрослый sheet как референс и напиши детскую/старшую версию по шаблону выше (детские пропорции, свой рост, своя одежда), сохранив узнаваемые якоря (цвет глаз, кудри, веснушки).

---

## PROP — hero view (default)

```
SUBJECT — {what it is}, {real scale: regulation size ~N cm / fits in a palm / as tall as a
person}, photoreal. Single object, single clean hero presentation.
{Materials by part}; {construction — how it's put together, especially for handmade things:
bound and lashed with wire and rope, NOT welded, visibly crooked and fragile};
{surface: gloss/matte, worn edges}; {wear consistent with its story: grass stains, cracked
seams, peeling panels, slightly under-inflated}.
{On-object text verbatim + rendering, or "no text"}.
VIEW — one object, 45° three-quarter front, fully in frame, centered.
BACKGROUND — seamless neutral mid-gray studio sweep, no gradient banding, no props, no text.
LIGHTING — {LIGHT — style pack}, soft contact shadow under the object. (Photoreal default:
flat even reference lighting, broad soft key at 45°, equal fill opposite, subtle top rim, WB 5600K.)
COLOR (60:30:10, locked at asset stage) — 60% {dominant} / 30% {secondary} / 10% {accent}.
STYLE — {RENDER — style pack}, correct contact shadow. (Photoreal default: 8K photoreal
product/reference photography, real materials, real weight.)
CONSTRAINTS — {CONSTRAINT — style pack}; neutral gray bg only; one object, single view;
scale = {anchor}, NOT oversized; full object sharp focus. {4:3 / 16:9} sheet.
```

The materials clause up top is where **SURFACE** lands (real leather vs. sculpted clay vs. flat cel fill).

Для двухракурсного sheet (перед/зад важны оба — одежда, форма): LEFT side of frame — BACK view, RIGHT side — FRONT view, identical item in both views; одежда — как ghost-mannequin: `displayed as invisible-mannequin / ghost-mannequin product shots... No person, no face, no body, no skin visible — garments hold a natural worn silhouette with soft realistic fabric drape`.

Проверенный образец (изношенный мяч):

```
old, worn black-and-white panel football (regulation size 5), centered on a plain neutral gray studio backdrop, no people. Heavily used and aged: yellowed grimy panels, scuffed faded pentagons, grass stains and dried mud, cracked seams with frayed and loosening stitching, a couple of panels peeling, slightly under-inflated — clearly kicked around for years. Aged synthetic leather, dull worn surface with no shine. Soft directional studio light from the front, gentle highlights revealing every scratch and stain, soft contact shadow beneath the ball, sharp realistic focus across the whole ball.
```

---

## LOCATION plate

```
Horizontal 16:9 cinematic location plate (16:9 by DEFAULT, even for a vertical film — the 9:16
frame is cropped from it later; keep the key subject inside the centre 9:16 area. EXCEPTION:
when the subject is itself vertical and fills the frame — a door, a window, a statue, a narrow
passage, an extreme close-up — write the plate in the film's own vertical aspect instead).
{Mood adjective + era + function}: {location одним предложением}, shot from a 3/4 angle so
{two walls of the room are visible / the space recedes in layers} and the space reads with
depth (not a flat head-on view).
{Anchor light source: a floor-to-ceiling glass wall / four floodlight rigs / low golden-hour
sun} {direction + what it does to the space}.
Foreground: {objects + geometry: two armchairs facing each other ~1.5m apart, angled
three-quarter to camera}. Midground: {…}. Background: {…, softly out of focus}.
{Materials and textures}. {Signs of life/wear: hanging laundry, graffiti, worn benches}.
{Light mood = emotional temperature: warm golden / cold floodlight white}, {shadows},
{atmosphere: haze N%, dust}, {DOF}. {Photoreal only: fine 35mm film grain, WB {N}K.}
Camera: ~{FOV}° FOV, {vantage height}, locked-off.
Style: {RENDER — style pack}. 60:30:10 — {dominant} / {secondary} / {accent}.
NO people, NO readable text{, NO daylight / NO crowd — per scene}; {CONSTRAINT — style pack}.
```

A location has no skin, so **SURFACE** maps to how materials read (photoreal concrete/rust vs. painted-flat shapes vs. felt/clay textures); **LIGHT** still recolors the mood line.

Проверенные образцы:

Камерный интерьер (тёплая сцена):
```
Warm mid-century-modern psychologist's office, shot from a 3/4 angle so two walls of the room are visible and the space reads with depth (not a flat head-on view). A floor-to-ceiling glass wall opens onto a green lawn and soft-focus trees, filling the room with diffused daylight. Foreground center: two matching tan-leather armchairs facing each other ~1.5m apart, angled three-quarter to the camera, a small round wooden table with a ceramic vase between them. Behind: a low wooden sideboard with books, a brass lamp and leafy plants, and warm wood paneling with built-in shelves softly out of focus, a muted earth-tone rug on parquet. Soft golden daylight, gentle warm shadow, shallow depth of field, soft bokeh, fine 35mm film grain. No people, no text.
```

Экстерьер-эпоха (тёплое воспоминание):
```
Photoreal, 8K, cinematic. An empty makeshift street football pitch in a working-class Spanish housing estate: a worn cracked concrete/dirt ground with faded markings, a rusty netless metal goal against a low painted wall, weathered apartment blocks with balconies, hanging laundry and graffiti on all sides, an old cast-iron lamp post, a couple of worn benches, a bike leaning against a wall. Warm golden-hour light, low sun, soft haze, long shadows, faded sun-bleached palette. Empty, no people. No text other than incidental graffiti.
```

**Правило толпы:** массовые локации (стадион, площадь, рынок) генерятся ПУСТЫМИ («deserted — empty stands, no people»). Толпу дорисует видеомодель; в видеопромпте локация помечается `LOCATION REFERENCE ONLY, NOT a keyframe` + явный override: `if the reference shows the stands empty, IGNORE that and fully populate`.

---

## Реестр ассетов (assets.md проекта)

Первой строкой — **проектный стиль** (закрепляется один раз, наследуется всеми ассетами):

```
STYLE: 3D-animated feature (CGI) · умеренная стилизация   ← locked once, all assets inherit

| @tag | тип | роль | hard locks | вариации | статус |
|------|-----|------|-----------|----------|--------|
| @santiago | персонаж | герой, 25 | кудри, щетина, hazel глаза, camp shirt | @santiago-kit, @kid-santiago | ✅ |
| @office | локация | терапия, тёплая | 3/4, стеклянная стена, 2 кресла ~1.5м | — | ✅ |
```

Имена @tag = имена файлов-референсов в студии/Elements, один в один. Если у проекта уже есть `STYLE:` — не переспрашивай стиль, бери отсюда.
