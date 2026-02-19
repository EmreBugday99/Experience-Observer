# Experience Observer

The Experience Observer is a runtime analytics framework that provides data about a player's moment-to-moment experience and compares it against designer-authored intent curves. It serves as a centralized "heart rate monitor" for gameplay, exposing non-authoritative signals that other systems can use to nudge the experience back toward its intended feel.

## Core Philosophy

* **Observer, Not Controller**: The system measures what is happening and alerts when the experience drifts, but it never "grabs the controls" or directly modifies game state.
* **The Fun is External**: This system does not define "fun"; it exists solely to support the experience of consuming the fun defined by the game's designers.
* **Advisory Signals**: Downstream systems (consumers) are never required to react to the Observer's signals.

## Documentation

* **[Design Details](Design.md)**: Full breakdown of architecture layers, core metrics (Intensity, Challenge, Readability, Pacing), and contributor profiles.
* **[Known Issues & Risks](KnownIssues.md)**: Overview of architectural risks such as contextual blindness, over-correction, and the "Mastery Paradox".

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
