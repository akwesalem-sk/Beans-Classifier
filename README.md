# Bean Quality Classifier (prototype)

A computer-vision prototype for sorting **good vs. bad beans**, built as the first stage of a larger goal: an affordable automated produce-sorting machine (camera + classifier + mechanical sorter) for local food processors.

> **Status: working pipeline, not a validated model.** The code runs end to end, but the model was trained on a small, mismatched dataset and has **not** been validated on dry beans. Collecting a proper dry-bean dataset is the next milestone. See [What I learned](#what-i-learned) and [Roadmap](#roadmap).

## What it does

- Trains a binary image classifier (good / bad) using **transfer learning** on MobileNetV2 (TensorFlow / Keras)
- Resizes and prepares images, applies data augmentation (flip, rotation, zoom, brightness, contrast)
- Evaluates with a train/validation split and a confusion matrix
- Includes a **live webcam notebook** that classifies what the camera sees in real time

## Files

| File | Purpose |
|---|---|
| `bean_classifier.ipynb` | Data prep, training, evaluation, saving the model |
| `camera_test.ipynb` | Loads the saved model and classifies a live webcam feed |

## How to run

1. Install dependencies:
   ```
   pip install tensorflow pillow matplotlib opencv-python notebook
   ```
2. Download a good/bad bean image dataset (see [Dataset](#dataset)) and extract it.
3. In `bean_classifier.ipynb`, set `RAW_DIR` to your extracted folder and adjust `CLASS_FOLDERS` to match the folder names.
4. Run the cells in order. The resize cell creates `data/good` and `data/bad`.
5. Run the **duplicate-removal check** (see below) before training.
6. Training saves `bean_classifier.keras`. Use `camera_test.ipynb` to try it on a webcam.

## Dataset

Trained on the public "BEANS" good/bad image dataset from Mendeley Data (search "BEANS good bad beans" at data.mendeley.com).
The dataset is not included in this repo. It contains images of green bean pods, not dry beans.

## What I learned

- **Dataset fit matters more than model choice.** I started this project aiming at dry beans (the brown and white varieties common in Nigeria). The public dataset I used contains green bean pods, so the model cannot be trusted on the beans I actually care about.
- **Check your data for duplicates.** After removing exact duplicate files, only about 12 unique "good" and 40 unique "bad" images remained, far fewer than the file counts suggested. I did not fully investigate the cause. Duplicates across a train/validation split inflate accuracy, so **no accuracy figure is reported here**.
- **Confidence scores are not correctness.** The model returns high confidence on anything, including images that are not beans, because it only has two outputs (good, bad) and no "not a bean" option.
- Real deployment needs more than a model: consistent lighting, one bean per image, and a way to reject non-bean objects.

## Roadmap

1. Build a dry-bean dataset: photograph scattered beans on a plain background, auto-crop each bean, sort into good / bad folders
2. Retrain and report honest validation metrics on held-out images
3. Add a "not a bean / empty" class
4. Convert to TensorFlow Lite and run on a Raspberry Pi with a camera
5. Add a servo or air-jet mechanism to physically separate bad beans

## Tech

Python, TensorFlow / Keras, MobileNetV2 (ImageNet weights), OpenCV, Pillow, Matplotlib, Jupyter (VS Code)
