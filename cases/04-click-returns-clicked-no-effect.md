# A browser click returned "clicked" and nothing changed

**Pattern:** A: I checked that it started and stopped there
**Tools:** an agent-driven embedded browser (JavaScript `eval`, `click`, page snapshots), a web document viewer
**When:** September 2026 · **Silent for:** one background loop and three attempts

## What was expected
An agent drove a web document viewer to click "Next document" and read the following file in a set of three.

## What happened
The viewer stayed on "Document 1/3". A background loop failed, then three more attempts failed the same way. Instead of moving on, the viewer opened a modal: "You must read the entire document". Jumping to the last page from the page list did not count as reading either, and that was not announced anywhere.

## Why nothing flagged it
The JavaScript click returned "clicked". The browser tool's click returned OK. The modal appeared only in the full page snapshot, not in the "interactive elements" snapshot the agent was reading.

## How I found out
I re-read the "Document N/3" label after each click. It had not changed.

## The signal I added
The working sequence scrolls the document container to the bottom by `eval` (`scrollTop = scrollHeight`, then a dispatched `scroll` event), then clicks. More generally: after any browser action meant to change state, re-read the state (label, URL, visible text) and compare it with the state before. An unchanged state is a failure, whatever the click returned.

## Gate rule
A UI action is done when the state it was meant to change has changed; the return value of the click is not evidence.
