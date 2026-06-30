# Slimming World Design Tokens — Figma Import Guide

Two token files are provided. Use whichever matches your Figma workflow:

---

## `tokens.json` → Tokens Studio (most common)

**Plugin:** [Tokens Studio for Figma](https://tokens.studio/) (free tier is sufficient)

### Steps

1. Install **Tokens Studio for Figma** from the Figma Community.
2. Open the plugin → **Settings** → set sync to **Local document** (or Git if you want version control).
3. Click the **Import** (⬆) button in the top-right of the plugin.
4. Paste or upload `tokens.json`.
5. The plugin will create three token sets:
   - **global** — raw palette values (colors, spacing, radius, shadow, font primitives)
   - **semantic** — alias tokens that reference `global` (e.g. `{global.color.green.500}`)
   - **typography** — composite text styles
6. Enable all three sets (toggle them on in the left panel).
7. Click **Apply to document** → Figma Styles will be generated automatically.

### After import

- Color styles appear under **Local styles → Colors** (e.g. `semantic/color/primary`, `global/color/green/500`)
- Text styles appear under **Local styles → Text** (e.g. `typography/Heading1`, `typography/Body`)
- Spacing & radius tokens are applied to components via the plugin's **Inspect** panel

---

## `tokens.w3c.json` → W3C DTCG / Figma Variables API

**Use when:** you are using Style Dictionary 4+, Theo, or the Figma Variables REST API.

### Style Dictionary (v4+)

```bash
npm install -g style-dictionary@4
```

Create `sd.config.json`:
```json
{
  "source": ["tokens.w3c.json"],
  "platforms": {
    "css": {
      "transformGroup": "css",
      "prefix": "sw",
      "buildPath": "dist/",
      "files": [{ "destination": "tokens.css", "format": "css/variables" }]
    },
    "js": {
      "transformGroup": "js",
      "buildPath": "dist/",
      "files": [{ "destination": "tokens.js", "format": "javascript/es6" }]
    }
  }
}
```

Then run: `style-dictionary build`

### Figma Variables REST API

The W3C file maps cleanly to Figma Variable types:

| Token `$type` | Figma Variable type |
|---|---|
| `color` | `COLOR` |
| `dimension` / `spacing` | `FLOAT` |
| `fontFamily` | (Text Style property) |
| `fontWeight` | (Text Style property) |
| `fontSize` | (Text Style property) |

Use the [Figma Variables API](https://www.figma.com/developers/api#variables) to `POST /v1/files/:key/variables` with the parsed token values.

---

## Token set structure

```
global/
  color/green/50–900      → raw green palette
  color/red/50–900        → raw red palette
  color/cream/50–500      → warm cream palette
  color/grey/50–900       → neutral greys
  font/family/…           → Playfair Display, Nunito Sans
  font/size/2xs–6xl       → 11-step scale
  font/weight/…           → 400–800
  font/lineHeight/…       → 1.2–1.8
  spacing/0–64            → 4px base grid
  borderRadius/none–full  → 0–9999px
  boxShadow/xs–brand      → 7 shadow levels

semantic/
  color/primary           → {global.color.green.500}
  color/accent            → {global.color.red.500}
  color/bg/…              → page, subtle, muted, highlight
  color/text/…            → primary, secondary, disabled, inverse, brand, accent
  color/border/…          → default, strong, brand, accent
  color/state/…           → success, warning, error, info (+ bg variants)

typography/
  Heading1–6              → composite font tokens
  Body Large / Body / Body Small
  Label / Caption / Overline
  Button LG / MD / SM
```

---

## Fonts required in Figma

Before applying text styles, ensure these are available in your Figma organisation:

| Font | Weights needed | Source |
|---|---|---|
| **Playfair Display** | Bold (700) | Google Fonts |
| **Nunito Sans** | Regular (400), Medium (500), SemiBold (600), Bold (700), ExtraBold (800) | Google Fonts |
