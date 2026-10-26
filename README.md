# skeptical-source-footage-audit (Grok skill)

A QA procedure for source footage: contact sheets, full-res fine pass, brightness check, privacy and policy audit, still images, and an exclusion log.

## Install

This repo is the skill folder: `SKILL.md` sits at the top level. Clone it into a skills directory.

User scope (all your projects), macOS or Linux:

    mkdir -p ~/.grok/skills
    git clone https://github.com/skeptical-agency/skeptical-source-footage-audit.git ~/.grok/skills/skeptical-source-footage-audit

User scope, Windows PowerShell (assumes Grok reads skills from `$HOME\.grok\skills`):

    New-Item -ItemType Directory -Force "$HOME\.grok\skills"
    git clone https://github.com/skeptical-agency/skeptical-source-footage-audit.git "$HOME\.grok\skills\skeptical-source-footage-audit"

Project scope (shared via git):

    mkdir -p .grok/skills
    git clone https://github.com/skeptical-agency/skeptical-source-footage-audit.git .grok/skills/skeptical-source-footage-audit

No git? Download the repo as a zip from GitHub and unzip it into a folder named `skeptical-source-footage-audit` inside the skills directory.

More skills: [skeptical-agency/grok-skills](https://github.com/skeptical-agency/grok-skills).

## Requirements

- `ffmpeg` on your PATH (macOS: `brew install ffmpeg`; Linux: `sudo apt install ffmpeg`; Windows: `winget install Gyan.FFmpeg`)
- A Unix-style shell for the brightness command that uses `grep` (macOS, Linux, or Git Bash/WSL on Windows)

## Use

Ask Grok things like:

> audit this footage for watermarks, badges and privacy leaks before I use it: <file>

SKILL.md has a plain-English intro, a worked example, the exact commands and the known pitfalls.

## Responsible use

This audit checks what is visible in a clip. It does not clear your rights to use it. Only use footage you own or are entitled to use.

## License

MIT, see LICENSE.
