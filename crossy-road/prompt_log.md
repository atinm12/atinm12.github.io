# Prompt Log

This file logs the human prompts used to build the Crossy Road game in this folder
with AI assistance (Anthropic's Claude — Claude Sonnet 4.5 — via the Kiro IDE). The
AI generated or edited the game code in response to the prompts below.

Prompts are listed in chronological order, reproduced verbatim. One prompt included a
reference image, noted where applicable.

---

### Prompt 1
```
Build a Crossy Road clone in 2.5D using Three.js. Use an isometric-style camera (angled top-down, orthographic projection) so it looks like the original game instead of full 3D. The character should be a simple blocky voxel-style avatar that hops forward, backward, left, and right in grid steps using arrow keys or WASD. Generate the world procedurally as the player moves forward, mixing grass lanes that are safe, road lanes with cars moving left or right at different speeds, and river lanes with floating logs the player needs to ride so they don't drown. Keep the camera following the player smoothly while staying at that fixed isometric angle. Track the score based on how far forward the player has gotten, and show it on screen. If the player gets hit by a car or falls in the water, end the game and show a game over screen with the score and a restart button. Keep the visuals simple and colorful like the original game, low-poly flat-shaded blocks, no textures needed. Add basic lighting so there's some shadow, and a little hop animation when the character moves. Build it as a single HTML file using Three.js from a CDN, and get the core movement and collision working first before adding extra polish.
```

### Prompt 2
```
In my Crossy Road clone, pressing the up arrow moves the character southwest on screen, but I want it to move northeast instead. The forward direction (positive Z) is tied to scoring and lane generation, so don't touch the key bindings or flip the Z direction, since that'll break those systems. Instead, fix this by repositioning the isometric camera, in both the init and restartGame functions, so that moving in positive Z reads visually as northeast on screen instead of southwest. Keep everything else about the camera behavior, like the smooth follow and fog, working the same way.
```

### Prompt 3
```
Pressing up now moves the character southeast, but I need northeast. The camera position was only partially adjusted last time — check camera.position.set() in both init and restartGame, and make sure both the X and Z values are negated from the original (15, 15, 15), not just one of them, so the camera ends up on the opposite corner. That full diagonal flip should make forward (positive Z) read as northeast on screen. Don't change the key bindings, scoring, or lane generation logic.
```

### Prompt 4
```
Right now the road obstacles are just plain colored boxes, not car-shaped. Update the createCars function so each car looks like an actual low-poly car instead of a single box: combine a few box geometries into a group, like a slightly taller and shorter box for the main body and a smaller box on top for the cabin/roof, so it reads as a car silhouette from the isometric angle. Keep the random color variation between cars, and keep the car's collision logic and movement/wraparound behavior exactly the same, since that's working correctly.
```

### Prompt 5
```
Also add trees scattered sparsely on grass lanes. In generateLane, when the lane type is grass, randomly decide with a low probability, around 10 to 15 percent, whether to spawn a tree somewhere on that lane at a random x position, avoiding the player's starting lanes near z equals 0. Build each tree out of a simple box for the trunk topped with a box or cone for the foliage, similar low-poly style to everything else. Trees should be purely decorative, so make sure they don't interfere with collision detection or movement, and store them in the lane's meshes array so they get cleaned up properly when the lane goes out of view.
```

### Prompt 6
```
Right now checkCollision only runs once, right when the player finishes a hop, so if a car drives into the player's tile while the player is sitting still between moves, nothing catches it. Fix this by calling checkCollision every frame while the game is active, not just at the end of a move, so a car moving into the player's position gets detected regardless of whether the player is mid-hop or standing still. Make sure this doesn't break the river/log logic, which relies on checkCollision to set ridingLog when the player is stationary on a river lane.
```

### Prompt 7
```
Triple the frequency of the trees on grass lanes — take whatever probability value you used for spawning a tree in generateLane and multiply it by 3, so trees show up more often while still leaving most grass lanes without one.
```

### Prompt 8
```
Right now trees are purely decorative and don't block movement, so the player can hop right through them. Add collision detection so the player can't move onto a tile occupied by a tree. When a tree is spawned in generateLane, store its position (x and z) somewhere accessible, like a treePositions array or a property on the lane object. Then in startMove, before committing to the move, check whether the destination tile matches a tree's position within a small tolerance, and if so, cancel the move instead of letting the player pass through, similar to how the side movement limit already caps player.position.x. Make sure this only blocks movement onto the tree's own tile and doesn't affect movement on lanes without trees.
```

### Prompt 9
```
Since checkCollision now runs every frame instead of just at the end of a move, it's catching the player mid-hop, while position.z is still interpolating between lanes, and incorrectly failing the river check because the player isn't aligned with any log yet during that transition. Fix this by only running the river/log check portion of checkCollision when the player is not mid-move, meaning isMoving is false, so it evaluates log position only once the player has actually landed and settled into a lane. Keep the car collision check running every frame regardless of isMoving, since that part needs to catch cars driving into a stationary player, but make sure the river logic doesn't trigger game over while the hop animation is still in progress.
```

### Prompt 10
```
The game world only fills a small portion of the screen, with a lot of empty sky around it. This is because the camera's orthographic viewSize is set to 20, meaning the visible area spans 40 units across, but the player's horizontal movement is clamped to between -8 and 8, and the lane ground meshes are only 20 units wide. So the actual playable area is much smaller than what the camera is framing. Fix this by reducing the camera's viewSize in both init and onWindowResize so the visible view more closely matches the playable width, filling most of the screen with the road and grass instead of showing so much empty background. Adjust it until the lanes stretch edge to edge or close to it, and make sure the aspect ratio scaling and resize logic still work correctly.
```

### Prompt 11
```
The playing field fills more of the screen now, but there's still visible sky in the corners. Reduce the camera's viewSize a bit further, in both init and onWindowResize, so the lanes extend closer to the edges of the screen. Also widen the lane ground geometry itself, currently 20 units in generateLane, along with the side movement clamp in startMove and the car and log wraparound bounds in updateObstacles, so the terrain actually covers more horizontal space instead of just zooming into the existing narrow strip and cutting off the view sooner.
```

### Prompt 12 _(included a reference image)_
```
Replace the player's green box with the crossy road style chicken that i pasted in chat. Build it out of a group of simple box geometries instead of a single cube: a white box for the main body, a smaller white box for the head, a small pink or magenta box on top of the head for the comb, a small orange box on the front for the beak, an orange box on the side for a wing, and two thin orange boxes underneath for the legs and feet. Group all these pieces together into a single object so they move and hop as one unit. Update createPlayer to build this chicken group instead of the single box, and make sure player.position still refers to the group so all the existing movement, hop animation, and collision logic keep working exactly as before without needing changes elsewhere.
```

### Prompt 13
```
The camera's position was changed to fix the forward-direction mapping, but the left and right arrow keys still use the original X sign convention, so pressing left now moves the chicken right on screen, and right moves it left. Swap the dx values in onKeyDown for arrowleft/a and arrowright/d, so arrowleft and a set dx to positive GRID_SIZE and arrowright and d set dx to negative GRID_SIZE, matching the new camera orientation. Don't change the up/down key bindings, the side movement clamp in startMove, or any collision logic, since those already work correctly and use absolute value comparisons that aren't affected by the sign flip.
```

### Prompt 14
```
There are still gaps of empty space appearing in the terrain as the player moves forward. In generateLanesAhead, startZ is already computed as a world-space Z value since furthestZ and the floor division already account for LANE_WIDTH, but the loop that follows calls generateLane(z * LANE_WIDTH), multiplying by LANE_WIDTH a second time. Fix the loop so it iterates over world-space Z positions directly in steps of LANE_WIDTH, from startZ minus a small buffer to startZ plus a small buffer, and calls generateLane(z) without any extra multiplication, so newly spawned lanes connect seamlessly to existing terrain with no gaps.
```
