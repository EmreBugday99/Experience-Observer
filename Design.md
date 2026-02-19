# Experience Observer

## Purpose & Philosophy

### Purpose
This system provides runtime analytics about the player's moment-to-moment experience and compares it against designer-authored intent curves. It exposes structured signals that other gameplay systems may use to nudge the experience back toward intent.

### The System
* Does not define fun
* Does not directly change gameplay
* Does not enforce outcomes

> **Note:** It is an observer + signal generator, not a controller.

### Core Principle
> The fun exists outside this system.
> This system exists to support the experience of consuming that fun.

### What This System Is Not
* A difficulty manager that auto-balances encounters
* A replacement for encounter design
* A single "dopamine meter"
* A frame-perfect reactive system

---

## Design Goals
* Maintain fidelity to designer intent across variable player skill and playstyle
* Provide clear, debuggable metrics instead of gut-feel iteration
* Support dynamic pacing and challenge without invalidating mastery
* Prevent over-intensification (visual clutter, noise, performance issues)
* Be modular, optional, and non-authoritative

---

## High Level Architecture
The system is structured as a one-way flow of information:

1.  Game systems report what is happening
2.  The Experience Observer analyzes it over time
3.  The Observer compares it to designer intent
4.  The Observer exposes signals for optional use

**No system downstream is required to react.**

---

## Architecture Layers

### 1. Runtime Game Systems (Source of Truth)
These are the systems that already exist in the game. These systems do not depend on the Experience Observer.

* **Systems:** Combat systems, Enemy AI, VFX & particle systems, Audio systems, Camera & feedback systems, Level scripting.
* **Events/State Changes:** They only emit events or state changes such as:
    * Enemy spawned / died
    * Weapon fired
    * Damage dealt
    * Explosion triggered
    * Particle burst played
    * Hazard activated

### 2. Experience Contributors
Experience Contributors are lightweight components or data profiles attached to runtime elements. Their role is to describe experiential impact, not behavior.

**Each contributor defines:**
* Which experience metrics it affects (Intensity, Challenge, Readability, etc.)
* How it affects them (impulse vs sustain)
* Context modifiers (visibility, distance, relevance)

**Examples:**
* An enemy defines ongoing Challenge and Intensity while alive
* An explosion defines a short Intensity impulse
* A particle system defines Readability Load while emitting

*Contributors do not know about intent, curves, or reactions.*

### 3. Experience Observer
The Experience Observer is a centralized, read-only analysis system.

**Responsibilities:**
* Collect contributions from all active contributors
* Aggregate contributions into metrics
* Apply smoothing over time
* Normalize values into stable ranges

**The Observer Produces:**
* Current observed experience state
* It never modifies game state
* It cannot spawn, remove, or tune anything

### 4. Designer Intent Definition
Designer intent is defined independently from runtime behavior.

* **Intent represents:** "What the experience is supposed to feel like here".
* It does not describe how to achieve that feeling.

### 5. Comparison & Signal Generation (Interpretation Layer)
The Observer compares **Observed experience** VS **Desired experience**. From this comparison it derives signals such as:
* Error (under / over target)
* Direction (rising / falling)
* Risk (approaching overload)

*These signals are informative only and are non-authoritative.*

### 6. Optional Consumer Systems
Other systems may query or subscribe to Observer signals.

**Examples:**
* Encounter pacing logic
* Enemy spawner heuristics
* Audio layering
* VFX density management
* Camera feedback tuning

**Key Constraints:**
* Consumers decide if and how to respond
* No consumer is required to respond
* Multiple consumers should not blindly react at once
* The Observer never calls consumers

---

## Mental Model (for Designers)

> **Think of the system as a heart rate monitor, not an autopilot.**

* It measures what's happening
* It compares it to the pre-defined plan
* It alerts when the experience drifts
* It never grabs the controls

---

## Core Metrics
The system tracks orthogonal experience dimensions. These are proxies, not fun.

### Example Metrics
* **Intensity:** Audio-visual spectacle delivered to the player
* **Challenge / Pressure:** Incoming threat and decision pressure
* **Readability Load:** Visual + cognitive clutter
* **Pacing:** Rate of change / volatility of experience

---

## Experience Contributors

### Contributor Definition
Any runtime element that meaningfully affects player experience registers as an Experience Contributor.

**Examples:**
* Enemies
* Weapons
* SFX
* VFX
* Particle systems
* Environmental hazards
* Camera effects
* UI feedback

### Contributor Profile
Each contributor defines a profile.

```yaml
# Example Data Structure
Intensity:
  Impulse: 0.4    # Short burst (explosion, hit, kill)
  Sustain: 0.1    # Ongoing presence (enemy alive)
Challenge:
  Sustain: 0.3
Readability Load:
  Sustain: 0.2
Context Modifiers:
  Offscreen Multiplier: 0.25
  Distance Falloff: Curve
```
### Key Distinction
* **Impulse:** Short burst (explosion, hit, kill)
* **Sustain:** Ongoing presence (enemy alive, hazard active)

---

## Consumer Guidelines
* **Treat signals as advisory:** Systems should never be forced to react.
* **Prefer illusionary or cosmetic adjustments first:** Change the feel (audio/VFX) before changing the simulation (stats/logic).
* **Apply cooldowns and caps:** Prevent systems from oscillating or reacting too frequently.
* **Avoid compensating in multiple systems simultaneously:** Prevents over-correction (e.g., don't spawn fewer enemies AND make them weaker at the same time).

