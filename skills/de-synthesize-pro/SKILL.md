---
name: de-synthesize-pro
description: "Run the De-Synthesize Pro darkroom pipeline END-TO-END via the Higgsfield MCP: take an AI-generated portrait, build a film-development edit prompt (film stock + skin texture recovery + ISO grain), and execute it with Nano Banana 2 (image-to-image) on Higgsfield, delivering the developed image. Trigger on: \"develop this image\", \"run it through the lab\", \"de-synthesize this with Higgsfield/Nano Banana\", \"develop as Portra/Tri-X/Cinestill\", \"give this the film treatment and generate it\", or any de-synthesize request where the user wants the result GENERATED (not just a prompt written). If the user only wants prompt text to paste elsewhere, or no Higgsfield MCP tools are available, use the sibling skill de-synthesize-portrait instead. Portraits of people only — not products or landscapes."
---

# De-Synthesize Pro (Higgsfield execution skill)

The skill version of the De-Synthesize Pro app (github.com/jaypiddy/De-Synthesize-Pro): removes the synthetic/plastic AI look from a generated portrait by reconstructing skin texture and applying real film-stock color science + grain, then **actually runs the edit** on Higgsfield. This is an image EDIT — the person's identity, pose, lighting, props, and set must survive unchanged.

**Default engine: `nano_banana_2`.** This skill's prompt language is calibrated for the Nano Banana (Gemini image) family. Other models (GPT Image 2, Soul) respond differently to the same prompts — see "Model selection" before switching.

## The four knobs

Ask only for what's ambiguous; offer defaults, don't interrogate. A user who just says "develop this" gets all defaults.

### 1. Film stock (default: Kodak Portra 400)
Nine stocks, each a distinct color-science instruction — see `references/film-stocks.md` for the exact phrasing per stock. Never blend two stocks in one prompt. Negative films (Portra, Gold, Pro 400H, Cinestill) flatter skin; slide films (Velvia, Provia, Ektachrome) push saturation/contrast; Tri-X and HP5 are full black-and-white conversions.

### 2. Skin Texture Recovery (0–100, default 50)
The user gives a percentage (or a word like "subtle"/"natural"/"raw"); map it to one of three instruction tiers:

| Range | Tier | Instruction language |
|---|---|---|
| < 30 | Polished | "Apply a light photographic grain and micro-texture to the skin, effectively removing synthetic digital smoothness while keeping the appearance clean and polished." |
| 30–70 | Natural (default) | "Restore realistic skin texture with natural variation. Preserve the person's features while adding the subtle depth and organic feel of real skin, bridging the gap between digital generation and authentic photography." |
| > 70 | Film Detail | "Apply professional high-fidelity skin reconstruction — natural pore structure and fine organic textures. The result should feel raw and authentic, resembling an unretouched high-resolution film negative, while honoring the original features and avoiding any exaggerated blemishes." |

**Calibration lesson (from production tuning of the app):** never ask the model for "imperfections," "blemishes," "moles," or "freckles" — it overshoots and hallucinates distracting spots. Always frame as *texture reconstruction*, never *flaws*. The only permitted use of "blemishes" is the negative guard "avoiding any exaggerated blemishes."

### 3. ISO grain (default: 400)
Separate instruction from color science so any ISO pairs with any stock:

> "Add a substantial amount of organic film grain, resembling a [ISO] ISO film, to the image, preserving the original aesthetic. The grain should feel raw and authentic."

ISO 200 = fine/tight · 400 = classic balanced · 800 = visibly gritty · 1600 = heavy, moody.

### 4. Resolution (default: 1k)
Maps directly to Nano Banana's `resolution` parameter: `1k`, `2k`, or `4k`. Higher resolutions cost more credits — mention that if the user asks for 4k.

## Assembling the prompt

One flowing paragraph, no labels, in this order:

> A portrait with [skin-tier instruction], while maintaining consistent lighting, props, composition, and set design, and preserving the person's exact facial features and identity. [Film-stock color-science instruction from references/film-stocks.md]. Add a substantial amount of organic film grain, resembling a [ISO] ISO film, to the image, preserving the original aesthetic. The grain should feel raw and authentic.

The preservation clause ("maintaining consistent lighting … exact facial features and identity") is non-negotiable — it's what stops the edit from drifting into a different person or a re-lit scene.

Aspect ratio and resolution are generation parameters, never text in the prompt.

## Higgsfield execution workflow

1. **Get the source image into Higgsfield.** Three paths depending on where the image lives:
   - **Local file, Apps UI client (claude.ai / desktop app):** call `media_upload_widget` (type: image) immediately — do not ask the user to attach the image in chat; remote MCP tools can't read chat attachments. The widget returns a `media_id`.
   - **Local file, CLI/Claude Code session:** call `media_upload` with the filename to get a presigned URL, PUT the bytes (curl), then `media_confirm` to get the `media_id`.
   - **Web URL:** call `media_import_url`; pass the returned `media_id`. Never pass an https:// URL in `medias[].value`.
2. **Detect the aspect ratio.** Read the source dimensions (locally: `sips -g pixelWidth -g pixelHeight <file>`; otherwise ask or eyeball from the widget preview) and pick the CLOSEST ratio Nano Banana supports: 1:1, 3:2, 2:3, 4:3, 3:4, 4:5, 5:4, 9:16, 16:9, 21:9. Matching the source ratio is the default behavior — don't silently letterbox a 3:4 portrait into 1:1.
3. **Confirm settings before generating.** Every Higgsfield job costs credits — no trial runs. State the recipe in darkroom terms ("Developing as Kodak Portra 400 @ ISO 400, 50% texture recovery, 3:4, 1k — good?") and, for 2k/4k or multi-image runs, preflight with `generate_image` + `get_cost: true` first.
4. **Generate:**
   ```
   generate_image {
     params: {
       model: "nano_banana_2",
       prompt: <assembled prompt>,
       medias: [{ value: <media_id>, role: "image" }],
       aspect_ratio: <detected>,
       resolution: <1k|2k|4k>
     }
   }
   ```
   If the tool result returns a `recovery_tool`, call it immediately.
5. **Deliver.** Show the result and give the user the image URL. Offer to save it locally with the app's filename convention: `<Stock-Slug>-iso<ISO>-<YYYYMMDD-HHMMSS>.png` (e.g. `Kodak-Portra-400-iso400-20260717-143000.png`).
6. **Contact sheet (optional).** If the user wants to compare stocks, run one generation per stock with all other knobs held constant — but confirm first, since each frame is a separate charged job.

## Model selection

| Model | When | Notes |
|---|---|---|
| `nano_banana_2` (default) | Standard development runs | Fast, high quality, this skill's prompt dialect is tuned for it |
| `nano_banana_pro` | User asks for maximum fidelity / 4k print work | Same prompt dialect, slower, costlier |
| `gpt_image_2` | NOT YET CALIBRATED | Available on Higgsfield (1k/2k/4k, quality tiers). Different prompt dialect — GPT Image tends to interpret texture instructions more literally and drift identity more on edits. Do not silently substitute it; if the user asks for it, warn that the recipe language hasn't been tuned for it yet and results will differ from the Nano Banana baseline. Calibrate as a future variant of this skill. |

The whole point of per-model calibration: identical prompts produce different results per engine. When a new model variant is tuned, record its dialect adjustments in this file rather than forking the skill.

## Handling rejections

Portrait edit models most often fail on: poorly lit sources, extreme crops / non-standard portrait framing, and heavily stylized or non-human subjects. When a generation fails, never surface the raw API error. Translate it into the app's three-part format:

> **REJECTION:** what happened, in darkroom terms. **WHY:** the likely cause in plain English. **REMEDY:** one concrete fix (e.g. "use a well-lit image with a typical head-and-shoulders portrait framing"), then offer to retry.

## Reference

- `references/film-stocks.md` — all 9 stocks with exact color-science phrasing.
- Sibling skill `de-synthesize-portrait` — prompt-text-only version for pasting into external tools (AI Studio, Kling, etc.). Use it when the user wants the words, not the picture.
