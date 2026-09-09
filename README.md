# Cold Routes City Generator

A standalone, single-file procedural city generator built for the **Cold Routes** Unity project. It generates a full 3D city — coastline, road hierarchy, districts, blocks, and building massing — from a seed and a set of tunable parameters, renders it live in-browser via WebGL, and exports it as a Unity-ready package.

No build step, no dependencies, no framework. Open the HTML file in a browser and it runs.

## Visual target

A clean, restrained architectural-model aesthetic: white/light-grey buildings, soft shadows, muted roads and landscaping, minimal material variation. From gameplay zoom it should read like a physical planning model; up close, detail comes from **form** (parapets, recessed entrances, setbacks, stepped masses) rather than textures.

The guiding rule for the generation logic itself: **nothing should look randomly placed.** Roads, buildings, parks, and coastline should each have a legible reason for being where they are.

## Running it

Open the `.html` file directly in a browser (Chrome/Edge recommended for WebGL2). The city generates automatically on load using the default seed, and regenerates whenever you change a setting and click **Generate City**.

## Controls

| Input | Action |
|---|---|
| Left-drag | Orbit camera |
| Right-drag | Pan camera |
| Scroll wheel | Zoom |
| W / A / S / D | Fly camera |
| Shift (held) | Fly faster |
| Reset Camera button | Frame the whole generated city |

## Settings panel

- **Map** — seed, width/depth (metres), internal generation tile size
- **Water** — coast on/off, optional major river with adjustable width and bridge count
- **Special areas** — optional regional airport, optional stadium/arena
- **Districts** — CBD count (1–5, scales with map size), CBD size, skyline intensity, secondary centre count, low-rise amount, street wall strength, block/parcel grain, building shape detail, industrial openness, commercial corridor strength, overall city detail bias (cleaner / balanced / dense)
- **Roads** — road irregularity, local road density
- **Landscaping** — park amount, tree amount, waterfront development
- **Global** — road density, building density, min/max building height, geometry quality (performance / high / ultra), shape variation

## Generation pipeline (high level)

1. **Layout** — map bounds, coastline/river shape, CBD and secondary centre placement, special areas (airport, stadium).
2. **Coastline** — built as a shape grammar: a handful of named macro features (headland, bay, straight developed frontage, occasional inlet) blended smoothly, with fine noise layered on top only as texture. `coastFeatureAt(z)` exposes each stretch's classification for other systems to read.
3. **Road network** — arterial coordinates are taken directly from real destinations (CBDs, secondary centres, industrial zone, civic anchor, airport, special areas) rather than even spacing; nearby destinations merge into a shared arterial. Density-based filler arterials only appear where a coverage gap would otherwise be too large. Collectors and local streets subdivide from there, including an intentional dead-end (`stub`) pattern reserved for outer/suburban blocks large enough to justify one.
4. **Blocks** — each block is composed with frontage rows, optional rear rows, side wings, composition courts, district yards (paved industrial/commercial service areas), controlled open space, and — where a rear row exists — a thin paved service alley behind it.
5. **Buildings** — massed per parcel with district-appropriate form (setbacks, recesses, roof structures, footprints) at the restrained all-white palette.
6. **Rendering** — WebGL2, soft PCF shadows, distance fog, all in the `GLRenderer` class.

## Export

**Export Unity-ready OBJ (.zip)** produces a `ColdRoutesCity/` folder containing:

To import: drag the whole `ColdRoutesCity` folder into your Unity project's `Assets`, wait for it to compile, then run **Tools > Cold Routes > Assemble Generated City**. Every OBJ is in final world-space coordinates (1 generator unit = 1 metre); no chunk offsets are used. `road_graph.json` and `buildings.json` carry the logical data gameplay systems (traffic, property) read from.

## Recent changes

- **V7.5** — coastline rewritten from a sine-sum boundary to the shape-grammar system described above.
- **V7.6** — arterial road placement rewritten to be destination-driven instead of evenly spaced.
- **V7.7** — added the `stub` cul-de-sac pattern for outer/suburban blocks.
- **V7.8** — added paved rear service alleys behind rear building rows in `composedBlock`.
