# Sub-calls with unreadable JSON were dropped, and the synthesis concluded anyway

**Pattern:** B: the implicit context is never tested absent
**Tools:** Python, OpenRouter, LLM sub-calls returning JSON, a synthesis call
**When:** September 2026 · **Silent for:** one overnight run per model

## What was expected
A document-analysis script split a long file set into sub-calls of eight pages each, sent through OpenRouter. Each sub-call returned JSON. A final synthesis call read all the parts and wrote conclusions.

## What happened
Any sub-call whose answer was not valid JSON (truncated string, `None` content) was never seen by the synthesis. With one model, 13 sub-calls out of 24 were lost: three blocks out of six missing whole groups of pages. With another model, 3 were lost. Three of the first model's four false findings ("there is no X in the file") came straight from the missing pages.

## Why nothing flagged it
The script wrote "unreadable JSON part N" in the run log and moved on. The synthesis returned complete, well-formed JSON. The block was marked done. Only a line of "limitations" hinted at the gap. The implicit assumption, that every part had answered, was never tested.

## How I found out
An evaluator model counted the pages the synthesis never cited. `grep -c "unreadable JSON"` on the logs confirmed it: 13 and 3.

## The signal I added
In the next version, any sub-call without valid JSON fails loudly and is retried. The synthesis receives the list of pages actually seen and refuses to conclude on a document it never saw.

## Gate rule
In any split-and-merge pipeline, count parts expected and parts received before merging; a mismatch fails the run, it is not a footnote.
