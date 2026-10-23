# Creation and Destruction | AppADay 169

Build a small 3D city on a snap grid from houses, towers, skyscrapers, trees, roads, cars, and a bridge, then destroy it with real physics. Bombs, missiles, a laser, meteors, tornadoes, hurricanes, tsunamis, earthquakes, and spreading fire crumble it block by block. Slow motion, one tap rebuild, and a photo gallery.

**Live:** https://augustineiacopelli.github.io/appaday-169-creation-and-destruction/
**Portfolio:** https://augustineiacopelli.github.io/appaday/

Category: I (Interactive). Claude API: No. Home screen name: Crumble City.

## How to use

1. The first visit opens on a Random City so there is something to wreck right away. Drag to orbit, pinch or scroll to zoom, and drag with two fingers to pan.
2. In Build, pick a piece from the palette and tap a grid cell to place it. The ghost turns red when the spot is out of bounds, already taken, or over the block budget. Skyscrapers take a 2 by 2 footprint, bridges span three cells and may cross a road in the middle cell, cars go on roads, and nothing else may sit on a road.
3. Rotate turns the next piece a quarter turn. Undo removes the last piece you placed. Clear empties the grid after a confirm sheet. Random City lays out a fresh town with a road grid, skyscrapers in the center, towers in the middle ring, houses and trees at the edges, cars on the roads, and one bridge where it fits. The pill at the top left shows blocks used against the budget.
4. Switch to Destroy. The city you built is saved as a snapshot, and the Rebuild button (or returning to Build) restores it exactly.
5. Pick a tool, set Power from 1 to 10, and follow the one line hint under the slider.
   Bomb: tap to plant a blinking bomb that blows after a short fuse.
   Missile: tap a target and a missile arcs in from the sky with a smoke trail.
   Laser: press and hold, then sweep; the beam cuts the block under your finger.
   Meteor: tap a target; a blazing meteor falls, blasts a crater, and starts fires.
   Earthquake: tap anywhere; power sets how long and how hard the ground shakes.
   Fire: tap a building; fire spreads to neighbors, chars blocks, weakens supports, and burns them to ash.
   Tornado: tap to drop a funnel that lasts about 12 seconds; drag to steer it.
   Hurricane: tap to toggle the storm (it darkens the sky and brings rain); drag across the city to aim the wind.
   Tsunami: tap near an edge and a wave rolls in from that side, putting out every fire it passes in a puff of steam, then leaves water that recedes. Doused blocks stay too wet to catch fire again for about 10 seconds.
6. The floating buttons at the top right are Pause (rendering and orbit keep running), Slow Motion (quarter speed), Reset View, Rebuild, and Photo.
7. Photo saves the scene with a caption band and the date to a gallery on this device. The gallery button in the header shows thumbnails; open one to Share, Download, or Delete it.

The gear button holds Sound, Vibration, Shadows, Weather, and Quality (Low, Medium, High). Weather sets the scene to Clear, Overcast, Rain, Sunset, or Night, and it changes the sky, fog, light, rain, and rain sound with a smooth fade. While a tornado or hurricane is running, the weather always shifts to storm overcast with rain, then fades back to your chosen scenario once the storm passes. Quality sets the pixel ratio, shadow map size, solver iterations, the cap on moving blocks, the block budget (2500, 3200, 4000), and the particle pool. The layout and settings are saved in this browser only (keys appaday169-layout and appaday169-settings), and photos are kept in IndexedDB (database appaday169, store photos).

## Tech

One self contained index.html with an import map pinning three 0.186.1 (with OrbitControls from three/addons) and cannon-es 0.20.0 from cdn.jsdelivr.net. The module script checks for WebGL and loads the libraries through a guarded dynamic import, showing a friendly card if either fails.

Every block is one instance in one InstancedMesh per canvas drawn texture (three facade variants, skyscraper glass, roof shingles, road, bark, leaves, bridge deck, pillar concrete, and car), with per block instance color carrying paint and charring. Roofs and canopies render as pyramid and icosahedron instances over box bodies. A typed array registry holds each block's state (frozen, active, dead), transform, health, burn level, and the supporters and dependents built from the piece specs.

## Physics notes

Blocks start as static bodies. A tool activates a block by switching it to dynamic, and activation or death queues its dependents for a support check, so a floor falls when the floor under it is destroyed, burned past 0.7, or has moved. Moving blocks that settle (a sleep event, or slow for 1.5 seconds) refreeze in place, and the oldest settled blocks refreeze first when the active cap is reached (600, 700, 800 by quality). Blocks that leave the grid retire as dust.

Only frozen blocks near moving debris are kept in the physics world; the rest join when something comes close. That keeps a quiet city nearly free and a big collapse bounded. Physics steps at a fixed 1/60 s with up to 3 substeps, and drops to 1 substep when a frame's simulation runs past about 14 ms, so a heavy collapse slows slightly instead of stuttering. The ground is a static box that becomes kinematic during an earthquake.

Effects share one points pool for dust, smoke, sparks, embers, foam, and rain, plus a small instanced pool of ballistic debris chips. Sound is synthesized with Web Audio (boom, rumble bed, laser buzz, fire crackle, wind, whoosh, and throttled collision clicks) and starts on the first tap.

Add ?debug=1 to the URL to show FPS, active and world body counts, burning blocks, and simulation and render milliseconds.
