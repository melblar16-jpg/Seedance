---
name: gemini-omni
description: >
  Gemini Omni Flash prompt-writing system (gemini-omni-1.1-flash on the Gemini Interactions API —
  "Gemini Omni" in the Longtake picker). Use whenever the user wants a prompt for Omni: "промпт для
  Omni", "сними на Omni", "prompt for Gemini Omni", "сцена на омни" — and whenever the brief needs
  what only Omni does: EDITING an existing clip in conversation ("сделай это видео аниме", "убери
  телефон из кадра"), EXTENDING a clip up to 40 seconds, video used as a REFERENCE for a character,
  or on-screen TEXT that has to be readable. Also for ordinary text-to-video when the user has picked
  the Omni model. Trigger on "продли видео", "поменяй в этом видео", "по таймкодам на Omni",
  "текст в кадре", "оживи фото на Omni". ROUTING: for BytePlus Seedance use seedance-clean (2.0
  family) or seedance-25 (2.5); for MiniMax H3 use minimax-h3; for Veo there is no skill — its prompt
  is plain prose. The grammars are NOT interchangeable. Assets and character sheets come from
  seedance-assets.
---

# Gemini Omni Flash Prompt Writer

Source of truth: **ai.google.dev/gemini-api/docs/omni**, section "Руководство по использованию
подсказок" (Prompting guide), read 2026-08-31. Everything below follows that page; where this file
says "the guide", that is what it means. Figures marked *(measured)* come from live runs in this
studio, not from the docs.

## THE ONE THING THAT SURPRISES EVERYONE

**By default Omni does not shoot one shot. It cuts.** The guide is explicit: left alone, the model
builds a multi-shot little story out of the brief, with its own edit points. Seedance does the
opposite — it holds one take unless told otherwise.

So a single-take brief must SAY so, verbatim from the guide:

- `In a single continuous scene`
- `In one continuous shot`
- `No cuts`

Forgetting this is the most common reason an Omni take "came out wrong": the prompt was fine, the
model just told a three-shot story with it.

And the flip side is a feature — for a montage you simply ask, and Omni cuts on its own beat:
`Every 2 seconds cut to a new frame.`

## WHAT THE STUDIO ADDS, SO DO NOT WRITE IT TWICE

Longtake's server builds part of the prompt for you (`omniInput` in server.js). Whatever the user
attached in the slate arrives already declared:

- a first-frame slot → `The opening frame is <FIRST_FRAME>.`
- a last-frame slot → `The final frame is <LAST_FRAME>.`
- every character and @mentioned image → `<IMAGE_REF_0> shows NAME.` (numbering from 0, in order)
- the duration slider → `Make the clip about N seconds long.`

**Write the scene, not the plumbing.** Do not open your prompt with `<FIRST_FRAME>` or repeat the
length — it will be said twice. Refer to a reference in the body by the tag the studio will assign
(`<IMAGE_REF_0>`, `<IMAGE_REF_1>`, …) when you need the model to use it in a particular way.

Outside the studio — writing a raw API call — you place the tags yourself; see TAGS below.

## MODES

Five values for `video_config.task`: `text_to_video`, `image_to_video`, `reference_to_video`,
`edit`, `extend`. The guide's own advice: **rely on the prompt first**, set `task` only when the
prompt fails to convey the mode, because the field "adds constraints". The studio picks it from what
is attached.

## TAGS

Two levels. Simple tags are recommended and enough almost always:

| Tag | Meaning |
|---|---|
| `<FIRST_FRAME>` | this image opens the video |
| `<LAST_FRAME>` | this image ends it — **only valid together with `<FIRST_FRAME>`** |
| `<IMAGE_REF_N>` | reference image, numbered **from 0** |
| `<VIDEO_REF_N>` | reference video for a character or object, numbered **from 0** |

Tags go inline, where the thing is mentioned:

```text
in the style of <IMAGE_REF_0> a woman <IMAGE_REF_1> is walking
the person in <VIDEO_REF_0> is playing the violin
```

That first line is the pattern worth remembering: one reference for STYLE, another for the SUBJECT,
in one sentence.

For many media with mixed roles, declare them **at the very start** and explain at the end:

```text
[# Sources <FIRST_FRAME>@Image1] [# References <IMAGE_REF_0>@Image2] a woman <IMAGE_REF_0> is
walking. Use Image1 as the starting frame. Use Image2 as a reference for the video generation.
```

Declaration forms from the guide, verbatim in shape:

- `[# Sources <FIRST_FRAME>@Image1]` — starting frame
- `[# Sources <FIRST_FRAME>@Image1 <LAST_FRAME>@Image2]` — first and last
- `[# Sources <FIRST_FRAME>@Image1 <LAST_FRAME>@Image1]` — same image both ends = **a loop**
- `[# Sources <VIDEO_0>@Video1]` — this video is the one being EDITED
- `[# Sources <PREVIOUS_VIDEO>@Video1]` — the previous turn's video, for extending
- `[# References <IMAGE_REF_0>@Image1 <VIDEO_REF_0>@Video1]` — image and video as references

Closing sentences the guide gives for each case:

- `Use this image as the starting frame.`
- `Use this image as the first frame and the last frame.` (loop)
- `Use the given image(s) as references for video generation. The images should not be used as
  literal initial frames.`
- `Use the given video(s) as references. Do not use them as a source for video editing.`

## TIMING

No special syntax is required — plain language works:

```text
After 3 seconds a woman enters the scene.
At the 5 second mark the chorus starts in the background.
Every 2 seconds cut to a new frame.
In fast motion, every half second (12 frames at 24fps) the location changes.
```

Timecode syntax is also accepted, and is the cleanest way to storyboard:

```text
[0-3s] A person is walking
[3-6s] They stop and turn around
[6-10s] They start running
```

Timecodes combine with references — this is the multi-subject montage pattern from the guide:

```text
[0-3s] A studio fashion sequence. Starting with woman <IMAGE_REF_0>, she is holding <IMAGE_REF_1>
[3-6s] Then we see the man <IMAGE_REF_2> holding <IMAGE_REF_3>
[6-10s] And finally another woman <IMAGE_REF_4> who is holding <IMAGE_REF_5> while walking.
```

**When extending, `0s` means the start of the EXTENSION, not of the original.** Extending a
10-second clip with "After 2s cut to a new scene" cuts at second 12 of the finished video.

## SOUND

Omni scores every take itself. If the default is not what you want, describe it — the guide is
blunt that this matters most for music:

```text
Sound design: gentle breeze, distant bird chirps. No dialogue.
Include calm background music.
The clip has a high energy techno beat.
The audio is a low, tinny radio broadcast playing a song in the background.
```

There is **no audio input of any kind** — a voice sample cannot be attached, and audio inside a
reference video is ignored. Voice consistency across scenes is a Seedance-only capability.

## TEXT ON SCREEN

Omni renders text legibly, and the guide recommends spelling out exactly what it should say —
including background signage:

```text
One word on screen at a time: "did, you, know, that, Omni, can, do, awesome, text?" Each word
appears for 1 second with a different animation style. No dialogue.
A sign on the street reads "This is Omni's AI generation", a shop window says "Everything you need
in AI", and a car nearby has the licence plate "OMNI1.1".
```

## NEGATIVES

There is no negative-prompt field — Omni does not support one. Put negations in the prose, short:

```text
No dialogue. No embellishments. No extra sound effects.
```

## EDITING

The rule is the opposite of everything else here: **keep an edit prompt SHORT**. The guide says an
over-detailed edit causes unintended changes, and gives paired examples:

- ✗ `In the video of the man sitting on the sofa, please add a small black cat that runs from the
  right side of the screen, jumps onto his lap, and then he starts to stroke its head while looking down.`
- ✓ `Add a cat that jumps onto his lap, he begins to pet it. Keep everything else the same.`
- ✗ `Please remove the cell phone that the person is holding in their hand and fill in the background
  so it looks like they are just holding their hand empty.`
- ✓ `Make the phone invisible. Keep everything else the same.`

`Keep everything else the same.` is the load-bearing sentence — add it whenever one element changes.

Other edits that work as one line: `Make this video anime`, `Put a fancy hat on this person`,
`Change the lighting to be more dramatic`, `Change the text on the sign to "Omni Flash"`.

## EXTENDING

`Extend this video` or `The scene continues` is enough to start. Omni adds **3–10 seconds** per
turn, up to **40 seconds total**, using the last 10 seconds of the source as context — and it will
re-render a few of the source's final frames to make the seam invisible.

What the guide says to include:

- whether the scene continues or cuts to a new one: `Show the same characters in the next scene`
- what the audio does, especially if it changes: `The music continues into the chorus`
- new people or things, by reference: `The person shown in the reference image enters the scene`,
  `The dog in the reference video <VIDEO_REF_0> jumps onto the sofa`

## META-PROMPTING

Omni responds to instructions about craft, not just content. Straight from the guide:

```text
Pay attention to small details, facial expressions and moment to create a very rich, detailed but
completely natural scene.
Be extremely detailed in your description of the characters and setting. Apply costume design
principles to the characters.
Include lots of appropriate detail in the background to make the scene look real and natural.
```

Worth adding to any brief where the result must not look generic.

## LIMITS THAT CHANGE HOW YOU WRITE

- **Reference video: at most 3 clips, 3 seconds each**, and their audio is ignored. Reasoning across
  several videos is not supported and degrades the result.
- **Uploaded video for edit/extend: 10 seconds max** (multi-turn extension of the model's own video
  is exempt).
- **A speaking uploaded video cannot be extended with more dialogue.** Silent characters are fine;
  dialogue works when extending Omni's own earlier video via `previous_interaction_id`.
- **Voice editing is not supported at all.**
- Extension only appends to the END — never the head, never the middle.
- Recognisable people in input images are not supported; in the EEA, Switzerland and the UK editing
  and extending UPLOADED video is unavailable entirely.
- No system instructions, no temperature, no top_p, no stop sequences.
- **English is the only fully supported language.** Others may work, untested. Write prompts in
  English even when the user writes in Russian.
- Aspect ratio is 16:9 or 9:16 only.

## LENGTH AND PRICE — WHAT TO TELL THE USER

*(measured, not from the docs)*

- The API has **no duration parameter**. Length is asked for in words and the model obeys well:
  "about 4 seconds" produced a 4.01-second clip.
- **Price barely moves with length.** Three runs at 360p: a 10-second clip billed 19 310 video
  tokens, another 10-second clip 57 920, and a 4-second clip 57 920 again. At $17.50 per million
  that is roughly $0.34–$1.02 for the same request shape.
- Consequence for writing: **do not chop a scene into short takes to save money — it saves nothing.**
  Write the longest clip the idea justifies. This is the opposite of the Seedance economy, where
  seconds are the bill.
- 1080p and 4K are **upscaled**, not rendered natively (the docs say so outright). For anything that
  will be graded or upscaled later, shoot 720p and spend on the upscale instead.

## SHAPE OF A GOOD OMNI PROMPT

No rigid template — the guide favours natural language. A dependable order:

1. **Shot discipline** — `In a single continuous shot` (or the montage instruction instead).
2. **Subject and action**, with reference tags inline where they belong.
3. **Camera** — handheld, drone, slow push-in, static.
4. **Setting, light, mood** — the more specific, the less generic the result.
5. **Timed events**, if any — plain language or `[0-3s]` blocks.
6. **Sound design**, explicitly, including music.
7. **Negatives** — `No dialogue.` and friends.
8. **Meta-instruction**, when the result must be rich rather than serviceable.

Worked example from the guide, showing 1/3/4/6/7 in one paragraph:

```text
Continuous, unbroken handheld shot of a fluffy tabby cat sitting on a sunny windowsill, looking out
into a leafy garden. The cat's tail twitches slowly, and its ears rotate slightly toward ambient
noises. Sunbeams illuminate dust motes in the air. Sound design: Gentle breeze, distant bird chirps.
No dialogue.
```

## CHECKLIST BEFORE HANDING THE PROMPT OVER

- [ ] Single take asked for explicitly — or a montage asked for on purpose?
- [ ] Written in English?
- [ ] Sound described, or silence stated?
- [ ] Reference tags numbered from 0 and matching what the studio will attach?
- [ ] No duplicated frame/length lines the studio adds itself?
- [ ] Edit prompt kept short, with `Keep everything else the same.`?
- [ ] Extension: audio and continuity mentioned, `0s` understood as the seam?
- [ ] Nothing asked for that the model cannot do — voice reference, negative field, 4:3, dialogue
      added to an uploaded speaking clip?
