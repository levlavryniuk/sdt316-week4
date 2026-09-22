## What changed
Added three small project files: `notes.txt`, `config.ini`, and `usage.md`.

## Why
The project had no notes, no settings file, and no usage documentation. Each file is one self-contained change so the history stays readable.

## How to verify
Check out `tidy` and run `git log --stat main..tidy` — it shows exactly three commits, each touching one file. Open `notes.txt`, `config.ini`, and `usage.md` to inspect the contents.
