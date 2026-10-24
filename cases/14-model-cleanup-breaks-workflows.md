# A model cleanup broke four ComfyUI workflows without a sound

**Pattern:** B: the implicit context is never tested absent
**Tools:** ComfyUI, model files (`.safetensors`), `grep`, a shared GPU machine
**When:** October 2026 · **Silent for:** about one hour

## What was expected
Free disk space on a GPU machine used by someone else, by deleting model files I had listed as "replaced" or "old": a text encoder variant, an image-edit model with its LoRA and its encoder. The list was approved before deletion.

## What happened
Those files were still referenced by four ComfyUI workflows (draft, final and API versions) installed on that machine two weeks earlier, with a usage note. After the cleanup, the workflows were broken.

## Why nothing flagged it
A ComfyUI workflow that points to a missing file warns nobody until someone opens it and runs it. My list said what each file was, not what used it. The implicit assumption, that nothing depended on an "old" file, was never tested.

## How I found out
By chance, one hour later, while installing a new workflow in the same folder.

## The signal I added
The files were downloaded again from the download lists kept on the machine, one encoder was switched to an int8 version, backups of the edited workflows were kept, and the repaired workflows were run through the queue. Before deleting any model now: `grep -rl <filename>` in every workflow folder of the machine, and a "used by" column in the table submitted for approval.

## Gate rule
Before deleting a file, search for everything that references it; "old" is a guess, a reference count is a fact.
