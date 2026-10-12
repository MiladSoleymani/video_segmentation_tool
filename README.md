# Video Segmentation Tool

A desktop tool for semi-automatic labelling of videos for semantic segmentation. The user labels a key frame by hand, and the tool propagates the labels to the other frames. Developed as my B.Sc. thesis at K. N. Toosi University of Technology, for labelling road and lane-marking videos.

## Features

- **Labelling tools:** pen (left click draws, right click erases), polygon, move, zoom, label transparency and multiple label classes.
- **Label propagation** from key frames with two algorithms:
  - **Optical flow** (OpenCV Farnebäck): carries each mask to the next frame.
  - **OSVOS** (One-Shot Video Object Segmentation, PyTorch): fine-tunes a network pretrained on DAVIS on the key frame, then segments every frame independently.
- **MVC design** (PyQt6), so new tools and algorithms can be added easily.

## Results

On a hand-labelled 200-frame highway video, starting from one key frame:

| Algorithm | Road IoU (%) | Lane-marking IoU (%) |
|---|---|---|
| OSVOS | 86.5 ± 5.3 | **63.0 ± 7.4** |
| Optical flow | **87.7 ± 3.2** | 20.8 ± 15.4 |

Both methods label the road well. Optical flow accumulates errors and loses the thin lane markings, while OSVOS stays stable to the end of the video.

## Installation

```bash
git clone https://github.com/MiladSoleymani/video_segmentation_tool.git
cd video_segmentation_tool
pip install -r requirements.txt

# OSVOS pretrained weights
cd osvos/models && bash download_parent_model.sh && bash download_vgg_weights.sh && cd ../..
```

## Usage

```bash
python app.py
```

1. Open an `.mp4` video.
2. Label one or more frames with the pen or polygon tool and mark them as key frames.
3. Click **Run**, choose the algorithm (optical flow or OSVOS) and the frames to label, and run.

A CUDA GPU is recommended for OSVOS; it falls back to the CPU otherwise.

## Project structure

```
app.py          entry point
models/         application state (frames, labels)
views/          main window and run window (PyQt6)
controllers/    tools, rendering pipeline and main logic
osvos/          OSVOS network and fine-tuning
```

## Acknowledgments

OSVOS: S. Caelles et al., "One-Shot Video Object Segmentation", CVPR 2017.
