---
name: video-sort
description: Sort and organize unedited Layer Up phone videos from ~/Downloads into topic folders under "Layer Up Catalog & Marketing/Drafts/", with a per-folder CapCut AI Clipper prompt. Triggers on "sort my videos", "organize the videos in Downloads", "process new phone clips", "sort the downloads", "cluster the videos into topics", or any request to dedupe phone clips and cluster them into CapCut-ready topic folders. Pipeline includes SHA256 dedupe, ffprobe metadata, ffmpeg keyframe contact sheets for AI topic identification, cp -p preserving timestamps, size-verify, and per-folder CapCut prompt generation with Layer Up brand defaults (9:16 vertical, auto-music-sync ON, auto-captions ON, voice-highlight OFF).
---

# Layer Up Video Sort + CapCut Prompt Workflow

Sort a fresh batch of unedited phone clips out of `~/Downloads` into Layer Up topic folders, then write a per-folder CapCut AI Clipper prompt for each one.

## When to use

Brandon drops a batch of phone-camera `.mp4` files (or similar) into `~/Downloads` and wants them organized into the `Drafts/` workflow for CapCut editing. Phrases that trigger this skill: "sort my videos", "organize the new clips", "process the downloads", "cluster these into topics", "sort and prompt the videos."

## Inputs

- **Source:** `~/Downloads/` (mount with `request_cowork_directory ~/Downloads` if not already mounted).
- **Destination:** `~/Documents/Claude/Projects/Layer Up Catalog & Marketing/Drafts/` (mount the project folder if not already mounted).
- **File types:** `.mp4` by default. Also handle `.mov`, `.m4v` if present.

## Hard rules

- **Always `cp -p`, never `mv` or `rm`.** Cowork's mount of `~/Downloads` doesn't reliably allow file removal. Originals stay; user clears manually after spot-check.
- **MODE: EXECUTE gate.** Do not run any `cp` ops until Brandon has reviewed the sort plan and replied with `MODE: EXECUTE`. This is bulk, irreversible-feeling work — gating is required per Brandon's global instructions.
- **Bash 45s timeout.** Files >500 MB need their own bash call. Always size-verify after any copy.
- **Off-by-one on resume.** If a batch fails partway, re-derive the full source→dest mapping from the source list. Do not trust a partial run.
- **CapCut prompt cap.** Target 400 chars, hard cap 500. AI Clipper has no published limit; 500 is the consistent soft cap across CapCut prompt-style fields.

## Pipeline

### 1. Mount and inventory

- Confirm `~/Downloads/` and `~/Documents/Claude/Projects/Layer Up Catalog & Marketing/` are mounted. Request mount if not.
- List all candidate video files in Downloads (`*.mp4`, `*.mov`, `*.m4v`).
- Report count, total size.

### 2. Dedupe by SHA256

- For any pair like `name.ext` and `name-2.ext`, compare SHA256 hashes.
- Flag byte-identical duplicates; pick the canonical one (no `-2` suffix) to keep.
- Report: total files, unique files, duplicate count, list of duplicate filenames.

### 3. Extract metadata

For each unique file, capture:
- Duration (`ffprobe -v error -show_entries format=duration -of csv=p=0`)
- Dimensions (`ffprobe -v error -select_streams v:0 -show_entries stream=width,height -of csv=p=0`)
- File size (`stat`)
- Capture timestamp (parse from filename if present, else `stat` mtime)

### 4. Generate keyframes for AI topic ID

For each unique file:
```
ffmpeg -ss <duration/2> -i <file> -frames:v 1 -vf scale=480:-2 <tmpdir>/<basename>.jpg
```
- Mid-video keyframe at 480px wide, aspect preserved.
- Save to a temp dir under outputs/.

### 5. Build contact sheets per date group

Group keyframes by capture date (`YYYY-MM-DD`). For each date:
```
montage <jpgs> -tile 4x4 -geometry 480x+4+4 -label "%f" <tmpdir>/contact_<date>.jpg
```
- Use 4x4 tile for ≤16 clips, 5x2 for ≤10, etc.
- Read each contact sheet to identify topic clusters.

### 6. Cluster into topics

Apply the **Layer Up taxonomy**. Same-day clips usually = one shoot session = one topic.

Layer Up topic classes (in order of recurrence from the proof-of-concept run):

| Class | Folder slug example | What it looks like |
|---|---|---|
| Unboxing | `<date>_<product>-unboxing` | Box opening, hardware reveal, first-look reactions |
| Product showcase | `<date>_<product>-product` | Squish, demo, articulation, finished print |
| Setup / install | `<date>_<thing>-install` | Panel install, config, first-print setup |
| BTS / workflow | `<date>_<thing>-station` or `<date>_<workflow>-bts` | Inventory, weighing, packing, prep |
| Workshop / studio tour | `<date>_workshop-tour` | Cozy shop pan, ambient walkthrough |
| Educational tip | `<date>_<topic>-setup` or `<date>_<topic>-tip` | Drying, storage, technique demo |
| Tutorial | `<date>_<topic>-tutorial` | Deeper how-to with steps |
| Event | `<date>_<event>-event` | Markets, conventions, in-person sales |
| Misc | `<date>_misc` | Doesn't fit a class — fallback |

Folder naming convention: `<YYYY-MM-DD>_<kebab-case-topic>` — date first for chronological sort.

### 7. Write the sort plan

Save to project root as `_video_sort_plan.md`. Structure:

```markdown
# Sort plan — <YYYY-MM-DD> run

## Source
~/Downloads — N total, M unique, K duplicates

## Duplicates (will not move)
- <list of -2 filenames matched to canonical>

## Proposed folders
### <YYYY-MM-DD>_<topic-slug> (N clips)
- 20260415_180919.mp4 → <topic-slug>_01.mp4
- 20260415_181117.mp4 → <topic-slug>_02.mp4
- ...

(repeat per folder)

## Open identifications (uncertain)
- <any clips you couldn't confidently classify>
```

Then **stop and wait for `MODE: EXECUTE`** before any `cp`.

### 8. Execute the copy

After `MODE: EXECUTE`:
- For each folder, `mkdir -p` the dest dir.
- `cp -p` each source → dest with renamed pattern `<topic-slug>_NN.mp4` (zero-padded, chronological).
- For files >500 MB, run each `cp` in its own bash call.
- After each batch, `stat -c %s` size-check every dest against source. Re-copy any truncated.

### 9. Write CapCut prompts

For each topic folder, write `prompt.txt`:

```
=== CapCut AI Clipper settings ===
Output: 9:16 vertical
Auto music-sync: ON
Auto-captions: ON
Voice-highlight: OFF
Pace: <fast | medium | slow based on topic>
Length: <15-45s based on clip count>

=== Prompt body (paste below the === into the AI Clipper field) ===
<400-char prompt body tailored to the topic>
```

**Brand defaults (always apply unless Brandon overrides):**
- 9:16 vertical (TikTok / Reels / Shorts target)
- Auto music-sync ON
- Auto-captions ON
- Voice-highlight OFF (footage typically has no narration)

**Pace by topic:**
- Unboxing → fast, anticipation arc, save reveal for 3/4 mark
- Product showcase → medium, hero shot at start and end
- Setup / install → medium, montage with one wide context shot
- BTS / workflow → medium, satisfying-loop feel
- Workshop tour → slow, cozy, let establishing shots breathe
- Educational tip → medium, lead with hook + payoff
- Tutorial → medium, numbered-step cadence
- Event → fast, energy + crowd cuts

**Prompt body template (adapt per folder, stay under 400 chars):**
> Build a `<length>s` `9:16` `<topic-class>` reel from this footage. Open with `<hook beat>`, build through `<middle beat>`, end on `<close beat>`. Sync to music drops. Auto-caption any voiceover. Pace: `<pace>`.

### 10. Write Drafts/_README.md

At `Drafts/_README.md` write a fresh index:
- Date range covered
- Total unique clips
- Folder index table (folder name → clip count → one-line topic description)
- Duplicates list (still in Downloads, safe to delete manually)
- Open identifications (anything you flagged uncertain in step 7)

### 11. Wrap up

Final report to Brandon:
- Total clips moved, total GB
- Folders created
- Anything flagged as uncertain
- Reminder: originals still in Downloads, safe to clear after spot-check

This run qualifies as a **milestone** for the `layerup-session-checkpoint` task — it'll log to the Notion Change Log DB on its next run.

## Pitfalls (encoded from the proof-of-concept run)

- **Downloads is read-only for `rm`/`mv` in Cowork.** Always `cp`. Never try to clean up source files.
- **Bash 45s timeout truncates large copies.** Files >500 MB need their own bash call. Always size-verify after.
- **Off-by-one in resume batches.** If a batch fails partway, re-derive the mapping. Don't trust the partial.
- **CapCut prompt cap is ~500 chars.** Target 400. No official AI Clipper number, but consistent across CapCut prompt fields.
- **Filename timestamp format.** Phone clips usually have `YYYYMMDD_HHMMSS` in the filename — parse this for chronological sort. Falls back to `stat` mtime if not present.

## Output

After a successful run, the following exists:

```
Layer Up Catalog & Marketing/
├── _video_sort_plan.md          # the plan you got MODE: EXECUTE on
└── Drafts/
    ├── _README.md
    ├── <YYYY-MM-DD>_<topic-1>/
    │   ├── <topic-1>_01.mp4
    │   ├── <topic-1>_02.mp4
    │   └── prompt.txt
    └── <YYYY-MM-DD>_<topic-2>/
        ├── ...
        └── prompt.txt
```

Originals untouched in `~/Downloads`.
