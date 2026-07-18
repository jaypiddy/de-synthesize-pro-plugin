# Film Stock Reference — De-Synthesize Pro

Exact color-science phrasing for the "[Film-stock color-science instruction]" slot of the prompt template. Each stock is a distinct combination of color temperature, saturation, contrast, and highlight behavior — pick one, never blend two in the same prompt. All 9 stocks from the De-Synthesize Pro app.

| Stock | Type | Instruction language |
|---|---|---|
| **Kodak Portra 400** (default) | Color negative | "Apply the warm, golden skin tones and natural highlight roll-off characteristic of Kodak Portra. Emphasize organic, flattering texture — the legendary portrait stock look." |
| **Kodak Gold 200** | Color negative | "Apply a vintage, warm consumer-film aesthetic with saturated yellows and punchy reds — the nostalgic 90s snapshot look of Kodak Gold." |
| **Kodak Ektachrome E100** | Color slide (E-6) | "Apply crisp, true-to-life color with clean, slightly cool shadows, neutral realistic whites, and exceptional sharpness, characteristic of Ektachrome slide film." |
| **Kodak Tri-X 400** | Black & white | "Transform the image into high-contrast black and white with gritty, punchy texture and deep, rich blacks — the legendary journalistic character of Tri-X." |
| **Fujifilm Velvia 50** | Color slide (E-6) | "Apply intense, hyper-saturated color and high contrast — deep greens and vibrant magentas typical of Velvia slide film. A distinctive, high-drama look." |
| **Fujifilm Provia 100F** | Color slide (E-6) | "Apply balanced, neutral professional color reproduction with incredible sharpness and a fine grain structure, characteristic of Provia 100F slide film." |
| **Fujifilm Pro 400H** | Color negative | "Apply a soft, airy pastel palette with a signature subtle cyan-green tint in the shadows, characteristic of Fujifilm Pro 400H — ethereal and light." |
| **Ilford HP5 Plus** | Black & white | "Transform the image into classic black and white with a wide tonal range, mid-gray richness, and a punchy organic grain — the raw street-photography character of Ilford HP5, less contrasty than Tri-X." |
| **Cinestill 800T** | Color negative, tungsten | "Apply a cinematic, tungsten-balanced look with halation — a soft red glow around bright highlights — and a cool, moody night-time color shift, characteristic of Cinestill 800T." |

## Notes (from app tuning)

- Black & white stocks (Tri-X, HP5) must switch the whole image to monochrome — say **"transform,"** not "tint" or "desaturate."
- Cinestill 800T's halation is its signature — always name it explicitly ("halation … around bright highlights"); "cinematic" alone won't reliably trigger it.
- Slide films (Velvia, Provia, Ektachrome) push saturation/contrast; negative films (Portra, Gold, Pro 400H, Cinestill) are gentler and prioritize skin-tone accuracy. Default to Portra 400 for portraits unless the user wants punch.
- Pro 400H vs Portra: both flattering, but Pro 400H reads cooler/airier (cyan-green shadows) where Portra reads warm/golden. Offer Pro 400H when the user says "soft," "airy," "ethereal," or "pastel."
- Provia vs Ektachrome: both neutral slide films — Provia is the Fuji-flavored pick (slightly cooler greens), Ektachrome the Kodak one (cleaner whites). Functionally interchangeable; follow the user's brand preference.

## Theme flavor (optional, for playback text)

The app themed its UI per brand — you can echo that in your delivery language: Kodak stocks = red/yellow darkroom talk, Fujifilm = green-box professional, Ilford/Tri-X = monochrome purist, Cinestill = neon night-shooter.
