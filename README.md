# Haven: Scene-Aware Shelter Guidance in the Panic Window

## 🚨 Inspiration

In the first few minutes of a sudden disaster, people often panic and
choose instinctive shelter that can actually make things worse. Standing
near windows during a blast, running into a garage during a tornado, or
staying on low ground during a flood are common reactions that increase
risk instead of reducing it.

Most emergency apps today provide generic text instructions that assume
people already know their environment. We wanted to explore whether a
phone's camera could be used to actually *see* the surrounding space and
guide users toward safer shelter in real time based on the situation.

------------------------------------------------------------------------

## 📱 What We Built

**Haven** is a mobile app built with **Expo (React Native)** that
performs a short scan of the user's surroundings and provides
**scenario-aware shelter guidance**.

### User Flow:

1.  Select a disaster mode:
    -   Earthquake
    -   Flood
    -   Tornado
    -   Blast
    -   Fire
    -   Hazmat
2.  Optionally specify indoor/outdoor
3.  Perform a **360° scan** of the environment

### Output:

-   A single image from the scan with:
    -   ✅ Safe zones
    -   ⚠️ Danger zones
    -   ➡️ Exit paths
-   Overlays rendered using normalized bounding boxes
-   🎙️ Voice summary explaining the safest immediate action

------------------------------------------------------------------------

## ⚙️ How It Works

### Frame Capture

-   Uses device **gyroscope** (1 frame every \~15°)
-   Falls back to **time-based capture** if sensors are unavailable
-   Selects key frames for analysis

### Scenario-Aware Prompting

Each disaster mode encodes specific safety rules: - **Earthquake** →
Drop and cover under sturdy furniture\
- **Flood** → Move to higher stable surfaces\
- **Tornado** → Seek lowest interior shelter\
- **Blast** → Stay below window line

### Model Output

The vision model returns structured JSON: { "bestRefuge": { "label":
"GET HERE", "bbox": \[0.1, 0.4, 0.5, 0.9\], "confidence": 0.82 },
"dangerZones": \[ { "label": "AVOID", "bbox": \[0.6, 0.0, 1.0, 0.4\],
"confidence": 0.91 } \] }

### Bounding Box Format

-   Coordinates are **normalized (0--1)**
-   Format: \[x1, y1, x2, y2\]
    -   (x1, y1) = top-left\
    -   (x2, y2) = bottom-right\
-   Relative to the selected **anchor frame**

### Rendering Pipeline

-   Validate and clamp bounding boxes
-   Map to screen coordinates
-   Overlay zones on final image

------------------------------------------------------------------------

## 🧠 Challenges We Faced

### Overlay Alignment

Early overlays didn't match correctly. We solved this by selecting a
canonical frame and enforcing a consistent coordinate system.

### Sensor Inconsistency

Gyroscope behavior varied across devices, so we added a time-based
fallback for reliability.

### Noisy Model Output

Vision models often return extra formatting. We implemented robust
parsing and validation to safely extract structured data.

------------------------------------------------------------------------

## 📚 What We Learned

-   Disaster response is highly scenario-dependent
-   The same object can be safe or dangerous depending on context
-   Prompt engineering is critical for reliable spatial reasoning
-   Direct spatial outputs outperform detect-then-classify pipelines

------------------------------------------------------------------------

## 🌍 Why It Matters

In the panic window, a clear indication of the safest nearby refuge can
prevent dangerous instinctive decisions.

Haven democratizes emergency awareness by providing
**environment-specific guidance**, not generic instructions.

------------------------------------------------------------------------

## ⚡ How It's Different

Most emergency apps provide static checklists or text-only advice.

**Haven uses vision + multi-frame scanning to:** - Understand the user's
environment - Identify safe vs dangerous zones - Overlay guidance
directly onto real-world imagery

This makes guidance **situational, visual, and immediate**.
