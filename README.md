# AI in Cybersecurity — CAPTCHA Object Detection

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![YOLO11](https://img.shields.io/badge/YOLO11-Ultralytics-111111?style=for-the-badge)

I built this project for the Applications of Artificial Intelligence in Cybersecurity course. It uses YOLO11 to recognize objects inside image-based CAPTCHA tiles. The notebook covers the complete workflow: preparing the dataset, training the model, checking the results, and testing it on a local HTML page.

## What the project does

- Converts the image folders into a YOLO-formatted dataset.
- Checks the number of images available for each class.
- Balances the training data by oversampling smaller classes.
- Fine-tunes a YOLO11s model.
- Evaluates the model on test images and generates a classification report and confusion matrix.
- Tests the trained model on a local HTML CAPTCHA page by reading the target class and selecting the matching tiles.

## Classes

The model currently recognizes these nine classes:

| ID | Class |
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

## Files

- `sample.ipynb` — notebook used for dataset preparation, training, evaluation, and testing.
- `sample.html` — exported HTML version of the notebook with its saved outputs.
- `best.pt` — trained model weights.
- `yolo11s.pt` — YOLO11s weights used as the starting model.

## Project structure

The notebook expects the dataset and the local test page to be available in the following structure:

```text
project-root/
├── data/
│   ├── train/
│   └── test/
└── website.html
```

The dataset and the original `website.html` are not included in this repository. The `sample.html` file is only the exported notebook report. If you want to run the notebook from the beginning, place the required files in the paths above or change the paths in the first cell.

## Training settings

- Model: `yolo11s.pt`
- Image size: `512`
- Epochs: `120`
- Optimizer: `AdamW`
- Seed: `42`
- Batch size: `16` with CUDA and `2` on CPU
- Training device: CUDA when available, otherwise CPU
- Oversampling is applied only to the training split

Each image is treated as one object and receives a full-image bounding box. This matches the CAPTCHA tile format used in the project.

## Installation

```bash
python -m venv .venv
```

On Windows, activate the environment with:

```powershell
.venv\Scripts\Activate.ps1
```

Install the required packages:

```bash
pip install ultralytics torch torchvision numpy pandas pillow matplotlib scikit-learn pyyaml requests websocket-client jupyter
```

## Run the notebook

```bash
jupyter notebook sample.ipynb
```

Run the cells in order. The notebook creates its generated datasets, training runs, and reports inside the `_generated/` directory.

## Use the trained model

```python
from ultralytics import YOLO

model = YOLO("best.pt")
results = model.predict("path/to/image.png", imgsz=512, conf=0.5)

for result in results:
    print(result.names)
    print(result.boxes)
```

The HTML test uses a confidence threshold and a confidence-margin check before selecting a tile.

