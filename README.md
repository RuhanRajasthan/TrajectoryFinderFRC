# TrajectoryFinderFRC

That sounds like a fantastic and ambitious project—useful not just strategically, but also as a great way to apply computer vision, robotics kinematics, and software engineering. You're right to anticipate some of the harder problems (e.g., camera calibration, occlusion, orientation), but the project is feasible if broken into manageable stages.

Here’s a **detailed step-by-step guide** for how you might approach and execute this, primarily using **Java and OpenCV**, with notes for optional Python tools where necessary.

---

## 🧩 PHASE 1: SETUP AND PLANNING

### Step 1: Define the Scope

* Will this be real-time or post-match analysis?
* How many camera angles per match will you support?
* Will you track only one robot at a time or all robots on the field?

### Step 2: Collect Resources

* Obtain several high-quality FRC match videos from sources like:

  * The Blue Alliance ([https://www.thebluealliance.com/](https://www.thebluealliance.com/))
  * YouTube (Official FRC events)
* Get the **field layout drawings** and dimensions from the **FRC Game Manual** for the specific year(s) you want to support.
* Collect sample images of FRC robots for visual reference and possible training.

---

## 📷 PHASE 2: VIDEO AND CAMERA PREPROCESSING

### Step 3: Frame Extraction

* Use OpenCV (`VideoCapture`) to read in video and extract frames (e.g., 10 fps).
* Save frames in a buffer for faster processing.

### Step 4: Camera Calibration

* Use known points in the field (e.g., driver station corners, field elements) to perform **homography** or **perspective transform**.
* Use OpenCV's `findChessboardCorners()` or `solvePnP()` with manually defined field markers to determine the camera's position and angle.
* This helps with **undistorting the view** and converting pixel coordinates to real-world field coordinates.

> Optional: Use ArUco markers or QR tags on the field corners or robots (some offseason events use this).

---

## 🧠 PHASE 3: ROBOT DETECTION & TRACKING

### Step 5: Robot Identification

* Use color filtering to detect bumpers (each alliance has a red or blue bumper).

  * Use HSV color space for better robustness under lighting changes.
  * Use `inRange()`, `findContours()`, and `boundingRect()` to locate rectangles of red/blue.

* Use OCR (`Tesseract` in Java via Tess4J or EasyOCR in Python) to read team numbers on bumpers (if readable).

  * Alternatively, use team starting positions + alliance to help narrow down.

### Step 6: Tracking the Robot

* Once a robot is detected in a frame, use a tracker:

  * Java options: OpenCV's `TrackerKCF`, `TrackerCSRT` (available in Java bindings too).
  * For better robustness, you could re-detect periodically and re-initialize the tracker.
* Store robot positions as (x, y, time) data points in a buffer.

### Step 7: Handle Occlusions

* If tracking is lost:

  * Attempt to re-detect based on most recent known position.
  * Use **Kalman Filter** or **Optical Flow (e.g., Farneback)** to predict movement temporarily.
  * If multiple angles are available, use the view with fewer occlusions.

---

## 🧭 PHASE 4: FIELD POSITIONING AND TRAJECTORY MAPPING

### Step 8: Map Pixel Position to Field Coordinates

* Use the camera calibration from Step 4 to map screen (pixel) positions to field coordinates.
* Use `getPerspectiveTransform()` and `warpPerspective()` for mapping.

### Step 9: Draw the Trajectory

* Plot a path using JavaFX or a Java graphics library, drawing a smooth line through the positions.
* Store the trajectory data in a format like JSON or CSV.

### Step 10: Calculate Orientation

* Detect bounding box orientation with `minAreaRect()` to estimate which side of the robot is facing where.
* Store angle with each position if possible.

---

## 🧪 PHASE 5: ANALYSIS AND OUTPUT

### Step 11: Calculate Metrics

* Use position + time to compute:

  * Velocity: $v = \frac{d}{t}$
  * Acceleration (optional)
  * Time spent near specific field elements (e.g., goal, game piece area)

### Step 12: Export and Visualization

* Output the trajectory, speed, orientation over time.
* Provide visualizations:

  * Heatmaps (where the robot spent most time)
  * Overlay on top-down map of the field

---

## 📚 PHASE 6: UI & USABILITY

### Step 13: Build a Simple Interface

* Create a GUI to:

  * Select match video
  * Choose team to track
  * View/export results

### Step 14: Optional Features

* Multi-robot tracking (track all 6)
* Game-specific logic (e.g., detect when a robot scored)
* Web interface (Spring Boot + frontend)

---

## 🚧 Tools, Libraries, and Java-Specific Notes

| Task                       | Java Tools                                      |
| -------------------------- | ----------------------------------------------- |
| Video Processing           | OpenCV Java bindings                            |
| OCR                        | Tess4J                                          |
| UI                         | JavaFX or Swing                                 |
| Charting                   | JFreeChart, JavaFX Canvas                       |
| JSON Export                | Jackson or Gson                                 |
| Optional (Python fallback) | Run model via Python subprocess, share via JSON |

---

## 🤖 Challenges You Might Face (And Mitigation)

| Challenge                 | Solution                                                                                           |
| ------------------------- | -------------------------------------------------------------------------------------------------- |
| Tracking behind obstacles | Use multiple angles, Kalman Filter                                                                 |
| Camera distortion         | Perspective calibration                                                                            |
| Team number recognition   | OCR fallback or manual ID                                                                          |
| Robot orientation         | Analyze rectangle angle or motion vector                                                           |
| Java OpenCV ecosystem     | Java OpenCV is supported but less common—stick to core features or use JNI for more advanced tasks |

---

## 🔄 Iterative Milestone Plan

1. ✅ Extract and display robot positions from one camera
2. ✅ Calibrate a camera with known field geometry
3. ✅ Convert positions to field coordinates
4. ✅ Track one robot over an entire match
5. ✅ Visualize and export the trajectory
6. ✅ Track multiple matches, same robot
7. ✅ Add velocity + orientation estimation
8. ✅ Build a user interface

---

Would you like a GitHub-ready project structure, a basic code scaffold (Java + OpenCV), or a visual diagram to help sketch this out further?
