# Reverse-Engineered Analysis of `koi-pond-garden` & Complete Prompt Suite for `japanese-house.html`

Based on inspecting [`souranyp-stack/koi-pond-garden`](https://github.com/souranyp-stack/koi-pond-garden) (~8,309 lines / 549 KB in `koi-pond.html`, alongside `prompt/one-shot-prompt.txt` and `prompt/edit-existing-file.txt`), this document provides:

1. **Deep Architectural Deconstruction of `koi-pond.html`** — How the 14-section specification translates into an 8,300+ line production WebGL engine.
2. **Master One-Shot Prompt (`one-shot-prompt.txt`)** — The exact structural counterpart to your koi pond prompt for building **`japanese-house.html`**: a hyperrealistic, two-story Modern Japanese House (*Gendai Jūtaku*) with a Ground Floor, First Floor, integrated 2-car Glass-Showcase Garage, streaming free CC0 PBR materials + procedural synthesis fallbacks, and cinematic lighting.
3. **Iterative Polish & Audit Prompt (`edit-existing-file.txt`)** — The companion prompt for running multi-round browser verification, screenshot inspection, shader/PBR lookdev fixes, and performance auditing on `japanese-house.html`.

---

## Part 1: Reverse-Engineering `souranyp-stack/koi-pond-garden`

Inspecting [`souranyp-stack/koi-pond-garden`](https://github.com/souranyp-stack/koi-pond-garden) reveals why `prompt/one-shot-prompt.txt` produces an 8,309-line masterpiece instead of a generic 400-line Three.js boilerplate:

| Architectural Mechanism in `koi-pond.html` | Why It Forces Hyperrealism | How We Map It to `japanese-house.html` |
| :--- | :--- | :--- |
| **1. Strict 15-Part `<script>` Topology (Sections 0–14)** | Prevents the model from skipping helper layers or tangling dependencies. Every subsystem (`TEX.*`, `patchMaterial`, geometry builders, passes, audio, `autoQuality`) has a dedicated slot. | Preserves the exact **Sections 0–14** layout: Constants -> Math/Noise -> Hybrid Online CC0 PBR + Procedural `TEX.*` -> Architectural CSG/Extrusion Helpers -> Renderer/Sky/IES Rig -> `patchMaterial` -> Site/Garden -> Ground Floor + Garage -> First Floor + Roof -> Bespoke Furniture + Sports Car -> Reflections/Glass/Post -> Camera/Walkthrough Physics/Audio -> UI & `autoQuality` Loop. |
| **2. Single `patchMaterial` Shader Hook (Section 5)** | Rather than writing 30 disconnected `ShaderMaterial` classes that lose Three.js PBR lighting, `patchMaterial` hooks `MeshPhysicalMaterial.onBeforeCompile` so **every surface shares global uniforms** (`uTime`, `uSunDir`, `uWet`, `uSnowCover`, etc.). | Uses `patchMaterial(mat, opts)` across every architectural material to inject **world-space triplanar UV mapping** (zero stretched textures on concrete cutouts), **analytic multi-light IES & linear LED cove evaluation**, **contact ambient occlusion at wall/floor joints**, and **dynamic rain streaks/wetness darkening**. |
| **3. Hybrid Online Free CC0 PBR + `TEX.*` Synthesis (Section 2)** | In `koi-pond.html`, `TEX.*` builds `Float32Array` & `DataTexture` maps (`TEX.granite`, `TEX.wood`, `TEX.caustics`). For an architectural interior/exterior walkthrough, combining **streamed CC0 PBR maps & HDRIs** ([Poly Haven](https://polyhaven.com/our-api), [ambientCG](https://ambientcg.com/list), [Three.js jsDelivr CDN](https://sbcode.net/threejs/loading-assets/)) with `TEX.*` procedural detail maps guarantees photorealistic close-up grain with zero broken textures offline. | Streams real CC0 1K/2K PBR albedo, normal, roughness, and equirectangular HDRIs from CORS-enabled CDNs via `THREE.LoadingManager`, blended with procedural high-frequency detail maps (`TEX.concreteFormwork`, `TEX.hinokiGrain`, `TEX.tatamiWeave`, `TEX.basaltEpoxy`, `TEX.yakisugi`, `TEX.zenGravel`). |
| **4. Multi-Pass Reflection, Refraction & Post Pipeline (Sections 10 & 12)** | Uses a half-res planar reflection pass, full-res depth-sampled refraction pass, half-res 12-sample normal-oriented SSAO, 5-mip bloom, god rays, physical DOF, and ACES Filmic grading. | Implements a **Planar Mirror Reflection pass** for the exterior reflecting pool, wet basalt patio, and polished epoxy garage floor; physical **transmission glass (`ior: 1.52`)** for the garage showcase partition and courtyard walls; plus **SSAO + 5-mip Bloom + Volumetric Sun God-Rays + Physical Autofocus Bokeh DOF**. |
| **5. Parametric Centerpieces & Articulated Physics (Sections 6–11)** | Specifies exact mathematical curves (480-point Catmull-Rom pond SDF, parametric koi body profiles `s = 0..1`, 15-bone SkinnedMesh hand with sphere collision). | Specifies an exact architectural coordinate grid (`18 m × 13 m` footprint, `y = 0.0 m` Ground Floor, `y = 3.2 m` First Floor, 15 cantilevered stair treads), raycasted sliding shoji/glass doors and sectional overhead garage door, and a **parametric lofted Japanese Grand Tourer coupe** (`4.75 m` long, clearcoat metallic paint, 21" multi-spoke wheels, LED blade DRLs) inside the glass-walled garage. |
| **6. Directed Cinematic Tour + Frosted-Glass UI (Sections 13–14)** | Combines an automated film tour (letterbox bars, serif captions, timed shots, dips to black) with a frosted-glass bottom dock (`backdrop-filter: blur(20px)`), restyled glass `dat.gui`, and `autoQuality` scaler. | Keeps the exact frosted-glass design system, restyled glass `dat.gui`, keyboard shortcuts (`C`, `P`, `O`, `V`, `E`, `G`, `L`, `T`, `M`, `H`, `I`, `1`–`6`), and a **12-shot directed architectural film tour** spanning both floors, the courtyard, and the garage showcase. |

---

## Part 2: Master One-Shot Prompt (`one-shot-prompt.txt`)

Copy and paste the prompt below to generate the complete `japanese-house.html` application from scratch:

```text
Build an ultra-realistic, interactive Modern Japanese House architectural walkthrough (Gendai Jūtaku — Kengo Kuma & Tadao Ando inspired) with a Ground Floor, First Floor, and an integrated 2-car Glass-Showcase Garage as ONE self-contained HTML file named japanese-house.html. No build step, no bundler, no local asset folder: every architectural mesh, custom furniture piece, grand tourer vehicle, sound, and animation is generated in the file, paired with streaming free CC0 3D PBR textures and equirectangular HDRIs from public CDNs (Three.js r160 jsDelivr CDN, Poly Haven, ambientCG) and backed by procedural 1024^2 DataTexture / Canvas PBR synthesis so every surface renders with crisp normal, roughness, metalness, and AO maps under all network conditions. The only external code loads are three.js r160 UMD / addons from cdnjs/jsDelivr (https://cdnjs.cloudflare.com/ajax/libs/three.js/0.160.0/three.min.js) and dat.gui 0.7.9 from cdnjs (https://cdnjs.cloudflare.com/ajax/libs/dat-gui/0.7.9/dat.gui.min.js). Target 60 fps on a laptop GPU at 1.6x device pixel ratio.
Work in rounds. After every implementation round: run it in a browser (e.g. python3 -m http.server 8765), take screenshots, measure (fps, frame-time, draw calls, and any metric the round defines), fix what the screenshots show, and only then move on. Commit to git after each round with an imperative subject and a body that explains what changed and why. Do not stop at "it renders": self-verify until the screenshots match architectural photography standards.

0. File layout
One <script> in 15 annotated sections (Sections 0–14), in this exact order:
- Section 0: parameters, architectural grid constants, room bounds & lighting presets;
- Section 1: math/noise utilities (hash, value noise, 2D/3D Worley cellular noise, fbm2/fbm3, smoothstep, smootherstep, lerp, clamp, Catmull-Rom interpolation, 2D/3D box/plane signed-distance helpers);
- Section 2: hybrid PBR textures (async online CC0 PBR texture/HDRI loader with CORS + procedural canvas/Float32 DataTexture synthesis `TEX.*` & Sobel height-to-normal generator);
- Section 3: geometry helpers (BufferGeometry merge, architectural wall extruder with boolean doorway/window cutouts and mitered corners, timber louver array generator, bevelled slab builder, lathe, tube, Catmull-Rom lofting);
- Section 4: renderer, Preetham sky, sun, PMREM environment & IES/area light rig;
- Section 5: shared material injections (`patchMaterial`);
- Section 6: site, perimeter walls, karesansui Zen gravel garden & exterior reflecting pool basin;
- Section 7: stones, granite stepping slabs, tsukubai water basin & contemporary tōrō lanterns;
- Section 8: Ground Floor architecture (genkan, double-height living/dining/kitchen pavilion, tsubo-niwa central courtyard, cantilevered floating staircase) + integrated 2-car Glass-Showcase Garage;
- Section 9: First Floor architecture (mezzanine gallery bridge, washitsu tatami tea room with yukimi-shoji, master bedroom suite, hinoki ofuro soaking bath, cantilevered engawa balcony, deep charred-cedar roof);
- Section 10: bespoke interior furniture, monolithic kitchen island, architectural luminaires, courtyard vegetation (Japanese red maple, black bamboo, moss) & parametric Japanese Grand Tourer sports car in the garage;
- Section 11: water simulation (reflecting pool, tsukubai basin, steaming hinoki bath), planar reflection pass & architectural glass refraction/transmission;
- Section 12: post-processing pipeline (half-res SSAO, 5-mip bloom, volumetric god rays, physical bokeh DOF, ACES Filmic & weather LUT grading);
- Section 13: camera modes (12-shot Cinematic tour, First-Person Walkthrough with stair/collision physics & interactive raycasting, Drone Cutaway Orbit) & procedural Web Audio;
- Section 14: frosted-glass UI, restyled dat.gui, autoQuality scaler & main loop.

1. Renderer, sky, sun & lighting rig
WebGLRenderer, HDR HalfFloatType main target with 4x MSAA (2x on Low), ACESFilmicToneMapping, exposure 0.95. PCFSoftShadowMap 4096 (2048 on Low) with bias -0.00015 and normalBias 0.018, updated every other frame (or on sun/door movement) through an orthographic shadow camera tightly fitted to the 24 m x 20 m estate across all layers.
Sky & Environment: Preetham analytic sky shader plus an FBM volumetric cloud deck lit brighter toward the sun with self-shadowing, blended with an online CC0 equirectangular environment map loaded from jsDelivr (`https://cdn.jsdelivr.net/gh/mrdoob/three.js@r160/examples/textures/equirectangular/venice_sunset_1k.hdr`) and re-baked into a PMREM environment cube map when the sun or weather moves.
Sun from time of day (5.5–21.5 h, default 16.8 Golden Hour) and azimuth (default 235 deg). Directional sun intensity ~3.8 at Golden Hour (kelvin-to-RGB color curve from 2400K sunset to 5800K noon) plus hemisphere sky/ground bounce light.
Render layers, every mesh on one primary layer: EXTERIOR=1, GROUND_FLOOR=2, FIRST_FLOOR=3, GARAGE=4, GLASS=5, SKY=6, WATER=7.

2. Hybrid CC0 online textures, procedural `TEX.*` & shared material injections (`patchMaterial`)
Online Free 3D Material Loader + Procedural PBR Synthesizer:
Stream real CC0 PBR textures (`crossOrigin = 'anonymous'`, max anisotropy, `SRGBColorSpace` for albedo, `LinearSRGBColorSpace` for roughness/normal/AO) from public CDNs (`https://cdn.jsdelivr.net/gh/mrdoob/three.js@r160/examples/textures/hardwood2_diffuse.jpg`, `hardwood2_roughness.jpg`, `hardwood2_bump.jpg`, `waternormals.jpg`, `brick_diffuse.jpg`, and Poly Haven raw 1k texture URLs `https://dl.polyhaven.org/file/ph-assets/Textures/jpg/1k/...`), and combine or back them up with 1024x1024 Float32/Canvas procedural PBR generators in `TEX.*`:
- `TEX.concrete`: Tadao Ando board-formed architectural concrete with vertical cedar plank imprint, 1.8 m x 0.9 m tie-hole grid (recessed dark cones in AO/normal), subtle efflorescence, roughness 0.45.
- `TEX.hinoki`: Quartersawn Japanese cypress & warm white oak wide planks with cathedral grain, micro-bevel plank joints, roughness 0.30.
- `TEX.yakisugi`: Shou Sugi Ban charred cedar exterior siding for the First Floor overhang, deep carbonized crackle normal map, anisotropic specular sheen, roughness 0.48.
- `TEX.basalt`: Honed dark Kyoto basalt pavers & polished epoxy garage slab (micro-mica sparkle, tire-track roughness variation 0.14–0.28, clearcoat 0.6).
- `TEX.tatami`: Fine woven igusa rush mat normal + albedo with indigo-black linen heri edge tape.
- `TEX.shoji`: Mulberry washi paper fiber map with wooden kumiko lattice mask and warm subsurface scatter.
- `TEX.gravel`: Raked white Shirakawa granite gravel with concentric Zen wave ridges (samon) around courtyard stones.
- `TEX.carCarbon` & `TEX.leather`: Perforated Alcantara/leather interior and carbon-fiber splitter weave.

One `patchMaterial(mat, opts)` `onBeforeCompile` helper used by every `MeshPhysicalMaterial` / `MeshStandardMaterial`. It injects:
- World-position (`vWorldPos`) and world-normal (`vWorldNormal`) varyings;
- `opts.triplanar`: world-space triplanar texture sampling with blend exponent 4.0 so architectural walls, lintels, stair treads, and rocks never show stretched UVs on cutouts or edges;
- `opts.foliage`: vertex wind sway in `project_vertex` (slow branch sway + travelling wind gusts + high-frequency leaf flutter) with a matching custom depth material for crisp swaying leaf shadows;
- `opts.archAO`: analytic room-cavity ambient occlusion and floor-wall contact darkening computed from signed distances to the nearest floor slab, ceiling slab, and structural walls so corners look path-traced;
- Analytic multi-light architectural evaluation before/after the light loop: evaluates 16 warm 2700K interior IES downlights, linear under-tread/cove LED strips, the flickering living-room hearth, and 6 rectangular 5000K garage gallery light panels directly in the fragment shader with smooth distance falloff and rect-specular highlights;
- `opts.shoji` / `opts.leaf`: back-lit subsurface translucency so sunlight or interior lamps glow warmly through washi paper and red maple leaves;
- Weather & floor-cutaway effects: rain darkening (`albedo *= mix(1.0, 0.56, uWet * exteriorMask)`), wet specular puddle sheen (`roughness *= mix(1.0, 0.2, uWet)`), animated vertical rain streaks on exterior glass/walls when `uRain > 0`, snow accumulation on upward-facing exterior surfaces (`uSnowCover`), and clean horizontal section clipping (`if (vWorldPos.y > uClipHeight) discard;`) with a glowing architectural section-cut cap for Drone Maquette X-Ray mode.
Global uniforms shared by every patched material: `uTime`, `uSunDir`, `uSunColor`, `uWet`, `uRain`, `uSnowCover`, `uAutumn`, `uWindDir`, `uWind`, `uGust`, `uInteriorLight`, `uGarageLight`, `uHearthFlicker`, `uClipHeight`, `uFogDensity`, `uFogColor`.

3. Site, Ground Floor, First Floor & Glass-Showcase Garage
Estate footprint: 24 m (X) x 20 m (Z) parcel enclosed by a 2.2 m board-formed concrete privacy wall with horizontal hinoki slat screens and a Corten steel entry gate. House footprint: 18.0 m (X: -9.0..9.0) x 13.0 m (Z: -6.0..7.0).

A. Ground Floor (`y = 0.00 m` to `y = 3.20 m`, floor slab `0.28 m` thick):
- **Genkan (Entry Vestibule, `x: -3.0..0.5, z: 3.5..6.5`, floor `y = -0.16 m`)**: stepped-down honed dark basalt floor, monolithic hinoki step-up threshold (`agari-kamachi`) with concealed warm 2700K LED toe-kick strip, floating timber shoe cabinet, tall pivot cedar front door with bronze hardware, and a frameless floor-to-ceiling sightline window directly into the central courtyard.
- **Integrated 2-Car Glass-Showcase Garage (`x: -9.0..-3.0, z: -0.5..6.5`, floor `y = -0.10 m`)**:
  - Separated from the Genkan and Living Room by a **floor-to-ceiling structural acoustic glass showcase partition (`x = -3.0, z: -0.5..6.0`)** framed in matte black steel so the parked grand tourer is visible from the living room sofa and entry hall like an art gallery installation.
  - Polished dark basalt-epoxy floor with flush stainless linear drain, recessed perimeter LED floor grazing strip, custom matte-charcoal fluted oak tool & helmet storage wall (`x = -8.8`), wall-mounted glass-front EV supercharger dock with coiled cable, and a ceiling grid of 6 rectangular 5000K high-CRI gallery light boxes (`1.8 m x 0.6 m` each) that reflect cleanly along the car's body lines.
  - Motorized sectional cedar-and-frosted-glass overhead garage door (`z = 6.4, x: -8.4..-3.6`) mounted on curved steel ceiling tracks; rolls smoothly open/closed (`E` key or UI toggle).
- **Central Tsubo-niwa (Open-to-Sky Interior Courtyard, `x: -2.8..1.8, z: -2.2..2.5`)**:
  - Open vertically through both floors to the sky (`y = 0.0..6.5 m`), wrapped on all four interior sides by 3.2 m tall low-iron sliding glass walls (`transmission: 0.94, ior: 1.52, roughness: 0.03`).
  - Contains raked white Shirakawa Zen gravel, mossy volcanic boulders, a carved granite **Tsukubai** water basin fed by a bamboo *kakehi* pipe with a live dripping water stream and concentric surface ripples, and a centerpiece multi-trunk **Japanese Red Maple (*Acer palmatum*)** whose twisted bark and translucent crimson/amber leaves reach up past the First Floor gallery bridge.
- **Double-Height Living, Dining & Kitchen Pavilion (`x: 1.8..8.8, z: -5.8..6.2`)**:
  - Living Zone (`z: 0.2..6.2`): soaring `6.3 m` double-height ceiling clad in warm hinoki acoustic slats; sunken conversation pit (`y = -0.28 m`) with a modular oat-bouclé L-sectional sofa, low smoked-oak coffee table with ceramic matcha set, woven jute rug, and a 6.0 m floor-to-ceiling board-formed concrete chimney breast with a recessed blackened-steel fireplace burning live procedural shader flames.
  - Kitchen & Dining Zone (`z: -5.8..0.2`, beneath the First Floor mezzanine): monolithic `3.8 m` poured-concrete and fluted-walnut waterfall kitchen island, flush induction cooktop, minimalist blackened-brass gooseneck faucet, 4 leather barstools, full-height concealed walnut cabinetry with backlit travertine niche, and a solid live-edge hinoki slab dining table for 8 with sculptural timber chairs and a suspended linear brass-and-alabaster pendant lamp.
- **Cantilevered Floating Staircase (`x: 7.1..8.6, z: -1.8..3.4`)**:
  - 15 cantilevered thick hinoki timber treads (`0.09 m` thick, `1.35 m` wide, rise `0.213 m` per step) anchored directly into the eastern board-formed concrete wall with concealed steel pins, paired with a frameless laminated glass balustrade (`transmission: 0.92`), brushed bronze handrail, and warm 2700K LED grazers under every tread.

B. First Floor (`y = 3.20 m` to `y = 6.15 m`, reached by walking up the staircase):
- **Mezzanine Gallery Bridge (`x: 1.8..7.2, z: -2.2..0.8`)**: overlooks the double-height living room to the south and the open Tsubo-niwa maple courtyard to the west; features a frameless glass guardrail, floating oak library shelving, a low architect's reading desk with a glowing Isamu Noguchi *Akari* washi paper floor lantern, and a large minimalist sumi-e ink canvas.
- **Washitsu (Modern Tatami Tea & Meditation Room, `x: -2.8..1.8, z: -5.8..-2.2`)**:
  - Elevated `0.12 m` hinoki platform laid with 6 woven igusa tatami mats in classic layout, *tokonoma* display alcove with hanging calligraphy scroll, spotlight, and ikebana branch arrangement in a Bizen-ware vase, low lacquered *chabudai* tea table with 4 floor *zabuton* cushions, and motorized **Yukimi-shoji** (snow-viewing sliding screens: translucent washi lattice upper half with a sliding lower section that reveals clear glass looking directly into the maple courtyard and down into the living room).
- **Master Suite (`x: -8.8..-2.8, z: -5.8..2.2`, above the Garage)**:
  - Architectural low platform bed in smoked oak with layered linen duvet and pillows, upholstered headboard against a vertical hinoki louver feature wall, warm blown-glass pendant globes, and a walk-in wardrobe with backlit fluted glass doors.
  - **En-suite Hinoki Ofuro (Japanese Cypress Soaking Bath, `x: -8.8..-5.2, z: 2.2..6.0`)**: honed dark slate wet-room floor with linear drain, handcrafted rectangular *hinoki* cypress soaking tub filled with gently rippling, steaming hot water (`y = 3.75 m`), ceiling-mounted brass rain shower, monolithic stone double vanity with backlit circular mirror, and exterior motor-tilted horizontal cedar privacy louvers that cast razor-sharp komorebi sun stripes across the steam and water.
- **Cantilevered Engawa Balcony & Overhanging Roof (`z: 6.0..7.8` and `y = 6.15..6.65 m`)**:
  - Glass-railed upstairs cedar terrace overlooking the southern reflecting pool; deep `1.9 m` cantilevered flat roof with charred cedar (*Shou Sugi Ban*) fascia and warm hinoki soffits with recessed pinhole downlights.

4. Exterior garden, water simulation & architectural glass
- Southern **Engawa** timber veranda stepping down to an L-shaped **Architectural Reflecting Pool** (`x: 0.5..9.0, z: 6.2..10.2`, depth `0.35 m`, black pebble bottom) crossed by 6 floating bush-hammered granite stepping stones leading from the street gate to the Genkan.
- Perimeter black bamboo grove (*Phyllostachys nigra*: segmented culms, nodal rings, swaying instanced leaf cards), sculpted moss mounds, clipped Japanese black pine (*niwaki*), and two minimalist granite *tōrō* garden lanterns.
- Live **Water Simulation (128x128 damped wave equation FBO + 3 micro-Gerstner ripples)** shared across the exterior reflecting pool, courtyard tsukubai basin, and upstairs hinoki bathtub—driven by bamboo pipe water drops, footsteps on stepping stones, clicks, and falling rain rings.
- **Planar Reflection Pass** (half-res mirrored camera with oblique clip plane at `y = 0.0`) rendered every frame for the reflecting pool and wet stone/garage epoxy floor so the glowing 2-story house, maple tree, and sky mirror accurately.
- **Architectural Glass System**: multi-pane sliding doors, courtyard walls, stair balustrade, and the garage showcase wall use physical transmission/Fresnel reflection (`F0 = 0.04`), subtle edge green tint on thick glass slabs, and screen-space reflection/refraction sampling so both the interior reflection and the room behind are visible simultaneously.

5. Centerpiece vehicle in the Glass Garage (Parametric Japanese Grand Tourer)
Construct a hyper-detailed, showroom-grade Japanese Grand Tourer Coupe (`4.76 m` long, `1.92 m` wide, `1.31 m` high, parked at `x = -6.0, z = 2.8`, angled 8 deg toward the glass showcase wall):
- Smooth multi-section Catmull-Rom lofted bodywork: sculpted low hood with twin aerodynamic creases, wide spindle/mesh front intake, muscular rear haunches, fastback roofline, integrated ducktail spoiler, and carbon-fiber front splitter / rear diffuser.
- Automotive physical paint shader: dual-lobe metallic flake basecoat + `clearcoat: 1.0, clearcoatRoughness: 0.03`, animated reflection of the 6 overhead garage light panels.
- Lighting & details: razor-thin L-shaped LED daytime running light blades, triple-projector headlamps with glass lenses, full-width rear LED light bar with volumetric bloom, side mirrors, flush door handles, tinted cockpit canopy revealing a detailed leather interior (steering wheel, twin gauge binnacle, center console, bolstered sport seats), and 4 rotating 21-inch forged 10-spoke alloy wheels with low-profile Michelin-style treaded tires, cross-drilled ceramic brake discs, and painted 6-piston calipers.

6. Interactions, physics & raycast controls
- **First-Person Walkthrough Physics**: smooth capsule collision (`radius = 0.38 m`) against all walls, closed doors, kitchen island, sofa, car, and railings. Continuous elevation solver across the Genkan step (`y = -0.16 m`), sunken living pit (`y = -0.28 m`), Ground Floor (`y = 0.0 m`), all 15 cantilevered stair treads (`y = 0.0 -> 3.20 m`), First Floor (`y = 3.20 m`), Washitsu platform (`y = 3.32 m`), and exterior stepping stones. Verify: 0.0 mm wall penetration, smooth stair ascent/descent with peak vertical camera acceleration under `3.5 m/s^2`.
- **Interactive Raycast Actions (`E` key or click on highlighted element)**:
  - Click / `E` near **Garage Overhead Door**: smoothly rolls the 4.8 m sectional door up along its curved ceiling rails over 3.2 s (with motor hum and rail latch sound).
  - Click / `E` near **Courtyard Sliding Glass Doors** or **Upstairs Yukimi-Shoji**: glides the panels open/closed with critically damped spring motion.
  - `G` / **'Showcase Car'**: triggers the grand tourer welcome sequence (DRL sweep, headlights on, instrument cluster glow, muffled V8 startup & warm idle rumble) and cycles 4 factory paint finishes (*Structural Liquid Silver*, *Infrared Metallic*, *Midnight Sapphire*, *Stealth Matte Ash*).
  - `L` / **'Cycle Interior Lighting'**: cross-fades interior architectural lighting between *Natural Daylight*, *Warm Evening Lounge (2700K coves + hearth)*, and *Gallery Collector (100% garage showroom + artwork spots)*.
  - Click water / stepping stones / bamboo spout: spawns live ripple rings and water plip audio.

7. Camera modes & directed film tour
Modes (`C` cycles):
- **Cinematic (default)**: A directed 12-shot architectural film tour with 2.39:1 letterbox bars, serif titles + Japanese subtitles, and smooth dips to black, in this exact order and duration:
  1. *The Floating Pavilion* (11 s, low aerial glide across the southern reflecting pool and stepping stones),
  2. *Glass Garage Collector's Showcase* (11 s, slow dolly around the grand tourer under the 5000K light grid looking into the house),
  3. *Genkan & Courtyard Sightline* (9 s, crossing the basalt threshold toward the central maple),
  4. *Double-Height Living Sanctuary* (11 s, crane sweep from the sunken conversation pit and live hearth up to the 6.3 m hinoki ceiling),
  5. *Monolithic Kitchen & Hinoki Dining* (10 s, lateral track along the poured-concrete island and walnut joinery),
  6. *Tsubo-niwa: The Open Heart* (11 s, orbiting the dripping bamboo tsukubai basin and backlit red maple inside the glass courtyard),
  7. *Cantilevered Timber Ascent* (10 s, gliding up the 15 floating hinoki treads with under-step LED grazers),
  8. *Mezzanine Gallery Bridge* (9 s, high-angle view through the maple canopy down into the living room),
  9. *Washitsu & Yukimi-Shoji* (10 s, tatami-level 0.45 m camera as the snow-viewing screens slide open),
  10. *Master Suite in the Trees* (9 s, slow pan across the slatted cedar bedroom wall and pendant globes),
  11. *Hinoki Ofuro Soaking Bath* (10 s, push-in on the steaming cypress tub with slatted sunlight rays),
  12. *Twilight Reflections* (12 s, pulling back across the mirror pool as warm 2700K interior light fills both floors), then loop.
- **Manual Walkthrough**: pointer-lock or drag look, `WASD` move, `Shift` brisk walk, `Space` step/hop, `Q` crouch to tatami sitting height (`0.85 m`), mouse wheel walk speed, number keys `1`–`6` teleport directly to rooms (`1` Exterior Pool, `2` Garage, `3` Living/Kitchen, `4` Courtyard, `5` Upstairs Mezzanine/Tatami, `6` Master & Hinoki Bath).
- **Drone Maquette Orbit**: turntable/spherical orbit with interactive `uClipHeight` section slider (`0.5 m..7.0 m`) to inspect Ground Floor and First Floor plans in real time.
- **Physical Lens**: GPU autofocus on what is under the crosshair, default aperture `f/8` (crisp architecture), automatic `f/2.4` shallow depth of field on close-up shots, max bokeh size, lens sharpening `0.35`.

8. Weather / seasons & time of day (`T` cycles; GUI dropdown)
Presets, each cross-faded over 3.0 seconds:
- **Golden Hour (Komorebi — default)**: tod 16.8, azimuth 235, cloud 0.35, turbidity 2.4, fog 0.003 `(0.58,0.52,0.46)`, wind 0.3, interior lights 0.65, exposure 0.95, grade `(1.05, 1.0, 0.94)`.
- **Clear Noon (Architectural Daylight)**: tod 12.3, azimuth 170, cloud 0.20, turb 1.8, fog 0.002 `(0.62,0.68,0.75)`, wind 0.25, interior lights 0.15, exposure 0.85, grade `(1.0, 1.0, 1.0)`.
- **Kyoto Rain & Dusk (Blue Hour)**: tod 18.1, cloud 1.0, overcast 0.88, sun x0.08, hemi x1.8, fog 0.016 `(0.14,0.18,0.24)`, `rain = 1`, `wet = 1`, wind 0.5, interior lights 1.0, garage lights 1.0, exposure 1.15; falling rain streaks outside and down the open courtyard shaft, rain rings on the pool and tsukubai, wet dark basalt/concrete with mirror sheen.
- **Winter Snow & Twilight Sanctuary**: tod 17.2, cloud 0.90, overcast 0.65, sun x0.25, hemi x1.7, fog 0.018 `(0.48,0.54,0.64)`, falling snow flakes outdoors and in the open courtyard, `snowCover = 1` on roof overhangs, garden rocks, and courtyard gravel, steaming hinoki bath x2.0, interior lights 1.0, hearth x1.4, exposure 1.10.

9. Post-processing
Main HDR -> SSAO (half res, 14-sample normal-oriented hemisphere, bilateral depth blur) -> Bloom (prefilter, threshold 0.95, 5-level mip chain, strength 0.28) -> Volumetric God Rays (sun shafts through louvers and courtyard maple) + Bokeh DOF -> Composite: exposure, ACES Filmic, per-weather colour grade, vignette 0.28, film grain 0.008, sharpening 0.35, letterbox. Quality High/Low with `autoQuality`: first scale resolution smoothly `1.0 -> 0.6` when frame time stays over 22 ms, then switch to Low; recover when fast.

10. Audio (Web Audio, all procedural; starts on first click/key)
Master chain: sources -> dry + dynamic convolution reverb (impulse response morphs smoothly between *Open Garden*, *Resonant Concrete/Glass Hall*, *Damped Tatami Room*, and *Wet Slate Bath* based on camera room bounds) -> indoor/outdoor low-pass filter (doors closed attenuates exterior wind/rain to 900 Hz) -> master gain (volume 0.7).
Continuous loops: bamboo *kakehi* water trickle into the courtyard basin, fireplace wood crackle & warm ember hum (distance-attenuated from hearth), wind rustling through maple and bamboo leaves, soft rain on glass/gravel (when raining), and steaming bath water murmur.
Events: material-specific footsteps (basalt tap, hinoki plank step, soft tatami brush, gravel crunch), sliding glass/shoji rail glide + soft wood stop, motorized garage door roll + track clank, tactile brass light switch click, grand tourer V8 starter crank + deep idle burble (`G`), water drop plip, and a distant temple bell / *shishi-odoshi* bamboo knock every 20–35 s. `M` mutes/unmutes.

11. UI
Frosted-glass design system (`backdrop-filter: blur(20px) saturate(160%)`, hairline borders, soft sheen, warm architectural bronze accent `#e09145`).
Bottom-right toolbar dock of icon+label buttons: Camera Mode, Room Teleport (`1–6`), Hold Shot, Freeze Time, Garage Door (`E`), Slide Doors, Car Showcase (`G`), Lighting (`L`), Weather (`T`), Sound (`M`), Clean View (`V`).
Top-right `dat.gui` restyled as a translucent glass sheet with folders: *Environment* (weather preset, time of day, azimuth, wind, rain, snow), *Architecture* (floor cutaway height, garage door open, shoji open, bathroom louver tilt), *Lighting* (interior dimmer, garage gallery dimmer, fireplace intensity, colour temperature), *Garage & Car* (car paint finish, headlights, engine idle, wheel rotation), *Rendering* (quality, auto-drop, exposure, bloom, bloom threshold, SSAO, god rays, grain, vignette, sharpening), *Lens* (DOF, f-stop, max bokeh), *Camera* (film tour shot, letterbox, change weather each loop, mode, walk speed, hold, freeze, clean view, volume, sound).
Top-left: FPS/ms + Current Room badge (`Genkan`, `Glass Garage`, `Living Pavilion`, `Tsubo-niwa`, `1F Mezzanine`, `1F Washitsu`, `1F Master Suite`, `1F Hinoki Bath`) and a Guide card listing every shortcut with key chips (`I` or `?` toggles). Loader with progress bar while online CC0 PBR maps and procedural textures build. Toast for transient messages.

12. Verification targets (do these before calling it done)
- Screenshots: (1) Twilight exterior hero across the reflecting pool showing both floors, (2) Inside the 2-car garage looking past the grand tourer through the glass wall into the house, (3) Double-height living room & sunken pit with Golden Hour louver shadows, (4) Monolithic kitchen & hinoki dining table, (5) Central Tsubo-niwa courtyard with red maple and dripping tsukubai, (6) Cantilevered floating staircase ascent, (7) First floor Washitsu tatami room with yukimi-shoji, (8) First floor Hinoki Ofuro soaking bath with slatted sun rays, plus all 4 weather presets.
- UV & material check: 0 stretched textures on any wall cutout, lintel, or stair tread; all online CDN texture loads gracefully fall back to or blend with `TEX.*` procedural maps with zero console errors or black meshes.
- Walkthrough physics check: 0.0 mm wall/glass penetration; continuous smooth elevation climb from Ground Floor (`y = 0.0 m`) up all 15 floating stair treads to First Floor (`y = 3.2 m`) with peak vertical camera acceleration under `3.5 m/s^2`; no NaN in camera or door states.
- Performance on a 2x display: High 50–90 fps, Low 100+ fps.
- Every shader compiles with no console errors; the full script parses cleanly.
```

---

## Part 3: Companion Iterative Audit Prompt (`edit-existing-file.txt`)

In [`souranyp-stack/koi-pond-garden`](https://github.com/souranyp-stack/koi-pond-garden), the `prompt/` folder also includes `edit-existing-file.txt`—used to drive visual self-verification and bug-hunting on the existing HTML file. Here is the tailored `edit-existing-file.txt` for `japanese-house.html`:

```text
Inspect and upgrade the existing japanese-house.html in place. Do not split it into multiple files or add a build step.
Run a local HTTP server, open japanese-house.html in a headless/live browser at 1.6x–2x DPR, capture native-resolution screenshots of all 12 film tour shots and all 6 room teleports across all 4 weather presets (Golden Hour, Clear Noon, Kyoto Rain & Dusk, Winter Snow), and audit against these exact visual and numerical checks:

1. Architectural & PBR Material LookDev:
- Check every board-formed concrete wall, hinoki floor, yakisugi overhang, tatami mat, and basalt slab in close-up screenshots: verify crisp normal-map relief, realistic roughness contrast (wet vs dry, honed stone vs brushed metal), and zero UV stretching around doorway/window cutouts (confirm world-space triplanar sampling is active).
- Verify that online CC0 PBR texture/HDRI loads complete without CORS or 404 failures, and test offline fallback mode to confirm `TEX.*` procedural maps alone still look indistinguishable from architectural photography.

2. Glass Garage & Grand Tourer Centerpiece:
- Inspect the acoustic glass wall (`x = -3.0`) from both the Living Room and inside the Garage: confirm there is no z-fighting, that the 6 overhead 5000K gallery light panels create clean linear specular highlights across the car's clearcoat hood and roofline, and that the sectional overhead garage door tracks smoothly along its ceiling rails when `E` is pressed.

3. Ground Floor <-> First Floor Staircase & Walkthrough Continuity:
- Automate a walkthrough path from the Genkan (`y = -0.16 m`) -> Living Room (`y = 0.0 m`) -> Sunken Pit (`y = -0.28 m`) -> up all 15 cantilevered floating stair treads -> First Floor Mezzanine (`y = 3.20 m`) -> Washitsu Tatami (`y = 3.32 m`) -> Master Bedroom & Hinoki Bath. Log camera Y and vertical acceleration every frame: verify 0.0 mm wall penetration, zero camera snagging on stair nosings, and smooth ceiling clearance under the mezzanine soffit.

4. Lighting, Reflections & Post-Processing Balance:
- Check Golden Hour komorebi sun stripes through the bathroom cedar louvers and courtyard maple leaves: no shadow acne (`bias` / `normalBias` tuned) and no peter-panning on furniture legs.
- Check Twilight / Rain mode: verify the planar reflection on the exterior reflecting pool and wet garage floor aligns accurately with the mirrored geometry, and that interior 2700K warm coves balance against the cobalt blue-hour sky without blowing out whites under ACES Filmic tone mapping.

Fix every visual defect found in the screenshots, re-measure FPS and frame-time (target 50–90 fps on High, 100+ fps on Low), and commit each round to git with a clear imperative subject and diagnostic body.
```
