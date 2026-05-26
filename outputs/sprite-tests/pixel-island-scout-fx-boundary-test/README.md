# Pixel Island Scout FX Boundary Test

This run tests whether the current sprite pipeline can produce project-usable
pixel-art VFX that match the accepted island scout hero style.

Current execution status: raw sheets generated with the built-in `image_gen` /
Image2 path, processed into transparent frames and GIF previews, and reviewed
for first-pass visual/QC conclusions. Engine playback and compositing remain
to be verified.

## Style Lock

- Refined modern pixel art, not vector art, 3D, painterly, or smooth illustration.
- Bright tropical palette: turquoise water, teal magic, coral-orange sparks,
  warm sand, shell-white highlights, dark crisp outline.
- Hand-placed pixel clusters, readable at small mobile roguelite scale.
- Solid `#FF00FF` magenta background for processing.
- No characters inside these FX sheets. These are detached runtime VFX layers.

## Test Set

1. `reeflight-projectile`
   - Projectile loop, 4 frames, 2x2 source grid.
   - Tests projectile direction consistency, trail readability, and compact loop quality.

2. `blooming-tidepool-impact`
   - Impact burst, 6 frames, 2x3 source grid.
   - Tests multi-component burst cleanup, expansion timing, and `component_mode=all`.

3. `driftwood-compass-cast-aura`
   - Cast aura loop, 6 frames, 2x3 source grid.
   - Tests ring stability, complex symbol readability, and loop continuity.
   - The first candidate is retained as a useful failure sample because its
     animation is too subtle for primary skill feedback.

4. `driftwood-compass-cast-aura-v2`
   - Revised cast aura loop, 6 frames, 2x3 source grid.
   - Adds a clearly readable internal charge pulse while preserving a stable
     shell-compass footprint.

5. `coral-tide-slash`
   - Wide slash arc, 4 frames, 2x2 source grid, wide delivery frames.
   - Tests wide-FX containment, pivot/origin metadata, and non-compression.

6. `sandbar-snare-rune`
   - Ground trap sequence, 16 frames, 4x4 source grid.
   - Tests long-sequence consistency, dense detail, and temporal readability.

## Layout References

- `references/2x2-layout-guide.png`
- `references/2x3-layout-guide.png`
- `references/4x4-layout-guide.png`

These guides are only for slot count, safe padding, and centering. The generated
art must not reproduce guide boxes, borders, labels, safe-area marks, or center
marks.

## Generation Path

Preferred path:

1. Use the built-in `image_gen` / Image2 tool with each prompt under `prompts/`.
2. Save the selected raw output into `raw/<asset>-raw-sheet.png`.
3. Run the processing commands below.

Fallback path:

Use these CLI commands only if the built-in image tool is unavailable and an
operator explicitly chooses the local API-key-based fallback.

Set `OPENAI_API_KEY` first, then generate each raw sheet with the imagegen CLI.
Use the prompt files under `prompts/` and save outputs under `raw/`.

```powershell
$IMAGE_GEN = "$env:USERPROFILE\.codex\skills\imagegen\scripts\image_gen.py"

python $IMAGE_GEN generate --no-augment --quality high --size 1024x1024 `
  --prompt (Get-Content outputs\sprite-tests\pixel-island-scout-fx-boundary-test\prompts\01-reeflight-projectile.txt -Raw) `
  --out outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\reeflight-projectile-raw-sheet.png

python $IMAGE_GEN generate --no-augment --quality high --size 1024x1024 `
  --prompt (Get-Content outputs\sprite-tests\pixel-island-scout-fx-boundary-test\prompts\02-blooming-tidepool-impact.txt -Raw) `
  --out outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\blooming-tidepool-impact-raw-sheet.png

python $IMAGE_GEN generate --no-augment --quality high --size 1024x1024 `
  --prompt (Get-Content outputs\sprite-tests\pixel-island-scout-fx-boundary-test\prompts\03-driftwood-compass-cast-aura.txt -Raw) `
  --out outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\driftwood-compass-cast-aura-raw-sheet.png

python $IMAGE_GEN generate --no-augment --quality high --size 1024x1024 `
  --prompt (Get-Content outputs\sprite-tests\pixel-island-scout-fx-boundary-test\prompts\04-coral-tide-slash.txt -Raw) `
  --out outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\coral-tide-slash-raw-sheet.png

python $IMAGE_GEN generate --no-augment --quality high --size 1024x1024 `
  --prompt (Get-Content outputs\sprite-tests\pixel-island-scout-fx-boundary-test\prompts\05-sandbar-snare-rune.txt -Raw) `
  --out outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\sandbar-snare-rune-raw-sheet.png
```

## Processing Commands

```powershell
python skills\generate2dsprite\scripts\generate2dsprite.py process `
  --input outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\reeflight-projectile-raw-sheet.png `
  --target asset --mode projectile --rows 2 --cols 2 --label-prefix projectile `
  --align center --shared-scale --component-mode all --fit-scale 0.92 `
  --output-dir outputs\sprite-tests\pixel-island-scout-fx-boundary-test\processed\reeflight-projectile

python skills\generate2dsprite\scripts\generate2dsprite.py process `
  --input outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\blooming-tidepool-impact-raw-sheet.png `
  --target asset --mode impact --rows 2 --cols 3 --label-prefix impact `
  --align center --shared-scale --component-mode all --fit-scale 0.9 `
  --output-dir outputs\sprite-tests\pixel-island-scout-fx-boundary-test\processed\blooming-tidepool-impact

python skills\generate2dsprite\scripts\generate2dsprite.py process `
  --input outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\driftwood-compass-cast-aura-raw-sheet.png `
  --target asset --mode cast-aura --rows 2 --cols 3 --label-prefix aura `
  --align center --shared-scale --component-mode all --fit-scale 0.9 `
  --output-dir outputs\sprite-tests\pixel-island-scout-fx-boundary-test\processed\driftwood-compass-cast-aura

python skills\generate2dsprite\scripts\generate2dsprite.py process `
  --input outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\coral-tide-slash-raw-sheet.png `
  --target asset --mode slash-fx --rows 2 --cols 2 --label-prefix slash `
  --align center --shared-scale --component-mode all --fit-scale 0.96 `
  --cell-width 224 --cell-height 128 --pivot-x 0.5 --pivot-y 0.5 `
  --preserve-source-center-x `
  --output-dir outputs\sprite-tests\pixel-island-scout-fx-boundary-test\processed\coral-tide-slash

python skills\generate2dsprite\scripts\generate2dsprite.py process `
  --input outputs\sprite-tests\pixel-island-scout-fx-boundary-test\raw\sandbar-snare-rune-raw-sheet.png `
  --target asset --mode ground-rune --rows 4 --cols 4 --label-prefix snare `
  --align center --shared-scale --component-mode all --fit-scale 0.88 `
  --output-dir outputs\sprite-tests\pixel-island-scout-fx-boundary-test\processed\sandbar-snare-rune
```

## QC Gates

- No frame should touch or cross its cell boundary.
- The effect identity should remain recognizable across frames.
- Detached pieces should be intentional, not magenta cleanup debris.
- Projectile and aura should loop without a visual pop.
- Impact should read as start, bloom, peak, dissipate.
- Wide slash should stay wide in delivery frames instead of collapsing into a compact blob.
- Ground rune should preserve a stable center and footprint across all 16 frames.

## First-Pass Results

- `reeflight-projectile`: provisional pass. Distinctive shell-water
  silhouette, readable travel direction, no edge-touch frames.
- `blooming-tidepool-impact`: provisional pass. Strong multi-component burst
  and dissipation stages, no edge-touch frames.
- `driftwood-compass-cast-aura`: not accepted as primary output. Styling and
  registration are good, but frame-to-frame energy change is too restrained.
- `driftwood-compass-cast-aura-v2`: provisional pass. Noticeably stronger
  charge sequence with stable outer ring, no edge-touch frames.
- `coral-tide-slash`: provisional pass for wide-frame delivery. The strongest
  frame occupies most of the `224x128` delivery width without crossing it;
  runtime clipping/origin integration remains a required gate.
- `sandbar-snare-rune`: provisional pass as the high-complexity boundary
  candidate. Its 16-frame buildup/trigger/dissipation sequence is readable and
  remains inside frame bounds.

All accepted-candidate outputs include transparent sheets, per-frame PNGs,
GIF previews, prompt records, and pipeline metadata under `processed/`.
