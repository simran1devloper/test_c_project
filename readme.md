# Feature Extractors

This directory contains the components responsible for converting raw input (audio metadata + STT transcript) into structured features for the `InterruptionManager`.

## Components

### 1. Overlap Detector (`overlap_detector.py`)
- **Input:** User start timestamp, Agent start timestamp.
- **Logic:** Checks if the user started speaking *while* the agent was currently speaking.
- **Output:** `Boolean` (True if overlap occurred).
- **Why?** Interruption requires overlap. If the agent wasn't speaking, it's just a normal turn, not an interrupt.

### 2. Filler Detector (`filler_detector.py`)
- **Input:** Transcript text.
- **Logic:** Uses Regex to detect common backchannels (e.g., "yeah", "uh-huh", "hmm", "ok").
- **Output:** `Boolean` or Score.
- **Why?** We want to **IGNORE** backchannels so the agent keeps talking, rather than stopping for every "uh-huh".

### 3. Semantic Intent Classifier (`semantic_intent.py`)
- **Input:** Transcript text.
- **Logic:**
    1.  **Keyword Search:** Fast check for "stop", "wait", "hold on".
    2.  **LLM Fallback:** If ambiguous, optionally asks an LLM "Is this an interrupt?".
- **Output:** `INTERRUPT`, `IGNORE`, or `NORMAL`.

### 4. Noise Classifier (`noise_classifier.py`)
- **Input:** Raw audio frames (Energy) or Transcript (Text).
- **Logic:** Detects non-speech events (coughing, background noise).
- **Output:** `clean` or `noise`.

## Usage in Pipeline
The `InterruptionManager` calls these extractors in parallel to build a `features` dictionary, which is then passed to the `ModeSelector`.
