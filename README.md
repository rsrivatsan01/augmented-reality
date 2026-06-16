This project is large enough that you should treat it like building a product, not a feature.

Rule: Never move to the next step until the current step passes all checks.


---

Phase 1: Stable OCR Foundation

Prompt 1

Goal:

Camera + OCR + Bounding Boxes

Nothing else.

Requirements:

Camera preview works

OCR detects text

Draw bounding boxes

No translation

No AR tracking

No overlays except boxes


Check After Completion

Test:

Book cover

Laptop screen

Poster

Product label


Must pass:

✅ Camera never freezes

✅ No crashes

✅ Boxes appear around text

✅ Boxes update live

✅ No black screen

❌ If any fail, stop here.


---

Phase 2: Largest Text Selection

Prompt 2

Goal:

Choose only ONE text block

Requirements:

Ignore all smaller blocks

Select largest fully visible block

Reject edge-touching boxes

Reject tiny detections


Check After Completion

Point at:

Large title
Small subtitle
Tiny logo

Must pass:

✅ Only one box visible

✅ Largest text selected

✅ Selection is consistent


---

Phase 3: Target Locking

Prompt 3

Goal:

Stop box jumping

Requirements:

States:

SEARCHING
LOCKING
LOCKED

Lock only after:

3 consecutive detections

Check After Completion

Must pass:

✅ Box doesn't jump

✅ Box stays on same text

✅ Moving camera slightly doesn't change target

✅ New text doesn't steal focus


---

Phase 4: Real Tracking

Prompt 4

Goal:

Track object
Not screen coordinates

Requirements:

Use ARCore camera pose

Use feature tracking

Update every frame


Check After Completion

Point at sign.

Move camera:

Left

Right

Up

Down

Closer

Further

Must pass:

✅ Box follows object

✅ Box not glued to screen

✅ No noticeable lag

This is the most important phase.


---

Phase 5: Tracking Smoothing

Prompt 5

Goal:

Remove jitter

Requirements:

Interpolation

Motion smoothing

Stable movement


Check After Completion

Must pass:

✅ Box moves smoothly

✅ No shaking

✅ No flickering

✅ Feels premium


---

Phase 6: Translation Engine

Prompt 6

Goal:

Translate locked target only

Requirements:

Translate only LOCKED target

Cache translations

Ignore English


Check After Completion

Must pass:

✅ Correct language detected

✅ Correct translation

✅ No repeated translation requests

✅ No frame drops


---

Phase 7: Text Replacement

Prompt 7

Goal:

Replace text inside box

Requirements:

Fit translation inside original bounds

Auto-size text

Multiline support


Check After Completion

Must pass:

✅ Translation stays inside box

✅ No clipping

✅ No overflow


---

Phase 8: Font Matching

Prompt 8

Goal:

Match original appearance

Requirements:

Estimate:

Font size

Weight

Alignment


Check After Completion

Must pass:

✅ Translation resembles original layout

✅ Doesn't look like random Android text


---

Phase 9: Perspective Correction

Prompt 9

Goal:

Match sign angle

Requirements:

Detect surface orientation

Warp translation


Check After Completion

Must pass:

✅ Tilt phone

✅ Translation tilts with sign

✅ Doesn't remain flat


---

Phase 10: Color Matching

Prompt 10

Goal:

Match text color

Requirements:

Extract:

Text color

Contrast


Check After Completion

Must pass:

✅ Red text becomes red translation

✅ White text becomes white translation

✅ Contrast remains readable


---

Phase 11: Depth Occlusion

Prompt 11

Goal:

Translation belongs to scene

Requirements:

Use:

ARCore Depth API


Check After Completion

Must pass:

✅ Hand in front hides translation

✅ Person walking blocks translation

✅ No rendering through objects


---

Phase 12: World Anchors

Prompt 12

Goal:

Remember translated objects

Requirements:

Create ARCore anchors

Maintain position in world space


Check After Completion

Must pass:

✅ Look away

✅ Look back

✅ Translation reappears correctly


---

Phase 13: Performance Pass

Prompt 13

Goal:

Flagship smoothness

Requirements:

Target:

30–60 FPS camera

5–8 FPS OCR

60 FPS tracking


Check After Completion

Must pass:

✅ No stutters

✅ No ANRs

✅ No memory leaks

✅ No crashes


---

Final Flagship Checklist

Only call the app "done" when all are true:

✅ Camera stable

✅ OCR accurate

✅ One target only

✅ Locking works

✅ Tracking works

✅ Tracking smooth

✅ Translation accurate

✅ Text replacement works

✅ Font fitting works

✅ Perspective correction works

✅ Color matching works

✅ Depth occlusion works

✅ World anchoring works

✅ No crashes

✅ No black screens

✅ No lag spikes

✅ Feels like a camera feature, not an OCR demo

The biggest milestone is Phase 4. If the box is still glued to the screen after Phase 4, stop everything and fix that before touching translation, fonts, colors, or UI.



Current status:

Camera works.

OCR works.

Largest text box selection works.

The problem is tracking.

---

CURRENT BUG

A text box is detected correctly.

When the camera moves:

- the box drifts
- the box freezes
- the box remains attached to old screen coordinates

The box is not attached to the real-world text.

---

IMPORTANT

Do NOT assume ARCore automatically tracks text.

ARCore tracks camera motion.

ML Kit OCR detects text.

A proper tracking layer must be added between OCR and rendering.

---

REQUIRED ARCHITECTURE

OCR (5–8 FPS)
↓
Largest Text Selection
↓
Target Lock
↓
Tracking Layer (60 FPS)
↓
Overlay Rendering

---

TARGET LOCK

When the largest text block is selected:

Store:

- text
- bounding box
- center point

This becomes the active target.

---

TRACKING LAYER

Do not wait for OCR every frame.

Between OCR updates:

Track the target position continuously.

Use:

- ARCore camera pose
- image feature tracking
- optical flow if needed

The target must move smoothly with the real object.

---

ARCORE USAGE

Use ARCore only for:

- camera pose updates
- world tracking
- motion estimation

Use ARCore pose every frame.

When camera pose changes:

Update tracked target position.

Reproject target into screen coordinates.

Redraw overlay.

---

SMOOTHING

Never snap directly.

Use interpolation.

Example:

currentX += (targetX - currentX) * 0.25f
currentY += (targetY - currentY) * 0.25f

Apply to:

- left
- top
- right
- bottom

This removes jitter.

---

OCR REFRESH

OCR should run only:

5–8 FPS

Tracking should run:

every AR frame

approximately:

30–60 FPS

---

SUCCESS TEST

Point at a sign.

A box appears.

Move camera left.

Expected:
Box follows sign.

Move camera right.

Expected:
Box follows sign.

Move closer.

Expected:
Box scales naturally.

Move away.

Expected:
Box scales naturally.

The box must never remain attached to a fixed screen position.

---

DO NOT WORK ON

- translation
- language detection
- text replacement
- UI redesign

Focus only on making the selected largest text box track the real-world text smoothly using ARCore pose updates and a dedicated tracking layer.

Current status:

- Camera works
- OCR works
- Largest text box selection works

Problem:

The selected text boundary box is rendered in screen space only.

When the camera moves, the box does not remain attached to the real-world object containing the text.

The box behaves like a UI overlay instead of an AR object.

---

GOAL

Convert the selected text target into a tracked AR world-space target.

The boundary box must remain attached to the real object while the camera moves.

---

REQUIRED IMPLEMENTATION

When OCR selects the largest text box:

1. Compute the center point of the detected text box.

2. Perform an ARCore hit test using the center screen coordinate.

3. If a valid hit result exists:
   
   - Plane
   - Depth point
   - Feature point

4. Create an ARCore Anchor.

5. Store this anchor as the active text target.

---

TRACKING

Do not store only screen coordinates.

Store:

- Anchor
- Pose
- Tracking state

Every AR frame:

1. Get current camera pose.
2. Get anchor pose.
3. Transform anchor pose into screen coordinates.
4. Update boundary box position.

The boundary box should be rendered using the current projected anchor position.

---

RENDERING

The boundary box must be derived from:

Anchor Pose
→ World Coordinates
→ Camera Coordinates
→ Screen Coordinates

Do not reuse stale OCR coordinates after lock.

OCR coordinates are only used to create the initial anchor.

---

LOST TRACKING

If:

anchor.getTrackingState() != TRACKING

temporarily hide the box.

When tracking resumes:

show the box again.

---

UPDATES

OCR should run only occasionally.

AR tracking should run every frame.

The box position should be updated from the anchor pose continuously.

---

SUCCESS CRITERIA

1. Point camera at a sign.

2. Largest text box detected.

3. Anchor created.

4. Move camera left.

Expected:
Boundary box remains attached to sign.

5. Move camera right.

Expected:
Boundary box remains attached to sign.

6. Move closer.

Expected:
Boundary box scales naturally.

7. Move farther.

Expected:
Boundary box scales naturally.

8. Rotate device.

Expected:
Boundary box remains attached to the text object.

The boundary box must behave like an AR object in the world, not a screen overlay.


DO NOT MODIFY ANY CODE YET.

FIRST PERFORM A COMPLETE TRACKING AUDIT OF THE PROJECT.

Current status:

- Camera preview works.
- OCR works.
- Largest text box detection works.
- Bounding boxes appear.
- Bounding boxes do NOT stay attached to the real-world text.
- Bounding boxes appear stuck to screen coordinates.
- AR tracking behavior is incorrect.

---

TASK 1: INVESTIGATE

Analyze the entire project and identify exactly how text boxes are currently being tracked.

Answer the following:

1. Where are OCR bounding boxes created?

2. Where are OCR bounding boxes transformed into screen coordinates?

3. How are boxes currently rendered?

4. Is ARCore actually used for tracking?

5. Are ARCore Anchors being created?

6. Are HitResults being created?

7. Are Anchor poses updated every frame?

8. Is camera pose used for box updates?

9. Are boxes redrawn from OCR coordinates only?

10. Is there any feature tracking implementation?

11. Is OpenCV used?

12. Is optical flow used?

13. Is there any tracking state machine?

14. What specific code path causes the box to appear attached to the screen instead of the object?

---

TASK 2: REPORT

Provide a report with:

CRITICAL ISSUES
MAJOR ISSUES
MINOR ISSUES

For every issue include:

- File name
- Class name
- Method name
- Exact explanation

---

TASK 3: FIX PLAN

Only after analysis:

Provide a step-by-step implementation plan.

For every required change specify:

- File
- Method
- Reason

Do not implement yet.

---

TASK 4: IMPLEMENTATION

Only after identifying the root cause:

Implement the minimum required changes to achieve:

1. Largest text target selection.
2. Target lock.
3. ARCore world anchor creation.
4. Continuous pose updates.
5. Screen projection updates every AR frame.
6. Smooth box movement.
7. Box remains attached to the physical text object.

---

SUCCESS TESTS

Test A:
Point camera at a sign.

Expected:
Box appears around sign.

Test B:
Move camera left.

Expected:
Box remains attached to sign.

Test C:
Move camera right.

Expected:
Box remains attached to sign.

Test D:
Move closer.

Expected:
Box scales naturally.

Test E:
Move farther.

Expected:
Box scales naturally.

Test F:
Rotate phone.

Expected:
Box remains attached to text.

---

IMPORTANT

Do NOT redesign UI.
Do NOT modify translation.
Do NOT modify language detection.
Do NOT add new features.

Focus exclusively on identifying and fixing the root cause of world-space tracking failure.