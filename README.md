# 🧩 Match 3D Mania: Production-Ready Hybrid-Casual Framework by Emir Bekar

![Unity Version](https://img.shields.io/badge/Unity-6%20LTS-black?style=for-the-badge&logo=unity)
![C#](https://img.shields.io/badge/Language-C%23-blue?style=for-the-badge&logo=c-sharp)
![Platform](https://img.shields.io/badge/Platform-iOS%20%7C%20Android-lightgrey?style=for-the-badge)
![Architecture](https://img.shields.io/badge/Architecture-Event--Driven%20%7C%20SOLID-success?style=for-the-badge)
![Render Pipeline](https://img.shields.io/badge/Render-URP%20Optimized-critical?style=for-the-badge)

> **A comprehensive technical showcase of scalable mobile game development.** 
> Match 3D Mania is not just a gameplay prototype; it is a fully integrated hybrid-casual foundation. Designed from scratch, it demonstrates advanced Z-axis physics optimization, zero-allocation memory management, and industry-standard F2P (Free-to-Play) retention systems.

---

## 📑 Table of Contents
1. [Visual Showcase](#-visual-showcase)
2. [3D Asset Pipeline & Rendering](#-3d-asset-pipeline--rendering)
3. [The Core Gameplay Loop](#-the-core-gameplay-loop)
4. [Deep Dive: Architecture & Optimization](#-deep-dive-architecture--optimization)
5. [LiveOps & Player Retention Systems](#-liveops--player-retention-systems)
6. [Highlighted Code: Object Pool](#-highlighted-code-object-pool)


---

## 📱 Visual Showcase

<p align="center">
  <img src="Images/mainmenu.png" alt="Match 3D Mania Ana Menü Ekranı" width="100" height="400">
</p>
<p align="center"><i>Main menu screen</i></p>

### The Meta-Game Interface
Commercial success relies heavily on UI/UX and meta-game loops. The following systems were implemented to drive Day-1 to Day-7 retention:

| Daily Rewards | Tournament System | Lucky Spin |
| :---: | :---: | :---: |
| <img src="Images/daily.png" width="250"><br><b>Progressive Daily Login</b> | <img src="Images/shop.png" width="250"><br><b>Shop</b> | <img src="Images/Lucky Spin.png" width="250"><br><b>Economy Sinks & Boosters</b> |

---

## 🎨 3D Asset Pipeline & Rendering

I handled the full technical art pipeline to ensure the visuals fit strict mobile constraints without sacrificing aesthetics.

<p align="center">
  <img src="Images/3D Objects.png" alt="3D Low-Poly Asset Showcase" width="900">
</p>
<p align="center"><i>All 3D models were designed from scratch in Blender with a low-poly aesthetic.</i></p>

* **Draw Call Reduction:** All 3D items share a single unified texture atlas and a single material. This allows Unity's URP (Universal Render Pipeline) to batch the entire pile of objects into just a few draw calls.
* **Geometry Optimization:** Strict polygon budgets were maintained during the Blender modeling phase, ensuring smooth rendering on devices stretching back to the iPhone 8 era.

---

## 🎯 The Core Gameplay Loop

The fundamental mechanic requires players to find and match specific 3D objects from a massive, physics-enabled pile within a strict time limit. 
* **Tactile Feedback:** Utilizes Unity's physics engine for realistic object collisions, drag-and-drop mechanics, and satisfying clearing animations.
* **Dynamic Goals:** Goal conditions are fully modular, allowing designers to set multi-stage item collection targets.
* **Combo System:** A time-sensitive multiplier system that rewards fast playstyles, directly tied to the end-of-level score and soft currency generation.

---

## 🛠 Deep Dive: Architecture & Optimization

Developing a physics-heavy 3D game for lower-end Android and iOS devices presents significant CPU and memory challenges. Here is how I solved them:

### 1. Zero-Allocation Memory Management
Mobile device CPUs throttle heavily during Garbage Collection (GC) spikes. To combat this:
* **Dictionary-Backed Object Pooling:** All interactable items, visual FX, and floating UI texts (`FloatingText.cs`) are pooled. There are absolutely **zero `Instantiate()` or `Destroy()` calls** once the gameplay scene initializes.
* **String Allocation Avoidance:** UI updates avoid string concatenation in `Update()` loops. Score and timer displays use cached string builders or integer-based text updates.

### 2. Physics & CPU Throttling
* **Collision Matrices:** Custom layer collision matrices ensure that objects only calculate physics against the floor and each other, ignoring UI and out-of-bounds trigger zones.
* **Rigidbody Sleep Management:** Implemented logic to aggressively force rigidbodies to sleep when velocity drops below a threshold, freeing up CPU cycles for the main thread.

### 3. Faux-3D UI Rendering Strategy
* **Avoiding RenderTextures:** Instead of using expensive camera-to-render-texture setups for 3D UI elements (which break batching), I developed `GoalDisplay.cs`. This script strips physics/mesh colliders from standard 3D prefabs, scales them down, and renders them directly within the UI Canvas space using custom Z-depth sorting.

---

## 📈 LiveOps & Player Retention Systems

A game must retain players. I built several data-driven meta-systems to ensure a commercially viable product:

* **Adaptive Difficulty (Anti-Churn):** The game tracks consecutive player losses (`Loss Streak`). If a player struggles, the game silently intervenes by increasing the time limit and reducing the total number of "junk" objects in the next attempt.
* **Centralized Data Economy:** Implemented `LevelConfig.cs` (ScriptableObject). The entire game economy—booster costs, coin rewards, spawn distributions, and level timings—can be balanced from a single file without modifying code.
* **Unified Virtual Currency:** A fully functional soft currency loop where players earn coins through matches and spend them on in-game tactical boosters (Magnet, Freeze Timer, Undo Move, Shuffle, Extra Slot).
