IMPORTANT - READ FIRST

Before modifying ANY code:

1. Read ALL project documentation.
2. Read ALL .md files in the repository.
3. Read architecture documents.
4. Read implementation notes.
5. Read tracking notes.
6. Read OCR notes.
7. Read ARCore notes.
8. Read translation notes.

Build a complete understanding of the existing implementation first.

DO NOT assume the architecture.

DO NOT rewrite working systems.

DO NOT duplicate existing functionality.

Create a report summarizing:

- Current OCR pipeline
- Current ARCore pipeline
- Current tracking pipeline
- Current translation pipeline
- Current rendering pipeline
- Existing anchors
- Existing pose tracking
- Existing projection system

Only after understanding the existing architecture should implementation begin.

---

PROJECT GOAL

Build a flagship-grade AR Translation system.

The goal is NOT:

OCR
↓
Translate
↓
Draw text on screen

The goal IS:

OCR
↓
Identify text in world
↓
Attach to AR world
↓
Track in 3D space
↓
Replace text naturally
↓
Remain locked during movement

The translation should appear attached to the real object.

---

REQUIRED FLOWCHART

CAMERAX LIVE FEED
↓
ML KIT OCR
↓
DETECT ALL TEXT BLOCKS
↓
FILTER INVALID TEXT

- tiny text
- edge touching text
- partial text
- low quality text
  ↓
  SELECT LARGEST VALID TEXT BOX
  ↓
  COMPUTE
- center
- width
- height
- orientation
  ↓
  LOCK TARGET
  ↓
  ARCORE HIT TEST
  ↓
  PLANE / DEPTH POINT / FEATURE POINT
  ↓
  CREATE ANCHOR
  ↓
  STORE
- anchor
- pose
- quaternion
- dimensions
- original text
  ↓
  STOP USING OCR COORDINATES
  ↓
  TRACK USING ARCORE
  ↓
  ANCHOR POSE UPDATE
  ↓
  CAMERA POSE UPDATE
  ↓
  QUATERNION ORIENTATION UPDATE
  ↓
  WORLD SPACE TARGET
  ↓
  PROJECT WORLD → SCREEN
  ↓
  BUILD TRACKED TEXT REGION
  ↓
  LANGUAGE IDENTIFICATION
  ↓
  TRANSLATION
  ↓
  CACHE RESULT
  ↓
  TEXT FITTING
  ↓
  TEXT REPLACEMENT
  ↓
  30-60 FPS RENDERING
  ↓
  STABLE AR TRANSLATION

---

CURRENT MAJOR ISSUE

The detected text boundary box appears stuck to the screen.

The box should remain attached to the real-world text object.

The box should move naturally when:

- camera moves left
- camera moves right
- camera moves up
- camera moves down
- camera rotates
- camera moves closer
- camera moves farther

The box should behave as an AR object.

---

TRACKING REQUIREMENTS

After anchor creation:

DO NOT render using OCR coordinates.

OCR coordinates are valid only for initial detection.

After lock:

Use only:

- Anchor Pose
- Camera Pose
- Quaternion Orientation
- World Coordinates

Every frame:

Anchor Pose
↓
World Position
↓
Projection
↓
Screen Position
↓
Render

---

CAMERA SHAKE REQUIREMENTS

Camera shake must not break tracking.

Use:

- ARCore Pose Tracking
- Quaternion Smoothing
- Pose Smoothing
- Motion Prediction

Target FPS:

30-60 FPS

Tracking must remain stable.

---

TEXT REPLACEMENT REQUIREMENTS

Do not draw a floating translation label.

Replace the original text.

Requirements:

- same position
- same size
- same orientation
- same alignment
- same perspective

Translation must remain inside original text bounds.

---

TEXT FITTING REQUIREMENTS

Automatically fit translated text.

Support:

- long translations
- short translations
- multiline translations

Never:

- overflow
- clip
- overlap nearby text

---

TECH STACK

Use:

- CameraX
- ML Kit Text Recognition v2
- ML Kit Language Identification
- ML Kit Translation
- ARCore Session
- ARCore Anchors
- ARCore Hit Testing
- ARCore Camera Pose
- ARCore Plane Tracking
- ARCore Depth API

If needed:

- OpenCV Optical Flow
- ORB Feature Tracking

for stronger lock stability.

---

BEFORE IMPLEMENTATION

Provide a report:

1. What already exists.
2. What is currently broken.
3. Which files are responsible.
4. Whether tracking currently uses:
   - OCR coordinates
   - AR anchors
   - projected anchor coordinates
5. Whether projection math is correct.
6. Whether pose updates are reaching rendering.

Only then begin modifications.

---

SUCCESS CRITERIA

1. Largest text box detected.
2. AR anchor created.
3. Target locked.
4. Camera moves.
5. Boundary box remains attached to object.
6. Translation remains attached to object.
7. Translation updates at 30-60 FPS.
8. Camera shake does not break lock.
9. No screen-space drifting.
10. No frozen boundary boxes.
11. No translation overlap.
12. Translation appears naturally integrated into the scene.

The final result should feel like a flagship AR translation feature rather than a screen overlay.