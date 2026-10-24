# Gemini reads video at one frame per second and measured cuts it could not see

**Pattern:** B: the implicit context is never tested absent
**Tools:** Gemini API (video input), `ffmpeg`
**When:** September 2026 · **Silent for:** caught before the numbers were used

## What was expected
An editing analysis of a film corpus, sent to Gemini: average shot length, shortest shot, J-cuts and L-cuts with timecodes.

## What happened
The Gemini API samples video at 1 frame per second. Any shot shorter than a second is invisible to it. Two close cuts merge into one. My J-cuts, at 0.45 seconds by my own mixing rule, do not exist in what the model receives. On a corpus whose shot-length distribution peaks under 2 seconds, the model would have returned consistent, confident, wrong numbers.

## Why nothing flagged it
The model "sees" the file, so I assumed it saw every frame. Nothing in its answer mentions the sampling rate. The implicit context (the temporal resolution of the input) was never checked.

## How I found out
One question, asked before the run: does the model receive the video at 24 frames per second? It does not.

## The signal I added
Measurement and interpretation are separated. `ffmpeg` measures: `select='gt(scene,N)',showinfo` returns cuts to the frame. My eye calibrates the threshold on a contact sheet. The model interprets afterwards, on correct numbers. Before any media analysis given to a model, I ask: at what rate does it receive the file, and does that rate allow the measurement I am asking for?

## Gate rule
Never accept a measurement from a model without knowing the resolution of what it actually received; measure with a deterministic tool, interpret with the model.
