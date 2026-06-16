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

DO NOT IMPLEMENT NEW FEATURES.

DO NOT REWRITE THE TRACKING SYSTEM.

DO NOT ADD MORE ARCORE CODE YET.

The project already has:

- OCR working
- Largest text box selection working
- ARCore hit testing
- Anchor creation
- Anchor pose updates
- AR frame updates
- AR tracking update loop
- Overlay rendering

Yet the boundary box still appears visually stuck to the screen and does not remain attached to the detected text.

This means there is likely a bug somewhere between:

Anchor Creation
→ Pose Update
→ Projection
→ SmoothedRect Update
→ Rendering

I need a ROOT CAUSE ANALYSIS.

---

TASK

Perform a complete diagnostic of the AR tracking pipeline.

Trace the currently selected text target from OCR detection all the way to rendering.

---

STEP 1

When the text target is first detected:

Log:

- OCR bounding box
- OCR center point
- OCR image coordinates
- OCR screen coordinates

---

STEP 2

During ARCore hit testing:

Log:

- hit test X
- hit test Y
- hit result type

Possible types:

- Plane
- DepthPoint
- FeaturePoint

Report which one is used.

---

STEP 3

After anchor creation:

Log:

- Anchor world position
- Anchor pose translation
- Anchor pose rotation

Verify that the anchor is actually created at the text location.

---

STEP 4

For every AR frame:

Log:

- Current camera pose
- Current anchor pose
- Current projected screen coordinates

Move the phone left and right during testing.

Determine:

Do projected coordinates change?

YES or NO.

---

STEP 5

Inspect projectToScreen().

Report:

- exact projection math
- matrices used
- view matrix
- projection matrix

Verify:

world coordinates
→ camera coordinates
→ screen coordinates

is mathematically correct.

---

STEP 6

Inspect updateFromARTracking().

Log:

- projected rectangle
- smoothed rectangle

Determine:

Is smoothing reducing movement?

Is smoothing preventing visible updates?

---

STEP 7

Inspect rendering.

Report:

- exact rectangle passed to drawBoundingBoxOutline()

Verify:

Is the renderer receiving updated AR coordinates every frame?

Or is it drawing stale coordinates?

---

FINAL REPORT

Provide:

ROOT CAUSE

Choose one:

A. Anchor created at wrong position

B. Projection math incorrect

C. Camera pose not applied correctly

D. SmoothedRect not updating

E. Renderer drawing stale coordinates

F. Other

---

IMPORTANT

Do NOT modify code until root cause is identified.

Do NOT add new tracking implementations.

Do NOT rewrite ARCore integration.

First prove exactly where the tracking pipeline fails.

The goal is to identify the exact reason why the boundary box visually remains attached to the screen instead of remaining attached to the real-world text object.
