# 🧩 3D Match-3 Mobile Game by Emir Bekar

![Unity Version](https://img.shields.io/badge/Unity-6%20LTS-black?style=flat-square&logo=unity)
![C#](https://img.shields.io/badge/Language-C%23-blue?style=flat-square&logo=c-sharp)
![Platform](https://img.shields.io/badge/Platform-iOS%20%7C%20Android-lightgrey?style=flat-square)
![Optimization](https://img.shields.io/badge/Optimization-Object%20Pooling-success?style=flat-square)

A highly optimized, production-ready 3D Tile Matching / Match-3 mobile game prototype. Built from the ground up with a strong emphasis on **mobile performance, scalable architecture, and player retention (LiveOps) mechanics**. 

This project demonstrates not just core gameplay, but the underlying systems required for a commercially viable hybrid-casual mobile game.

## 🎮 Gameplay & UI Previews

<p align="center">
  <img src="[BURAYA_OYUN_GIFININ_LINKINI_KOY]" alt="Gameplay GIF" width="400">
</p>

| Daily Rewards | Tournament System | Lucky Spin |
| :---: | :---: | :---: |
| <img src="[BURAYA_DAILY_REWARD_RESMI]" width="250"> | <img src="[BURAYA_TURNUVA_RESMI]" width="250"> | <img src="[BURAYA_SPIN_RESMI]" width="250"> |

---

## 🎨 Blender 3D Art Pipeline

<p align="center">
  <img src="Images/3D Objects.png" alt="3D Low-Poly Asset Showcase" width="900">
</p>
<p align="center"><i>All 3D models used in this project were designed in Blender with a low-poly aesthetic, highly optimized for mobile rendering.</i></p>

---

## 🚀 Key Technical Features

### 1. Advanced Performance & Optimization
* **Custom Object Pooling (`ItemPool.cs`):** Eliminates CPU spikes and Garbage Collection (GC) overhead by dynamically recycling 3D match items and floating UI texts (`FloatingText.cs`). Zero `Instantiate/Destroy` calls during active gameplay.
* **Cheap UI Rendering (`GoalDisplay.cs`):** Instead of using expensive Render Textures for 3D UI elements, the game strips physics and components from actual prefabs and renders them directly on the UI layer. Bypasses URP batching limits entirely.
* **Device Agnostic UI:** Integrated `SafeAreaFitter.cs` automatically adapts the RectTransforms to accommodate notches and rounded corners on modern iOS and Android devices.

### 2. Data-Driven Level Design & Adaptive Difficulty
* **Scriptable Object Architecture (`LevelConfig.cs`):** All game economy, booster costs, and level definitions are centralized in a single, easily tweakable asset.
* **Procedural Generation:** Once handcrafted levels are exhausted, the game seamlessly transitions into a math-driven procedural level generator, creating infinite replayability.
* **Adaptive Difficulty:** Implemented an industry-standard retention trick (Loss Streak tracking). If a player fails multiple times, the game silently increases the timer and reduces obstacles to prevent churn.

### 3. Robust LiveOps & Meta-Game Systems
* **State Machine Flow (`MatchGameManager.cs`):** Clean separation of game states (Menu, Playing, Paused, LevelComplete, GameOver).
* **Retention Mechanics (`MetaProgress.cs`):** Fully integrated daily login rewards, competitive daily tournaments, win-streak bonuses (unlocking extra tray slots), and a luck-based spin wheel.
* **Booster Economy:** Features multiple interactable boosters (Magnet, Freeze, Undo, Shuffle, Extra Slot) tied into a unified virtual currency system.

---

## 💻 Architecture Highlight: The Object Pool

To handle massive waves of 3D objects dropping into the arena simultaneously without frame drops, I built a dictionary-backed stack pool. Here is a snippet demonstrating the fetch logic:

```csharp
// Snippet from ItemPool.cs
public static MatchItem Get(GameObject prefab, int poolKey, Vector3 pos, Quaternion rot)
{
    if (pools.TryGetValue(poolKey, out var stack))
    {
        while (stack.Count > 0)
        {
            MatchItem pooled = stack.Pop();
            if (pooled == null) continue; // Safe-check for scene unloads
            pooled.transform.SetParent(null);
            pooled.gameObject.SetActive(true);
            pooled.ResetForSpawn(pos, rot); // Resets state, clears obstacles
            return pooled;
        }
    }

    // Fallback if pool is empty
    GameObject go = Object.Instantiate(prefab, pos, rot);
    MatchItem item = go.GetComponent<MatchItem>();
    item.PoolKey = poolKey;
    return item;
}
