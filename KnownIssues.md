# Known Issues: Experience Observer Framework

This document outlines potential architectural risks and edge cases identified during the design phase. These issues represent areas where the "Monitor, not Autopilot" philosophy may encounter friction with complex gameplay systems.

### 1. Contextual Blindness
* **Issue:** The system may report high **Intensity** or **Readability Load** based on nearby active contributors (emitters) even if the player is not actually perceiving them (the player is hiding behind a wall or looking in the opposite direction).
* **Impact:** Consumer systems (like dynamic music or VFX) might prematurely reduce effects, thinking the player is overwhelmed when they are actually in a "safe" or "bored" state.
* **Risk:** A disconnect between the *Observed* state and the player’s *Subjective* experience.

### 2. Multi-Consumer Over-Correction
* **Issue:** Since the Observer is non-authoritative and does not coordinate reactions, multiple systems may react to the same "High Intensity" signal simultaneously.
* **Impact:** If the Audio system drops layers AND the VFX system cuts particle density at the exact same moment to address the same spike, the game may suddenly feel "hollow."
* **Constraint:** The framework currently lacks a "Priority Dispatcher" to negotiate which consumer should handle a specific error signal.

### 3. Metric Coupling and Contradiction
* **Issue:** Core metrics like **Challenge** and **Readability Load** are treated as independent, but in practice, they are deeply correlated (high visual clutter makes the game harder).
* **Impact:** Attempting to fix a "Low Challenge" signal by spawning more enemies may inadvertently push the "Readability Load" past its maximum threshold.
* **Risk:** The system may create feedback loops where solving one drift error creates a critical failure in another.

### 4. The Mastery Paradox
* **Issue:** High-skill players often intentionally create "Intensity Peaks" (grouping enemies for a massive chain explosion).
* **Impact:** The Observer identifies these peaks as "Drift" from the designer's intended pacing and generates signals to dampen the experience.
* **Risk:** Consumer systems may effectively "punish" mastery by smoothing out the highs that the player worked hard to create.

### 5. Signal Oscillation
* **Issue:** Rapid changes in game state (especially from *Impulse* contributors) can cause the Interpretation Layer to fluctuate.
* **Impact:** If Consumer systems do not implement their own internal cooldowns or smoothing logic, game elements may "stutter" as they toggle on and off to stay within the intended range.
* **Constraint:** The framework relies on individual Consumers to manage their own reaction frequency.

### 6. Information Flow Latency
* **Issue:** Because the system applies smoothing and weight calculations over time to aggregate contributions, the "Observed Experience State" is inherently lagging behind the real-time simulation.
* **Impact:** The system is explicitly not a "frame-perfect" reactive system. It is unsuitable for mechanics requiring microsecond precision.
