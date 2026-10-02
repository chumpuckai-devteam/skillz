---
name: explain-simpler
description: Use when output is confusing, dense, or needs STE.
version: 1.0.0
author: chumpuckai-devteam
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [explanation, ste, diagrams, comprehension]
    related_skills: []
---

# Explain simpler

## Overview

When an output is hard to understand, climb a ladder and stop at the first rung that can remove the confusion. Do not emit every format.

80% STE is the default writing rung: structural clarity, hedges kept, no dictionary. Strict ASD-STE100 is a separate mode. It runs only when the user names STE, ASD-STE100, or compliance.

Distilled from Andrej Karpathy's output ladder (https://x.com/karpathy/status/2105819303471976479) and from the structural habits in AminBlg/SimpleEnglish and danyuchn/asd-ste100-skill, checked against ASD-STE100 Issue 9. This file is not a copy of those skills or of the standard. It is not affiliated with ASD. Do not reproduce the STE dictionary.

## When to Use

- The user does not understand an answer, or says it is dense, confusing, or too long.
- The user asks for a simpler explanation, "80% STE", a diagram, a page, or a video explainer.
- The user names STE, ASD-STE100, or compliance.

Do not use for:

- Chat the user already understood.
- Creative or marketing copy, where voice is the point.
- Certified technical publications. No tool certifies ASD-STE100 compliance.

## Ladder

Pick the lowest rung that fits. "Still don't get it" climbs exactly one rung from what was just delivered. Do not skip to video.

| Rung | Use it when | Done when |
| --- | --- | --- |
| 1. Writing | Default. Also "simpler", "80% STE", or a dense answer. | The first sentence is the answer, and the structural rules below hold. |
| 2. Diagram | The confusion is a flow, system, sequence, or comparison. Also when writing just failed. | A diagram file is attached, and it shows the relation the sentence could not. |
| 3. Page | The user asks to click through it, or a diagram still failed and they need to explore. | One HTML file is attached and opens. |
| 4. Video | The user asks for a video or a 3Blue1Brown-style explainer. Also when text and a diagram failed and the topic is change over time. | A rendered video file is attached, or a real blocker is named. |

A structural confusion gets writing and a diagram in the same turn. That is rung 1 plus rung 2, not a skip. Do not add a page or a video in that same turn.

## Rung 1: 80% STE

This is not compliance. Do not announce the mode unless asked.

1. The first sentence is the answer or the action. Do not restate the question.
2. One claim per sentence. Split an explanation over 25 words. Split a procedure sentence over 20 words.
3. Active voice. Name who acts. Passive only if the actor is unknown.
4. If there is a condition, put it before the command, with a comma.
5. One instruction per sentence, unless two actions happen at the same time.
6. Do not drop the subject, the verb, or an article to sound short. Keep "that" when it marks a clause.
7. No semicolon. No em dash. A hyphen in a compound, a range, or a flag is fine. The em-dash ban is a clarity rule of this skill. The standard bans the semicolon, not the em dash.
8. Break a noun stack of four or more words with "of", "for", or "in".
9. Keep every fact, number, condition, and hedge. Do not turn "may" into "can", or "should" into "must".
10. At first use, define a concept term in a few words. Do not define product names.
11. Use a list for three or more steps or parallel items. Colon on the lead-in. One instruction per item. Do not ban lists to look plain.
12. For a destructive or safety step: command or condition first, then the risk.
13. No opener ("Certainly") and no closer ("I hope this helps").

Stop there. Do not rewrite spelling, ban contractions, or enforce a word list. That is strict mode.

If the confusion is a flow, system, sequence, or comparison, make the diagram in the same turn.

## Strict mode

Run this only when the user says STE, ASD-STE100, compliance, or strict STE. "Simpler" and "80% STE" stay on rung 1.

Before drafting, read `references/strict-checklist.md`. Done when that file was read in this turn and the draft follows it.

End with one sentence: this is not certified ASD-STE100 compliance, and the official dictionary was not checked unless the user supplied a local word list.

## Rung 2: Diagram

Load `architecture-diagram` for a system, service, or infra relation. Load `excalidraw` for a sequence or a hand-drawn flow.

If that skill is not installed, write one SVG or a single mermaid file that shows the same relation. Say which skill was missing. Do not describe a diagram you did not write to disk.

Attach the file in the current chat.

## Rung 3: Page

Load `claude-design` or `sketch`. Build one discardable page that separates the confusing parts. Not a product site, not a brand exercise.

If neither skill is installed, write one self-contained HTML file and say so.

Attach the file. Done when it is on disk and attached.

## Rung 4: Video

Do not start here because it is the top of the ladder.

Load `manim-video` for a 3Blue1Brown-style explainer. Narrate with the local speech tool. Never ask for, accept, or type an API key.

If `manim-video` is not installed, say so and stop. Do not invent frames or claim a video exists.

Done when a video file is attached, or the missing tool is named.

## Output

Deliver the explanation and any required file. Do not announce this skill or the rung unless the user asked which rung.

Add one line naming the next rung only if this rung is likely to miss. Do not build that next rung unless the ladder already required it.

## Common pitfalls

1. Climbing to a page or a video because a later rung is "better". Later means easier to parse, and slower. Stop at the first rung that can work.
2. Upgrading hedges in 80% mode. "May" and "should" stay. Upgrading them makes an uncertain claim sound settled.
3. Banning lists. Steps and parallel items belong in a list.
4. Claiming STE compliance, or pasting dictionary rows into the reply, this skill, or git.
5. Asking for an ElevenLabs or other API key. Use local narration, or stop.
6. Describing a diagram, page, or video that was not written to disk.
7. Running strict mode on "I don't get this".

## Verification checklist

- [ ] The rung matches the request. "Still don't get it" climbed exactly one rung.
- [ ] The first sentence answers.
- [ ] Hedges are unchanged, unless strict mode required a mapped modal and the mapping was stated.
- [ ] A structural confusion has an attached diagram, not only prose.
- [ ] Strict mode read `references/strict-checklist.md` and ends with the compliance sentence.
- [ ] No dictionary text, API key, or secret is in the reply or the files.
- [ ] Any promised file exists on disk and is attached.
