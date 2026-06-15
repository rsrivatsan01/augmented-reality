Current status:

The camera works.

OCR works.

Text boxes are detected correctly.

Bounding boxes are visible.

DO NOT modify:

- CameraX
- OCR
- Translation
- Language detection
- UI design

The problem is ONLY tracking.

---

CURRENT BUG

The detected text box appears stuck to the screen.

Example:

1. OCR detects a sign.
2. Bounding box appears.
3. User moves the camera.
4. Bounding box remains attached to the screen position.
5. Bounding box does NOT stay attached to the real-world sign.

This is incorrect.

The text box should follow the real object.

---

GOAL

The bounding box must stay attached to the detected text in the real world.

If the user moves the camera:

- left
- right
- up
- down
- forward
- backward

the box must move with the text.

The box should appear glued to the sign, poster, book, or object containing the text.

---

DO NOT USE SCREEN LOCKING

Do not store a fixed screen position.

Do not keep drawing the box at old screen coordinates.

Do not assume OCR coordinates remain valid forever.

---

USE ARCORE PROPERLY

Use ARCore tracking capabilities.

Use:

- ARCore camera pose
- Feature points
- World tracking
- Anchors when appropriate

The detected text should be associated with a real-world position.

When the camera moves:

ARCore should update the position of the tracked text.

The overlay should be redrawn at the new projected screen position.

---

REQUIRED BEHAVIOR

When text is first detected:

1. Determine the text region.
2. Create a tracked target.
3. Associate the target with ARCore tracking data.

During every frame:

1. Obtain current ARCore camera pose.
2. Update tracked target position.
3. Project target into screen coordinates.
4. Draw the box at the updated location.

---

TRACKING PRIORITY

Tracking quality is more important than translation.

Do not work on translation.

Do not work on overlays.

Do not work on caching.

Only fix tracking.

---

SUCCESS TEST

Test 1:

Point camera at a sign.

Expected:
Box appears around sign.

Test 2:

Move camera left.

Expected:
Box moves with sign.

Test 3:

Move camera right.

Expected:
Box remains attached to sign.

Test 4:

Move camera closer.

Expected:
Box scales naturally.

Test 5:

Move camera away.

Expected:
Box scales naturally.

The box must never appear frozen at a fixed screen position.

---

IMPORTANT

Do not redesign the app.

Do not add new features.

Do not modify OCR.

Focus only on making detected text boxes remain attached to real-world text using ARCore tracking and camera pose updates.# augmented-reality