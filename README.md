# Leaffliction

A deep-learning pipeline for plant leaf disease classification. It covers everything from dataset analysis and augmentation through image transformation, model training, and prediction.

## Table of Contents

- [Overview](#overview)
- [Requirements](#requirements)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Usage](#usage)
  - [Interactive Pipeline Menu](#interactive-pipeline-menu)
  - [Individual Scripts](#individual-scripts)
- [Pipeline Steps](#pipeline-steps)
- [Supported Classes](#supported-classes)

---

## Overview

Leaffliction classifies plant leaf images into one of eight disease or health categories (Apple and Grape). The pipeline:

1. Analyses the class distribution of your raw dataset.
2. Splits images into training and prediction sets.
3. Augments the training set to balance classes.
4. Applies image transformations (masking, colour histogram, pseudolandmarks, etc.).
5. Trains a Keras convolutional neural network.
6. Predicts the class of a new leaf image.

---

## Requirements

- Python 3.10+
- See [`requirement.txt`](requirement.txt) for all dependencies:

```
albumentations==2.0.8
matplotlib==3.10.7
numpy==1.26.4
opencv-python==4.10.0.84
scikit-image==0.25.2
tensorflow==2.16.1
tqdm==4.57.0
```

---

## Installation

```bash
pip install -r requirement.txt
```

Place your raw dataset under `dataset/images/`, with one sub-folder per class:

```
dataset/
└── images/
    ├── Apple_Black_rot/
    ├── Apple_healthy/
    ├── Apple_rust/
    ├── Apple_scab/
    ├── Grape_Black_rot/
    ├── Grape_Esca/
    ├── Grape_healthy/
    └── Grape_spot/
```

Alternatively, unzip a pre-built dataset archive with menu option **12** (see below).

---

## Project Structure

```
leafliction/
├── dataset/               # Raw images (not committed)
├── src/
│   ├── Augmentation.py    # Data augmentation (albumentations)
│   ├── Distribution.py    # Class distribution plots
│   ├── Transformation.py  # Image transformation (mask, ROI, histogram…)
│   ├── augment.py         # Augmentation helpers
│   ├── logger.py          # Simple logger utility
│   ├── main.py            # Legacy entry point
│   ├── parser.py          # Argument parsing helpers
│   ├── plots.py           # Bar / pie chart helpers
│   ├── predict.py         # Single-image prediction
│   ├── test_accuracy.py   # Batch accuracy evaluation
│   ├── train.py           # Model training
│   └── transform.py       # Transform helpers
├── setup.py               # Interactive pipeline CLI menu
├── requirement.txt
└── README.md
```

---

## Usage

### Interactive Pipeline Menu

`setup.py` provides a terminal menu (arrow-key navigation) that orchestrates the full pipeline:

```bash
python setup.py            # interactive curses menu
python setup.py --text     # plain text menu (no arrow keys)
python setup.py --list     # print menu options and exit
python setup.py --run 1    # run a specific step non-interactively
```

#### Menu options

| # | Action |
|---|--------|
| 1 | Distribution analysis – generate bar/pie charts |
| 2 | Split dataset → `dataset_train/` + `dataset_predict/` |
| 3 | Augment training set → `dataset_train_augmented/` |
| 7 | Demo: augment a random image |
| 8 | Demo: transform a random image |
| 9 | Clean all generated output folders |
| 10 | Train the model on `dataset/images/` |
| 11 | Test model accuracy on the validation split |
| 12 | Unzip `dataset_and_model.zip` |
| 0 | Exit |

### Individual Scripts

**Distribution analysis**

```bash
python src/Distribution.py dataset/images/ [--output-dir distribution_graphs]
```

**Augmentation** (balance a folder or visualise one image)

```bash
python src/Augmentation.py dataset_train/ --output-dir dataset_train_augmented/
python src/Augmentation.py path/to/leaf.jpg          # demo mode
```

**Transformation** (apply and display all transformations)

```bash
python src/Transformation.py path/to/leaf.jpg
python src/Transformation.py -src dataset_train/ -dst output_transform/
```

**Training**

```bash
python src/train.py dataset/images/
```

Saves the trained model to `dataset_and_model/leaf_model.keras`.

**Prediction**

```bash
python src/predict.py path/to/leaf.jpg
```

**Accuracy test**

```bash
python src/test_accuracy.py dataset_and_model/augmented_dataset/val 30
```

---

## Pipeline Steps

1. **Distribution** – counts images per class and saves bar/pie charts.
2. **Split** – copies images into `dataset_train/` (80 %) and `dataset_predict/` (20 %) by class.
3. **Augmentation** – uses albumentations (rotate, flip, blur, colour jitter, perspective…) to balance class sizes.
4. **Transformation** – extracts six feature views per image: Gaussian Blur, Mask, ROI Objects, Analyse Object, Pseudolandmarks, Colour Histogram.
5. **Training** – trains a CNN with Keras on the augmented/transformed dataset.
6. **Prediction** – loads the trained model, applies the same transformation, and displays the predicted class with confidence.

---

## Supported Classes

| Plant | Condition |
|-------|-----------|
| Apple | Black rot |
| Apple | Healthy |
| Apple | Rust |
| Apple | Scab |
| Grape | Black rot |
| Grape | Esca |
| Grape | Healthy |
| Grape | Leaf spot |
