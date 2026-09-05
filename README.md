# ✋ Hand Detection using cvzone & OpenCV

A Python script that detects and tracks hands in a video file using **cvzone's HandTrackingModule** (built on MediaPipe) and **OpenCV**. It labels each detected hand as **Left** or **Right**, with color-coded text overlaid on the video frames.

## 📌 Overview

This script reads a video file (`hands.mp4`), detects up to two hands per frame, and displays the hand type (Left/Right) directly above each detected hand's center point in real time as the video plays.

## 🛠️ Tech Stack

- [OpenCV](https://opencv.org/) (`opencv-python`)
- [cvzone](https://github.com/cvzone/cvzone) (`HandTrackingModule`)
- MediaPipe (used internally by cvzone for hand landmark detection)

## ⚙️ Installation

```bash
pip install opencv-python cvzone
```

> cvzone's `HandTrackingModule` depends on `mediapipe`, which will be installed automatically as a dependency. If it isn't, install it manually with `pip install mediapipe`.

## 📁 Requirements

- A video file named `hands.mp4` placed in the same directory as the script (or update the path in `cv2.VideoCapture("hands.mp4")` to point to your own video).

## ▶️ Usage

```bash
python hand_detection.py
```

- The video will open in a window titled **"Hand Detection"**.
- Detected hands are labeled **Right** (green) or **Left** (orange), drawn at the hand's center with a black outline for readability.
- Press **`q`** at any time to quit early.
- The script also exits automatically when the video ends or if the video file fails to open.

## 🔍 How It Works

1. **Initialization** — Creates a `HandDetector` with `detectionCon=0.8` (80% detection confidence threshold) and `maxHands=2` (tracks up to 2 hands at once).
2. **Video capture** — Opens `hands.mp4` with OpenCV; exits with an error message if the file can't be read.
3. **Per-frame detection loop:**
   - Reads a frame; stops the loop when the video ends.
   - Runs `detector.findHands()` to detect hands and get landmark/type data.
   - For each detected hand, retrieves its type (`"Left"`/`"Right"`) and center coordinates `(cx, cy)`.
   - **Fallback logic:** if cvzone doesn't return a recognizable type, the script assigns `"Right"` when the hand's center is on the left half of the frame (`cx < w // 2`), and `"Left"` otherwise — i.e., it assumes a **mirrored view** (as with a front-facing camera), where the hand appearing on the left side of the image belongs to the person's right hand.
   - Draws the label as text with a black outline (for contrast) followed by the colored fill (green for Right, orange for Left), centered above the hand.
4. **Display** — Shows the annotated frame in a live window until the video ends or `q` is pressed.
5. **Cleanup** — Releases the video capture and closes all OpenCV windows.

## 🎨 Customization

| Setting | Location | Description |
|---|---|---|
| `detectionCon` | `HandDetector(...)` | Minimum confidence to count as a valid hand detection (0–1) |
| `maxHands` | `HandDetector(...)` | Maximum number of hands tracked per frame |
| Video source | `cv2.VideoCapture("hands.mp4")` | Replace with `0` to use a live webcam instead of a video file |
| Colors | `color = (0, 220, 0) if ... else (0, 180, 255)` | BGR color values for Right/Left labels |
| Font scale/thickness | `scale, thick = 3.0, 6` | Adjust label text size and boldness |

## ⚠️ Notes

- The mirrored-view fallback assumption only matters when cvzone fails to classify a hand's type on its own; in most cases cvzone provides the Left/Right label directly.
- Since the input here is a pre-recorded video (not a live mirrored webcam feed), double-check that the fallback left/right assumption matches your actual footage — flip the condition if labels appear swapped.

