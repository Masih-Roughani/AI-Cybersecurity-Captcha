# CAPTCHA Object Detection with YOLO11

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![Ultralytics](https://img.shields.io/badge/Ultralytics-YOLO-111111?style=for-the-badge)

This repository contains an experimental computer-vision workflow for recognizing objects in image-based CAPTCHA challenges. The project uses Ultralytics YOLO11 for model training, evaluation, and local HTML-based inference experiments.

> This project is intended for research, education, and testing against CAPTCHA pages that you own or are explicitly authorized to evaluate. Do not use it to bypass protections on third-party services.

## Overview

The notebook prepares an image-classification-style dataset for YOLO object detection by assigning one full-image bounding box to each sample. It then:

1. Builds a YOLO-compatible train/test dataset.
2. Checks class distribution and previews sample images.
3. Oversamples under-represented classes in the training split only.
4. Fine-tunes a YOLO11s model.
5. Evaluates predictions on the test split with accuracy, a classification report, and a confusion matrix.
6. Runs an optional local HTML experiment that captures CAPTCHA tiles, predicts their classes, and clicks matching tiles through Chrome DevTools Protocol.

## Supported classes

The current notebook defines the following nine classes:

| Class ID | Class |
| ---: | --- |
| 0 | Bicycle |
| 1 | Bridge |
| 2 | Bus |
| 3 | Car |
| 4 | Crosswalk |
| 5 | Hydrant |
| 6 | Motorcycle |
| 7 | Stair |
| 8 | Traffic Light |

## Repository contents

| File | Description |
| --- | --- |
| [`sample.ipynb`](sample.ipynb) | Main notebook containing dataset preparation, training, evaluation, and local HTML inference code. |
| [`sample.html`](sample.html) | Static HTML export of the notebook, including saved outputs and visualizations. |
| [`best.pt`](best.pt) | Trained YOLO weights produced by the project. |
| [`yolo11s.pt`](yolo11s.pt) | YOLO11s base/pretrained weights used as the training starting point. |
| [`Video_compressed.mp4`](Video_compressed.mp4) | Compressed project video/demo asset. |

## Important project-layout note

The notebook was exported from a larger local project and expects the following paths when it is executed:

```text
project-root/
├── data/
│   ├── train/
│   └── test/
└── website.html
```

The `data/` directory and the original `website.html` are not part of this repository snapshot. The included `sample.html` is the exported notebook report, not a drop-in replacement for `website.html`. To reproduce training or the local HTML experiment, restore the corresponding dataset and page, then update the paths in the first notebook cell if needed.

## Training configuration

The notebook uses the following main settings:

- Base model: `yolo11s.pt`
- Image size: `512`
- Epochs: `120`
- Optimizer: `AdamW`
- Seed: `42`
- Batch size: `16` with CUDA, otherwise `2`
- Device: CUDA when available, otherwise CPU
- Training-only class balancing through oversampling
- No mosaic, mixup, or copy-paste augmentation

The dataset builder creates a full-image YOLO label for each sample. This is appropriate for the notebook's single-object-per-tile setup; it should be changed if the dataset contains multiple independently localized objects.

## Installation

Create a virtual environment and install the notebook dependencies:

```bash
python -m venv .venv
```

Activate it on Windows:

```powershell
.venv\Scripts\Activate.ps1
```

Install the required packages:

```bash
pip install ultralytics torch torchvision numpy pandas pillow matplotlib scikit-learn pyyaml requests websocket-client jupyter
```

For GPU training, install the PyTorch build that matches your CUDA installation before installing or upgrading the remaining packages.

## Running the notebook

1. Restore the expected `data/train`, `data/test`, and `website.html` paths.
2. Open the notebook:

   ```bash
   jupyter notebook sample.ipynb
   ```

3. Run the cells in order.
4. Review the generated dataset summaries, training outputs, test report, and confusion matrix.

Generated datasets, runs, and reports are written under `_generated/` by the notebook. That directory is intentionally not included in this snapshot because it contains reproducible training artifacts and can become very large.

## Using the trained weights

After installing `ultralytics`, a single image can be evaluated with:

```python
from ultralytics import YOLO

model = YOLO("best.pt")
results = model.predict("path/to/image.png", imgsz=512, conf=0.5)

for result in results:
    print(result.names)
    print(result.boxes)
```

The notebook's local HTML solver uses stricter decision logic than the example above. It compares the predicted class with the displayed target, checks a confidence threshold and confidence margin, and only then selects a tile.

## Reproducibility and limitations

- Results depend on the original dataset, class balance, hardware, and installed package versions.
- The repository does not include the source dataset, so a fresh checkout cannot retrain the model without restoring that data.
- `best.pt`, `yolo11s.pt`, and `Video_compressed.mp4` are tracked with Git LFS because they are binary artifacts.
- The HTML automation code is designed for a local test page and may require changes for another page structure or browser environment.
- No production accuracy, latency, or security guarantee is provided.

## License

No separate license file is included in this snapshot. Add a license before redistributing the code, trained weights, dataset, or media under terms that clearly define permitted use.

