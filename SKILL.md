---
name: skeptical-source-footage-audit
description: "Audit downloaded footage for badges, watermarks, privacy leaks, and hidden frames before using it. Use when you pulled trailers, third-party demos, creator clips or reels as b-roll and plan to cut windows out of them."
when-to-use: "You pulled official trailers, third-party demos, creator clips or reels as b-roll and plan to cut windows out of them, or any pipeline where source footage you don't own appears in a published video."
---

# Source footage audit (before it ships in your video)

## What this does (plain English)

Footage that looks safe is not always safe. This skill gives your AI assistant a step-by-step check to run on source video and images before they go into your own video. It catches things like a burned-in age-rating badge, a watermark, a screen recording that shows a personal URL or bookmark bar, a menu from a piracy tool, or a single off-topic frame hidden inside a fast-cut montage. It starts with a coarse contact sheet, then audits candidate windows at full resolution, checks brightness, checks privacy and policy, covers still images too, and logs every exclusion.

## Responsible use

This audit checks what is visible in a clip. It does not clear your rights to use it. Only use footage you own, have a license for, or are otherwise entitled to use, and keep the platform's content rules in mind.

## Install what you need

You need `ffmpeg`.

macOS:

```
brew install ffmpeg
```

Linux (Debian/Ubuntu shown; on Fedora use `sudo dnf install ffmpeg`, which may need the RPM Fusion repo enabled):

```
sudo apt install ffmpeg
```

Windows (PowerShell or Command Prompt; open a NEW terminal window after installing so the tools show up on PATH):

```
winget install Gyan.FFmpeg
```

Check it worked: `ffmpeg -version` and `ffprobe -version` should both print a version.

The commands in this skill are written for a Unix-style shell: macOS Terminal, Linux, or on Windows Git Bash or WSL. Windows PowerShell and Command Prompt do not have tools such as `grep`, `tail` and `sort`, and in PowerShell `curl` may be an alias for a different command.

## Worked example

Audit one downloaded clip, `src.mp4`, with the three commands from the tooling pattern below.

1. Coarse pass: build a 1-second contact sheet over the whole clip, then reject windows with obvious defects:

```
ffmpeg -i src.mp4 -vf fps=1 sheet_%03d.jpg        # 1s contact sheet
```

2. Fine pass on each candidate: grab full-resolution frames at the START, MID and END times of the window you plan to use (replace `<t>`) and read small on-screen text at full size:

```
ffmpeg -i src.mp4 -ss <t> -frames:v 1 full.png     # full-res grabs
```

3. Brightness audit: print per-second frame means and look for long near-black stretches:

```
ffmpeg -i src.mp4 -vf "fps=1,signalstats,metadata=print:key=lavfi.signalstats.YAVG" -f null - 2>&1 | grep YAVG   # brightness
```

4. Check privacy and policy items, audit still images at full res too, and log every exclusion (window plus reason) in your build README. An unaudited asset is a hard no.

---

The full production recipe follows.


## When to Use
- You pulled official trailers, third-party demos, creator clips, or reels as
  B-roll and plan to cut windows out of them
- Any pipeline where source footage you don't own appears in a published video

## Why
Official footage is NOT automatically safe. Real production catches: a studio
launch film containing a screen-recording window with a personal photo-share
URL and bookmark bar visible; a 30s spot trailer with a burned-in age-rating
badge in the corner; a tutorial whose on-screen demo prompt referenced
elections (violating a no-politics content policy); modding footage hiding
payload/piracy tool menus mid-clip; fast-cut montages hiding off-theme
single frames (a character face inside a 'beauty' shot).

## The procedure

1. COARSE PASS: build a contact sheet at 1s steps over the WHOLE clip
   (ffmpeg fps=1 frame grabs). Reject windows with obvious defects.
2. FINE PASS on candidates: coarse sheets MISS things. Rules:
   - Rating badges/watermarks: sample EVERY candidate source at 1s steps
     before use; if a badge/subtitle/watermark appears on ANY frame of a short
     spot trailer, exclude the WHOLE trailer (badges persist for its entire
     run). Long-form trailers are usually clean after the opening rating card -
     still fine-audit them.
   - Inserted third-party clips: a talking-head video may cut to a
     celebrity/podcast clip a coarse sheet labels as 'facecam'. After picking
     any facecam window, extract START/MID/END at FULL RES and verify the
     person/setting matches on all three.
   - Small on-screen text: audit at FULL RESOLUTION (1080p grabs of the final
     segment frames, not 400px tiles). Read the LAST lines of terminal/log
     windows - static offending text contaminates the whole region. Downscaled
     sheets called production terminal logs 'clean' while a piracy-tool command
     sat in every frame.
   - Fast-cut montages: audit each planned window's START and END frame (not
     just the midpoint); prefer windows verified as one continuous scene;
     freeze-pad (tpad) off a verified-clean last frame when the clean scene is
     shorter than the slot.
3. BRIGHTNESS AUDIT: compute per-second frame means (1fps); long near-black
   stretches read as broken/empty on mobile - split or shift those windows to
   brighter footage.
4. PRIVACY/POLICY AUDIT: exclude personal-browser windows, visible URLs/
   bookmarks/accounts, ambiguous competitor branding, and any on-screen text
   touching banned topics (politics etc.) even when your topic is clean.
5. STILL IMAGES TOO: press assets can embed third-party branding (an official
   hardware hero image once contained a TV screen showing a competing streaming
   app icon). Vision-audit every image at full res before compositing.
   Downscale 4K+ images to ~1600px for analysis tools (timeouts); if even that
   fails, DON'T SHIP the asset unaudited - swap the beat.
6. LOG every exclusion (window + reason) in the build README. An unaudited
   asset is a hard no regardless of how official the source is.

## Tooling pattern

```
ffmpeg -i src.mp4 -vf fps=1 sheet_%03d.jpg        # 1s contact sheet
ffmpeg -i src.mp4 -ss <t> -frames:v 1 full.png     # full-res grabs
ffmpeg -i src.mp4 -vf "fps=1,signalstats,metadata=print:key=lavfi.signalstats.YAVG" -f null - 2>&1 | grep YAVG   # brightness
```
Then montage the sheets and vision-review, or review manually.
