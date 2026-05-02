# Games & Graphics

Interactive entertainment, real-time 3D, simulations, and rendering systems. Performance constraints (frame budget) and asset pipelines distinguish this category from web/mobile applications.

## When to look here

- Frame budget matters (16.6ms for 60fps, 8.3ms for 120fps)
- Real-time 3D rendering is core to the product
- Asset pipeline (art, audio, animation) is a major part of production

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| Cross-platform Game (Unity / Unreal / Godot) (TODO) | Adopt | Most commercial game projects |
| Web-based Game (WebGL/WebGPU) (TODO) | Trial | Browser-distributable games, marketing experiences |
| Multiplayer Game Backend (TODO) | Trial | Real-time multiplayer with authoritative server |
| Scientific / Engineering Simulation (TODO) | Adopt | Physics, fluid dynamics, training simulators |
| Data Visualisation / 3D Viewer (TODO) | Adopt | CAD viewers, geospatial, medical imaging |

## Default tech stack

- **Engines:** Unity (C#), Unreal (C++/Blueprints), Godot (GDScript/C#), Bevy (Rust, assess)
- **Web 3D:** Three.js, Babylon.js, PlayCanvas
- **Multiplayer:** Photon, Mirror (Unity), dedicated servers via Agones
- **Backend services:** Standard backend stack, but with stricter latency budgets

## LLM-development fit

Mixed. LLMs handle gameplay scripting, UI, and tooling well, but struggle with performance-critical rendering code, complex state machines, and shader debugging.
