# WebGL Mesh-Swap Destruction System

A high-performance, procedural 3D physics destruction system built with Three.js and Cannon.js. Inspired by the Geometry Collection and Chaos physics architecture found in industrial game engines, this project implements a true "Mesh Swap" workflow to achieve massive, frame-stable structural collapses in the browser.

## Technical Highlights

* **Pre-Fracturing & Topology Alignment:** Utilizes a custom BSP (Binary Space Partitioning) convex hull slicing algorithm to recursively pre-fracture base geometries during initialization. It automatically rebuilds non-indexed Three.js point clouds into indexed, shared-vertex arrays, ensuring mathematically watertight `ConvexPolyhedron` generation for the Cannon.js collision pipeline.
* **Seamless Mesh Swap Architecture:** Eliminates the common "glass seam" and internal face transparency artifacts. The system renders a pristine, seamless, and lightweight `BoxGeometry` prior to impact. The complex fragmented geometries are cached entirely in RAM and are only injected into the render and physics pipelines at the exact frame of the stress trigger.
* **Kinetic Stress Triggers:** Physics impact is calculated dynamically. Structures do not simply fall apart upon touch; the collision event listener calculates the exact impact velocity along the surface normal (`getImpactVelocityAlongNormal()`). Fracture and mesh displacement only occur when the kinetic energy surpasses a defined structural integrity threshold.
* **Collision Layer Masking (New):** Assigns fragmented chunks to a dedicated collision layer (`COL_SHARD`). Shards collide with the ground, projectiles, and intact walls, but strictly ignore collisions with other shards. This drastically reduces the physics engine's computational complexity from O(N²) to O(N) during massive explosions.
* **Material Instance Caching (New):** Implements a global material pool to prevent runtime instantiation. Reusing core materials across hundreds of dynamic shards significantly minimizes GPU draw calls and eliminates instantiation stutters.

## 60 FPS Performance Tuning

Prevents the physics "Spiral of Death" during massive simultaneous multi-body awakenings using a combination of optimizations:

* Replaced default algorithms with O(N) `SAPBroadphase` (Sweep and Prune) for multi-body collision filtering.
* Implemented an adaptive `fixedTimeStep` with a strict `maxSubSteps` limit to provide calculation circuit breakers during frame drops.
* Applied aggressive kinematic sleep thresholds (`sleepSpeedLimit` and `sleepTimeLimit`) to freeze micro-jitters of resting shards instantly.

## Tech Stack

* **Rendering:** Three.js (WebGL)
* **Physics:** Cannon.js
* **UI/Styling:** Tailwind CSS

## How it Works

1. Click the **FIRE!** button to spawn a high-density kinetic projectile (`mass: 1500`).
2. The projectile is fired with a severe initial linear velocity (`z = -300`) towards the procedural structure.
3. Upon impact, the system evaluates the force, destroys the static mesh, instantly swaps in the pre-calculated convex chunks, and unleashes the physics simulation.
