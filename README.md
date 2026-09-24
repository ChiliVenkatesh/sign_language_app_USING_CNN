# 🤟 Sign Language Detection

Real-time hand-sign recognition from a live webcam feed, built with MediaPipe hand tracking and rule-based gesture detection, served through a Streamlit + WebRTC app.

## How it works

```
Webcam frame → MediaPipe Hand Landmarker → Finger-state rules → Gesture label → Overlay on video
```

- **`streamlit-webrtc`** streams live video from the browser into the app.
- **MediaPipe's `HandLandmarker`** (video mode) detects 21 hand landmarks per frame.
- **Rule-based logic** (`gesture_rules.py`) checks which fingers are extended, based on landmark positions, and maps the finger pattern to a gesture label.
- The hand skeleton and detected gesture name are drawn directly on the video frame and streamed back to the browser.

## Supported Signs

| Gesture | Finger pattern |
|---|---|
| ✊ FIST | all fingers closed |
| ✋ FIVE | all fingers open |
| ☝️ ONE | index only |
| ✌️ VICTORY | index + middle |
| 👍 THUMBS UP | thumb only |
| 🤟 LOVE YOU | thumb + index + pinky |
| L | thumb + index |

## Tech Stack

- **UI / video:** Streamlit, `streamlit-webrtc`, `av`
- **Hand tracking:** MediaPipe Tasks (`HandLandmarker`)
- **Frame processing:** OpenCV

## Project Structure

```
.
├── app.py                    # Streamlit UI + WebRTC video processor
├── backend/
│   ├── camera_stream.py       # Per-frame pipeline: detect -> draw -> label
│   ├── hand_landmarker.py     # Creates the MediaPipe HandLandmarker
│   ├── gesture_rules.py       # Finger-state -> gesture name rules
│   ├── drawing_utils.py       # Landmark/skeleton drawing helpers
│   └── speaker.py             # Optional text-to-speech for the detected sign
├── models/
│   └── hand_landmarker.task   # MediaPipe hand landmark model
└── requirements.txt
```

> **Fix before running:** `backend/hand_landmarker.py` loads the model from `models/hand_landmarker.task`, and all the backend modules import as `backend.<module>` (e.g. `from backend.hand_landmarker import create_hand_landmarker`). Put `camera_stream.py`, `hand_landmarker.py`, `gesture_rules.py`, `drawing_utils.py`, and `speaker.py` inside a `backend/` folder (with an `__init__.py`), and move `hand_landmarker.task` into a `models/` folder, so the paths and imports resolve.

## Setup

1. **Clone the repo**
   ```bash
   git clone <your-repo-url>
   cd <repo-folder>
   ```

2. **Arrange files as shown above** — `backend/` package for the logic modules, `models/hand_landmarker.task` for the model file.

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```
   `speaker.py` additionally needs `pyttsx3` if you enable the text-to-speech line (`pip install pyttsx3`).

4. **Run the app**
   ```bash
   streamlit run app.py
   ```

## Usage

1. Allow camera access when the browser prompts for it.
2. Hold up a hand in view of the webcam.
3. The detected gesture name and hand skeleton overlay appear live on the video feed.
4. Reference card on the right shows all currently supported signs.

## Notes

- Detection currently runs on a single hand (`num_hands=1`); raise this in `hand_landmarker.py` to track multiple hands.
- Text-to-speech for the detected sign is wired up in `camera_stream.py` but commented out (`#speak(sign)`) — uncomment it to hear the gesture spoken aloud.
- Unrecognized finger patterns are labeled `"UNKNOWN"` — extend `gesture_rules.py` to add more signs.

## License

MIT (or update as needed).
