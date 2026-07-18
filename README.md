# De-Synthesize Pro

A Claude Code plugin that removes the synthetic/plastic "AI look" from generated portraits. It builds a film-development edit prompt — real film-stock color science, skin-texture reconstruction, and ISO grain — and runs it **end-to-end on Higgsfield** with Nano Banana 2 (image-to-image), delivering the developed image.

It's the skill version of the [De-Synthesize Pro app](https://github.com/jaypiddy/De-Synthesize-Pro), with the app UI stripped out and the prompt-assembly engine kept intact.

## Requirements

- **Claude Code**
- The **Higgsfield MCP server** connected in your session (this is what actually generates the image). Without it, the skill can still write the prompt for you to paste elsewhere — but for the full pipeline you need Higgsfield.

## Install

```
/plugin marketplace add jaypiddy/de-synthesize-pro
/plugin install de-synthesize-pro@de-synthesize-pro
```

The first command registers this repo as a plugin marketplace; the second installs the plugin from it. (`de-synthesize-pro@de-synthesize-pro` = the plugin name @ the marketplace name — they happen to match here.)

To update later:

```
/plugin marketplace update de-synthesize-pro
```

### Install from a local clone instead

If you've cloned this repo locally and want to install without going through GitHub:

```
/plugin marketplace add /path/to/de-synthesize-pro-plugin
/plugin install de-synthesize-pro@de-synthesize-pro
```

## Use

Once installed, trigger it naturally or by command:

- `/de-synthesize-pro`
- "develop this portrait as Kodak Portra 400 at ISO 400"
- "run this through the lab as Cinestill 800T, high skin detail"

You'll be asked (with sensible defaults) for four knobs:

| Knob | Default | Options |
|---|---|---|
| **Film stock** | Kodak Portra 400 | 9 stocks — Portra 400, Gold 200, Ektachrome E100, Tri-X 400 (B&W), Velvia 50, Provia 100F, Pro 400H, Ilford HP5 Plus (B&W), Cinestill 800T |
| **Skin Texture Recovery** | 50% | 0–100 — Polished / Natural / Film Detail |
| **ISO grain** | 400 | 200 / 400 / 800 / 1600 |
| **Resolution** | 1k | 1k / 2k / 4k |

The image edit preserves the subject's identity, pose, lighting, props, and set — only the texture, color science, and grain change.

> **Note:** every Higgsfield generation costs credits. The skill confirms the recipe before generating and preflights cost for 2k/4k or multi-frame runs.

## What's inside

```
.
├── .claude-plugin/
│   ├── plugin.json         # plugin manifest
│   └── marketplace.json    # marketplace manifest (repo is its own marketplace)
├── skills/
│   └── de-synthesize-pro/
│       ├── SKILL.md         # the pipeline instructions
│       └── references/
│           └── film-stocks.md   # exact color-science phrasing for all 9 stocks
└── README.md
```

## Models

Defaults to `nano_banana_2`. Nano Banana Pro is available for maximum-fidelity / 4K work. GPT Image 2 is reachable on Higgsfield but the prompt language is **not yet calibrated** for it — the skill won't silently substitute it.

## License

MIT
