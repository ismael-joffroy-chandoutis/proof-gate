# A backup folder named "COMPLETE" was empty

**Pattern:** A: I checked that it started and stopped there
**Tools:** file copy to an external disk, macOS Photos library, `du`, `find`
**When:** September 2026 · **Silent for:** about six weeks

## What was expected
A full copy of my main data disk, including the Photos library, on a backup disk attached to the always-on server. The folder name ended in `COMPLETE` plus the date.

## What happened
Inside that folder, the Photos library weighed 0 bytes, had 0 files in `originals`, and an unreadable database. The copy had been launched in August, failed, and left an empty shell behind. A neighbouring copy was real (777 GB, 326,876 originals) but contained no video at all: zero `.mov`, zero `.mp4`.

## Why nothing flagged it
The copy failed without reporting anything. The folder existed, at the right place, with a name that said the opposite of its content. The name stood in for proof.

## How I found out
I was about to answer "yes" to the question: if I erase the photo library on the main disk, do I have a copy elsewhere? Answering on the folder name would have destroyed 372,798 items. Counting the files first showed the truth.

## The signal I added
A backup is checked with `du -sm`, a file count by type, and opening a sample. Never by its name, never by the return code of the copy. The test question: if the copy had failed completely, what would I see? Here: a well-named, well-placed, empty folder.

## Gate rule
Before any destructive step that relies on a backup, measure the backup's content (size, file count by type, one opened sample); its name and its copy log prove nothing.
