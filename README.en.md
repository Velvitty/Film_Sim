[한국어](README.md)&emsp;&emsp;English

# Film Filter Studio — Film Science Engine v2.0

A browser-based photo processor that applies film color, grain, halation and vintage-lens optical effects. It converts sRGB images to linear light, applies film characteristic curves and noise models, and exports JPEG files.

**Film format, independent grain and halation controls, lens aberrations and processing resolution** let you adjust both color and texture.

## Use online

Open **[film.yeon.at](https://film.yeon.at/)** without installing anything. The [online manual](https://film.yeon.at/manual.html) introduces the app and its controls.

[`CNAME`](CNAME) specifies `film.yeon.at`. The hosted app and a downloaded `index.html` use the same browser-side processing architecture.

## Features

- 13 color-negative, slide and black-and-white presets.
- Film-specific ISO, emulsion, gamma, grain and halation information.
- Film look: 0–100%; grain and halation: independently 0–250%.
- Texture scaling for half-frame, 35mm, medium and large formats.
- A modern APO option and seven vintage-lens models; aberration strength: 0–150%.
- Original resolution or limits of approximately 24, 12 or 6 megapixels.
- Before/after comparison, processing logs and JPEG download.

## Technology

| Technology | Purpose |
| --- | --- |
| HTML / CSS / vanilla JavaScript | Interface and processing state |
| FileReader / Image / Canvas 2D | File input, pixel access and output |
| Float32Array / lookup tables | Linear RGB, density calculations and characteristic curves |
| Custom image-processing functions | Blur, resampling, noise, halation and lens aberrations |
| localStorage | License acceptance |
| Google Fonts | Web fonts |

No application server, API key, external JavaScript framework, package installation or build step is required.

## Run locally

Choose **Code → Download ZIP**, extract the archive and open `index.html` in your browser. Alternatively, with Git and Python 3 installed:

```sh
git clone https://github.com/Velvitty/Film_Sim.git
cd Film_Sim
python3 -m http.server 8000 --bind 127.0.0.1
```

- App: <http://localhost:8000/index.html>
- Manual: <http://localhost:8000/manual.html>

Local use does not require a custom domain. Hosting requires serving the static files and configuring the domain, DNS and HTTPS separately.

## Workflow

1. Accept the license agreement shown on first use.
2. Load a browser-supported photo such as JPG, PNG or WebP.
3. Select a film and review its characteristics panel.
4. Adjust film-look strength, grain and halation.
5. Select a film format. Larger formats make grains smaller relative to the image frame.
6. Optionally select a vintage lens and aberration strength. The default is **Modern APO (no correction)**.
7. Choose a processing resolution and apply the filter. The default is a maximum of 12MP.
8. Compare the result with the original and download the JPEG. Apply the filter again after changing settings.

## Presets and settings

| Category | Options |
| --- | --- |
| Color negative | Portra 400, Pro 400H, Superia 400, Gold 200, ProImage 100, Ektar 100, Natura 1600 |
| Slide | Velvia 50, E100D, Kodachrome 64 |
| Cinema-derived | CineStill 800T |
| Black and white | HP5 Plus, Kentmere 400 |
| Film formats | Half-frame 18×24, 35mm 36×24, medium format 645, medium format 6×7, large format 4×5 |
| Lenses | Modern APO, Tessar-type, fast double-Gauss, 58mm swirl-bokeh, uncoated triplet, thorium, Petzval, plastic toy |

Film format controls relative texture scale; it does not crop to that aspect ratio. The implementation has 13 presets, despite the manual's introductory reference to 16.

## Detailed specifications

### Ranges and defaults

| Control | Range | Initial default | Effect |
| --- | --- | --- | --- |
| Film | 13 presets | Portra 400 | Selects `FILM_DB` color, tone and texture parameters |
| Film look | 0–100%, steps of 1% | 100% | Blends the complete processed sRGB result with original pixels |
| Grain | 0–250%, steps of 1% | 100% | Adjusts density fluctuations as `grain.sigma × amount` |
| Halation | 0–250%, steps of 1% | 100% | Adjusts reflected light as `halation.strength × amount` |
| Film format | Five formats | 35mm | Maps image long edge to film long edge in millimeters |
| Lens | Eight models including modern APO | Modern APO | Selects `LENS_DB` parameters |
| Aberrations | 0–150%, steps of 1% | 100% | Scales chromatic aberration, edge effects, glow, flare and vignetting |
| Resolution | Original / 24 / 12 / 6MP | Maximum 12MP | Downsizes larger inputs while preserving aspect ratio |

At 0% film look, the app returns original RGB at the **processing resolution**. Resizing and JPEG re-encoding still occur; this is not a byte-for-byte original-file copy. Zero grain or halation disables that effect while preserving tone and the other effects.

**Modern APO vignetting:** although labeled as having no correction, `LENS_DB.none.vig.natural` is `0.10`. Vignetting is applied separately, so a small natural falloff remains at 100% aberration strength. Set aberrations to 0% to disable it too.

Settings do not automatically re-render the image. Use **Apply → wait for completion → Download**; otherwise, you may download the previous result. Reset clears the photo and effect settings but retains the resolution selection, logs and license acceptance.

### Film parameters

These are **simulation parameters in `FILM_DB`**, not measurements supplied by the manufacturers. ISO is descriptive and does not change camera ISO or EXIF. Grain size is the modeled effective diameter; sigma is noise strength in logarithmic-stop space.

| ID | Preset | ISO | Gamma R/G/B | Grain µm / sigma | Halation strength / radius mm |
| --- | --- | ---: | --- | --- | --- |
| `portra400` | Kodak Portra 400 | 400 | 0.86 / 0.88 / 0.90 | 11.0 / 0.075 | 0.10 / 0.16 |
| `pro400h` | Fujifilm Pro 400H | 400 | 0.82 / 0.83 / 0.845 | 12.5 / 0.072 | 0.09 / 0.15 |
| `superia400` | Fujicolor Superia X-TRA 400 | 400 | 0.99 / 1.02 / 1.04 | 14.5 / 0.092 | 0.10 / 0.15 |
| `gold200` | Kodak Gold 200 | 200 | 0.92 / 0.92 / 0.94 | 15.0 / 0.088 | 0.12 / 0.18 |
| `proimage100` | Kodak ProImage 100 | 100 | 0.94 / 0.95 / 0.96 | 9.5 / 0.058 | 0.08 / 0.14 |
| `ektar100` | Kodak Ektar 100 | 100 | 1.06 / 1.08 / 1.10 | 7.0 / 0.044 | 0.07 / 0.13 |
| `velvia50` | Fujichrome Velvia 50 | 50 | 1.34 / 1.36 / 1.38 | 6.0 / 0.036 | 0.05 / 0.11 |
| `e100d` | Kodak Ektachrome E100D | 100 | 1.20 / 1.21 / 1.22 | 7.5 / 0.042 | 0.06 / 0.12 |
| `kodachrome64` | Kodachrome 64 | 64 | 1.23 / 1.25 / 1.29 | 5.2 / 0.030 | 0.04 / 0.10 |
| `cinestill800t` | CineStill 800T | 800 | 0.84 / 0.85 / 0.86 | 14.0 / 0.105 | 0.62 / 0.45 |
| `natura1600` | Fujicolor Natura 1600 | 1600 | 0.76 / 0.77 / 0.78 | 26.0 / 0.165 | 0.14 / 0.22 |
| `hp5plus` | Ilford HP5 Plus | 400 | 1.00 / 1.00 / 1.00 | 21.0 / 0.130 | 0.08 / 0.14 |
| `kentmere400` | Kentmere 400 | 400 | 1.06 / 1.06 / 1.06 | 25.5 / 0.150 | 0.11 / 0.17 |

Gamma alone does not determine the look. The sensitivity matrix, channel gains, toe and shoulder, tint offsets, grain clumping and base fog also contribute. Black-and-white presets mix RGB through `mono.weights` and do not add color noise.

### Format conversion

| ID | Displayed size | Long edge used |
| --- | --- | ---: |
| `half` | 18×24 | 24mm |
| `135` | 36×24 | 36mm |
| `645` | 56×41 | 56mm |
| `6x7` | 70×56 | 70mm |
| `4x5` | 127×102 | 127mm |

`pxPerMm = max(width, height) / formatLongEdgeMm`. At a 6,000px long edge, 35mm gives about 166.67px/mm and 4×5 gives about 47.24px/mm. Portra 400's 11µm grain corresponds to approximately 1.83px and 0.52px, respectively, but the engine's minimum cell size of 1px makes the latter 1px. Differences between formats can therefore be limited at small output sizes.

### Lens models

CA values describe corner R/B scale errors at a 3,000px long edge and 100% aberration strength. `soft`, `swirl`, `flare` and `vig` are model coefficients, not measured resolution or optical-performance ratings.

| ID | Model | CA R/B (px) | Edge soft / swirl | Flare | Natural / mechanical vignetting |
| --- | --- | --- | --- | --- | --- |
| `none` | Modern APO | 0 / 0 | 0 / 0 | 0 | 0.10 / 0 |
| `tessar1950` | 1950s Tessar-type | 1.1 / -1.6 | 0.30 / 0.05 | 0.030 | 0.28 / 0.16 |
| `gauss1960` | 1960s fast double-Gauss | 1.4 / -2.0 | 0.34 / 0.10 | 0.026 | 0.30 / 0.22 |
| `swirl58` | 58mm swirl-bokeh | 1.6 / -2.4 | 0.46 / 0.55 | 0.030 | 0.30 / 0.26 |
| `triplet1930` | 1930s uncoated triplet | 2.0 / -2.8 | 0.50 / 0.18 | 0.075 | 0.36 / 0.30 |
| `thorium1970` | 1970s thorium lens | 0.9 / -1.2 | 0.22 / 0.04 | 0.034 | 0.26 / 0.12 |
| `petzval1860` | 1860s Petzval | 2.6 / -3.4 | 0.62 / 0.78 | 0.060 | 0.40 / 0.42 |
| `toy` | Plastic toy lens | 2.8 / -3.6 | 0.66 / 0.22 | 0.055 | 0.42 / 0.50 |

Lateral CA is `presetCA × (imageLongEdge / 3000) × aberrationAmount`. A blurred proxy is blended into the corners with a cubic radial weight capped at 0.85. Swirl is `swirl × aberrationAmount × normalizedRadius² × 0.32` radians. There is no depth-map or subject-segmentation-based optical defocus calculation.

## Processing pipeline

The engine modifies RGB in an `ImageData` object in eight stages. Canvas supplies 8-bit RGBA input; intermediate calculations use per-channel `Float32Array` buffers.

| Stage | Processing | Representation |
| --- | --- | --- |
| 1 | Decode sRGB and expand bright values | Linear RGB, `v × (1 + 1.45 × v²)` |
| 2 | Lens aberrations, glow, flare and vignetting | Resampling, blurring and blending in linear light |
| 3 | Halation, emulsion scattering and dye-cloud diffusion | Highlight extraction and channel-wise diffusion approximations |
| 4 | Sensitivity mixing, interimage effect, characteristic curves and tint | Log stops relative to middle gray 0.18 |
| 5 | Luminance and color grain | Noise in log-stop space |
| 6 | Restore linear values, add fog and monochrome tint | `0.18 × 2^density + fog`, limited by `dmax` |
| 7 | Adjacency effect and acutance | Blur-difference enhancement in square-root space |
| 8 | Encode sRGB and blend look intensity | Blend with original RGB; clamp to 0–255 |

The result is written back to Canvas and encoded as JPEG with quality `0.96`. Stage 1 is an empirical highlight expansion for the model; it does not recover clipped highlight detail.

### Curves and lookup tables

For `g = exposureStops × gamma`, `curveEval()` uses:

```text
g >= 0: shoulder × (1 - exp(-g / shoulder))
g <  0: -toe × (1 - exp(g / toe))
```

Channel gain is added as `log2(gain)`. Printing exposure combines 72% of the white-reference correction with the preset's `exposure`. This is the app's tone model, not a measured reproduction of manufacturer sensitometric curves.

| Lookup table | Size | Range / purpose |
| --- | ---: | --- |
| `SRGB_TO_LIN` | 256 | Decode 8-bit sRGB |
| `LIN_TO_SRGB` | 4,097 | Linear 0–1 to sRGB with linear interpolation |
| `EXP2_LUT` | 4,097 | Powers of two from -14 to 8 stops, with linear interpolation |
| Per-channel characteristic curves | 2,049 each | -16 to 10 stops; endpoint values outside the range |
| Grain amplitude | 257 | Density-dependent amplitude from `peak`, `width`, `floor` |
| Radial aberration / vignetting | 513 each | Weights, rotation and falloff as a function of normalized radius |

`EXP2_LUT` returns 0 at or below -14 stops and 256 at or above 8 stops. Final sRGB conversion clamps linear values at or below 0 to 0, and at or above 1 to 255. The implementation uses the curve's “density” as output stops.

### Grain, halation and blur

- Grain cell size is `c1 = max(1, sizeUm / 1000 × pxPerMm)`; clumps and larger mottling use `2.7 × c1` and `6.8 × c1`.
- Three noise layers are weighted by `clump` and normalized by their sum of squared weights. Fine cells of 1.05px or smaller use hash noise.
- `NoiseField` uses smoothstep interpolation and keeps only two required cell rows. The random seeds are fixed in the code, so identical dimensions, input and settings do not produce freshly randomized grain on each run.
- Density-dependent amplitude comes from the mean RGB stops, converted to linear brightness, then a lookup using `sqrt(Y)`. Color presets add two independent color-noise fields and per-channel `layer` weights.
- Halation extracts the positive part above a threshold from `0.3R + 0.59G + 0.11B`, squares it and multiplies by 2.2. After blur, film-specific tint and strength are added to linear RGB.
- Halation radius is `radiusMm × pxPerMm`. A 6,000px long edge with 35mm and CineStill 800T gives about 75px; its 100% strength coefficient is 0.62.
- Spherical glow uses per-channel highlights and quarter-size proxies. Corner aberration uses roughly one-third-size proxies and flare roughly one-twelfth-size proxies, subject to minimum dimensions.
- `blurSigma()` normally approximates a Gaussian with three horizontally and vertically separable box-blur passes. Some callers use one or two. Vertical passes retain overwritten rows in a ring buffer.

This is a visual simulation using numerical models. It is not a scientific reproduction of an entire optical and photochemical process, and does not guarantee exact reproduction of real-film or lens results or recovery of lost image information.

## Repository and code

| File | Role |
| --- | --- |
| `index.html` | Interface, Film Science Engine, preset databases and image I/O |
| `manual.html` | Introduction and user guide |
| `CNAME` | Hosting domain |
| `README.md` / `README.en.md` | Korean / English documentation |

| Component | Responsibility |
| --- | --- |
| `FILM_DB`, `LENS_DB`, `FILM_FORMATS` | Film, lens and format parameters |
| `renderFilm()` | Rendering stages |
| `buildCurveLUT()`, `curveLookup()` | Film characteristic curves |
| `applyGrain()`, `applyHalation()` | Grain and highlight spread |
| `resampleRadial()`, `applyCornerAberration()` | Chromatic and corner aberrations |
| `handleFile()`, `applyFilter()`, `downloadImage()` | Interface, processing and file I/O |

### Data structures

Film and lens parameters are defined in `index.html`, not loaded from an external file or server.

| `FILM_DB` field | Type and purpose |
| --- | --- |
| `name`, `iso`, `kind`, `emulsion`, `note` | Strings/numbers used by the selection interface and descriptions |
| `exposure` | Printing exposure in stops |
| `tone` | RGB arrays for `gain`, `gamma`, `toe`, `shoulder`, `fog`: per-channel color balance, tone and fog |
| `spectral` | 3×3 sensitivity matrix; rows are output channels |
| `interimage` | Number: channel mixing / saturation coefficient in log-stop space |
| `shadowTint`, `highTint` | RGB stop arrays that shift shadow and highlight colors |
| `grain` | `sizeUm`, `sigma`, `clump`, `chroma`, `layer`, `peak`, `width`, `floor`: grain size, strength, color and density-dependent distribution |
| `halation` | `strength`, `radiusMm`, `threshold`, `tint`: the size, color and target brightness of light spread |
| `scatter`, `chromaDiffusion` | Numbers: emulsion-scattering and color-diffusion coefficients |
| `acutance`, `dmax` | Numbers: edge enhancement and linear-channel ceiling |
| Optional `mono` | RGB `weights` and `tint` arrays for monochrome mixing and final toning |

| `LENS_DB` field | Purpose |
| --- | --- |
| `name`, `note` | Interface labels and descriptions |
| `ca` | R/B lateral-chromatic-aberration array, referenced to a 3,000px long edge |
| `lca` | Blue-channel blur coefficient, proportional to the image long edge and aberration amount |
| `spher` | `strength`, `threshold`, `radiusMm`, `edge`: glow and edge emphasis |
| `corner` | `soft` and `swirl`: edge blur and rotational blending |
| `vig` | `natural` and `mech`: natural and mechanical vignetting |
| `flare`, `micro`, `tint` | Flare, local-contrast reduction and glass transmission tint |

The UI's `films` array is separate from `FILM_DB`; when adding films within the permitted scope, the DB object and `films` array must use matching IDs for the film to appear in the selection interface. `buildSelects()` creates lens and format selectors from their databases. See the license before modifying or redistributing the source.

`renderFilm(srcData, opts, hooks)` returns `Promise<ImageData>` and modifies its input directly. Options are `filmId`, `intensity`, `grainAmount`, `halationAmount`, `format`, `lensId`, `lensAmount`; 100% is represented by `1`. Internal hooks `hooks.stage(pct, msg)`, `hooks.log(msg)` and `hooks.yield()` connect progress, logging and UI updates. This is not a separately published package API.

### Execution and memory

Rendering runs on the main thread, without Web Workers, WebGL or WebGPU. The manual's WebGL claim differs from the current implementation. Some stages yield with `setTimeout(..., 0)` to update the display; this does not make all computation parallel or eliminate UI pauses.

For `N = width × height`, the principal arrays use the following approximate decimal MB (1,000,000 bytes per MB). These are arithmetic buffer sizes, not measured peak memory:

| Buffer | Bytes | 6MP | 12MP | 24MP |
| --- | --- | ---: | ---: | ---: |
| RGBA ImageData | `4N` | 24MB | 48MB | 96MB |
| Three RGB Float32 planes | `12N` | 72MB | 144MB | 288MB |
| Two Float32 scratch planes | `8N` | 48MB | 96MB | 192MB |
| Total of the six arrays above | `24N` | 144MB | 288MB | 576MB |

Original-image decoding, Canvas buffers, effect proxies, JPEG/Base64 strings and previews add more memory. Selecting 12MP therefore does not limit total memory use to 288MB. The resolution cap applies to the processing Canvas dimensions **after** reading the original, so very large inputs can consume substantial memory during loading too.

Downscale factor is `sqrt(targetMP × 1,000,000 / inputPixelCount)` with rounded dimensions. Small images are not enlarged. Rounding may place the result slightly above or below the nominal cap. The code does not promise a fixed processing time, minimum RAM or universal maximum resolution.

## Output, privacy and troubleshooting

- JPEG quality is `0.96`; filenames use `<film name>_<timestamp>.jpg`.
- Choose original resolution to preserve pixel dimensions; other limits proportionally downsize larger photos.
- Photos remain in browser memory; there is no upload feature. Google Fonts still makes font-related network requests.
- License acceptance and its timestamp are stored in localStorage.
- Processing uses multiple floating-point buffers; reduce resolution to 6MP or 12MP when memory is limited. “No file-size limit” does not remove browser resource limits.
- RAW and HEIC support depends on browser decoding; try JPG, PNG or WebP if loading fails.
- Original EXIF is not copied to the result, and JPEG does not preserve transparency.

## License

Use is subject to the source-header and in-app EULA conditions for personal study, education and non-commercial research. Redistribution, distribution of modified versions, sales and commercial use are restricted without the author's permission.

The EULA mentions `LICENSE.txt`, but that file is not in the current repository. Consult the header of `index.html` and the in-app agreement for the full notice.

Copyright © 2026 Velvitty (Yeony). All rights reserved.
