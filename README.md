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