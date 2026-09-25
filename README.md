# scroll-world

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/oso95/scroll-world)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![CI](https://github.com/oso95/scroll-world/actions/workflows/deploy.yml/badge.svg)](.github/workflows/deploy.yml)

https://github.com/user-attachments/assets/b08e641e-985b-4bd4-83ff-6750272d0c37

An agent skill — for Claude Code, Codex, and any `SKILL.md`-compatible agent — that
builds an immersive, **scroll-scrubbed "fly through the world" landing page** for any industry or brand — the kind where, as you scroll, a camera flies
from *outside* each scene *into* its interior, then flows on to the next scene with **no
cuts**. One continuous connected flight through a little generated world (think the Emons
logistics site, applied to whatever you want).

---

## ⚡ Quick Start & Live Deployment

This repository includes both the **AI Agent Skill** and a **production-ready live interactive showcase** configured for zero-setup deployment to Vercel and GitHub Pages.

### 1. Deploy directly to Vercel

Click below to fork and deploy your own live instance in under 60 seconds:

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/oso95/scroll-world)

### 2. Run locally

```bash
# Clone the repository
git clone https://github.com/oso95/scroll-world.git
cd scroll-world

# Start a local static server
npm run dev
# or
npx serve -l 3000 .
```

Open [http://localhost:3000](http://localhost:3000) in your browser to experience the scroll-scrubbed camera flight.

---

## Install

### Claude Code — as a plugin (recommended)

```
/plugin marketplace add oso95/scroll-world
/plugin install scroll-world@scroll-world
```

Then just ask for a scroll-through world landing page, or invoke `/scroll-world`.

### Codex & other agents — via the skills CLI

Using [Vercel's skills CLI](https://github.com/vercel-labs/skills), which installs into
Codex, Claude Code, Cursor, and 20+ other agents:

```bash
npx skills add oso95/scroll-world            # pick your agent(s) when prompted
npx skills add oso95/scroll-world -a codex   # or target Codex directly
```

In Codex, invoke it with `$scroll-world` (or `/skills` to browse), or just ask for a
scroll-through world landing page.

### Manually (drop-in skill)

Copy the skill folder into your agent's skills directory:

```bash
git clone https://github.com/oso95/scroll-world
cp -R scroll-world/skills/scroll-world ~/.claude/skills/   # Claude Code
cp -R scroll-world/skills/scroll-world ~/.codex/skills/    # Codex
```

## Requirements

- The [Monid CLI](https://monid.ai) with an API key and balance — the **default
  video-chain backend** (Seedance 2.0, billed per clip in USD; see below).
- The [Higgsfield CLI](https://higgsfield.ai), authenticated (`higgsfield auth login`),
  with credits — renders the scene stills, the `kling3_0` fallback, and the whole
  chain when Monid is absent.
- `ffmpeg` / `ffprobe` for frame extraction and encoding.
- Python 3 with Pillow (for the mobile portrait canvases; also the optional
  transparent-scene knockout).
- The [Codex CLI](https://github.com/openai/codex) (optional) — if present, the scene
  stills can be generated through Codex's built-in `image_gen` (the same GPT Image
  model), billed to a ChatGPT subscription instead of Higgsfield credits.
- About the Monid default: verified 2026-07-25 — first/last-frame conditioning
  frame-locks, so it renders the full seamless chain; frames travel via Monid's
  free workspace file system. Pay-per-use with no subscription or monthly expiry
  (a 6-scene 1080p chain ≈ $27). The skill re-checks the endpoint schema each
  build and keeps qualification probes in the pipeline for when the catalog
  changes; Higgsfield credits remain the fallback biller.

## What it does

It generates the art with AI: cohesive isometric diorama scenes (GPT Image 2 — via
Higgsfield, or the Codex CLI on a ChatGPT subscription) and the camera flights
themselves (Seedance image-to-video via **Monid by default**, pay-per-clip; Seedance
or Kling on Higgsfield credits as fallback — only models that can frame-lock a
seam), scrubbed
by scroll position — the same technique behind Apple's scroll-through product pages. The
camera genuinely moves; scroll only drives time. It's **framework-agnostic**: you get the
Higgsfield pipeline, the prompt templates, and a portable vanilla-JS scrub engine that
drops into plain HTML, Next.js, Vue, or a Python-served page — nothing assumes a stack.

When invoked, the skill:

1. **Interviews you** — the subject/industry + pitch, a brand kit (import from a URL, hand
   it over, or have it proposed), art direction, the ordered scenes the camera visits,
   whether you want the **mobile version** (a second chain rendered natively in 9:16
   portrait — composed for phones, not a crop of the landscape film), and the **budget** —
   render tiers and stills source shown with estimated credit costs, approved before
   anything generates.
2. **Generates the assets** — one still per scene, one "dive-in" camera
   clip per scene, and the **connector** clips that join consecutive scenes, generated
   from the actual rendered frames of their neighbours so every seam is frame-identical.
   Mobile opt-in renders a parallel portrait chain the same way, frame-locked against its
   own 9:16 renders.
3. **Wires it up** — a config-driven scroll engine that plays the whole chain as one
   flight, serving the portrait clips and posters automatically on phones.

## What's in the repo

```
scroll-world/
├── index.html                  live showcase landing page (Vercel ready)
├── scrub-engine.js             portable, zero-dependency scroll-scrub engine
├── vercel.json                 Vercel static routing & cache configuration
├── package.json                scripts for local development and CI
├── assets/
│   └── scenes/                 sample vector diorama scenes (desktop & mobile)
├── skills/
│   └── scroll-world/
│       ├── SKILL.md            procedure + seam rule + gotchas
│       └── references/
│           ├── prompts.md      intake checklist + Higgsfield prompt templates
│           ├── pipeline.md     batch scripts (generate → frames → connectors → encode)
│           ├── scrub-engine.js portable scrub engine
│           ├── index-template.html minimal standalone starter
│           └── knockout.py     background knockout for floating scenes
└── .claude-plugin/             Claude Code marketplace and plugin metadata
```

## GitHub & Vercel Deployment

For a detailed walkthrough on setting up your own GitHub repository and deploying to Vercel with custom domains, see [DEPLOYMENT.md](DEPLOYMENT.md).

## Notes

- Asset generation costs money (~N image gens on Higgsfield credits + ~2N-1 video
  gens billed per clip on Monid by default; the mobile chain doubles the video gens)
  and takes a while — the skill runs generations in the background and polls. Monid
  pricing is per-token and printed per run; Higgsfield pricing isn't exposed by its
  CLI, so the skill calibrates against your live balance. Either way the estimated
  total is stated before spending.
- The generated `.mp4`/`.webp` assets are produced per project. This repository includes built-in vector dioramas so the demo works instantly upon cloning or deploying.

## Star History

<a href="https://www.star-history.com/?type=date&repos=oso95%2Fscroll-world">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=oso95/scroll-world&type=date&theme=dark&legend=top-left&sealed_token=rsHNX9eWfbhlu820oC1dzsc66Y8UZI4dawuHvAUlbn36F0gwOWXRDi-Qq4QFopkoEJE7bzgXPUkAmSnmMcglxAo_rM7TvGDKFehk5MzprmeT2euDRbHnTQZIxEWwjjpGQ3nodpdblW6WjTssURtDxXO2MCVL_WgJ_WnCIoVbV8qhsB_Z-Eeo8KCyVerC" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=oso95/scroll-world&type=date&legend=top-left&sealed_token=rsHNX9eWfbhlu820oC1dzsc66Y8UZI4dawuHvAUlbn36F0gwOWXRDi-Qq4QFopkoEJE7bzgXPUkAmSnmMcglxAo_rM7TvGDKFehk5MzprmeT2euDRbHnTQZIxEWwjjpGQ3nodpdblW6WjTssURtDxXO2MCVL_WgJ_WnCIoVbV8qhsB_Z-Eeo8KCyVerC" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=oso95/scroll-world&type=date&legend=top-left&sealed_token=rsHNX9eWfbhlu820oC1dzsc66Y8UZI4dawuHvAUlbn36F0gwOWXRDi-Qq4QFopkoEJE7bzgXPUkAmSnmMcglxAo_rM7TvGDKFehk5MzprmeT2euDRbHnTQZIxEWwjjpGQ3nodpdblW6WjTssURtDxXO2MCVL_WgJ_WnCIoVbV8qhsB_Z-Eeo8KCyVerC" />
 </picture>
</a>

## License

MIT — see [LICENSE](LICENSE).
