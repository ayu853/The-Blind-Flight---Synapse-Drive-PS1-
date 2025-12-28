#  The Blind Flight: Synapse Drive

**Autonomous Pathfinding in Unlabeled Terrains using Deep Learning Ensembles**

![Status](https://img.shields.io/badge/Status-Completed-success)
![Accuracy](https://img.shields.io/badge/Training_Accuracy->99%25-brightgreen)
![Inference Speed](https://img.shields.io/badge/Inference_Speed-~2.02s-blue)

##  Table of Contents
- [Executive Summary](#executive-summary)
- [Problem Statement](#problem-statement)
- [Methodology](#methodology)
  - [Data Preparation](#data-preparation)
  - [Model Architecture](#model-architecture)
  - [Training Strategy](#training-strategy)
- [Advanced Inference Techniques](#advanced-inference-techniques)
- [Path Planning & Navigation](#path-planning--navigation)
- [Results](#results)
- [Author](#author)

---

##  Executive Summary
This project addresses the **"Blind Flight"** challenge, requiring an autonomous agent to navigate a grid-based map from a start point to a destination. The core difficulty is that terrain types in the test set are provided only as visual images without explicit labels.

To solve this, a **hybrid system** was developed combining **Computer Vision** and **Graph Theory**. The solution utilizes an ensemble of deep convolutional neural networks (**ResNet-18** and **EfficientNet-B0**) to classify terrain tiles, followed by a robust pathfinding algorithm (**Dijkstra**) that adapts to environmental "biomes" and velocity boosts. The system features advanced inference techniques ensuring high reliability and optimal path generation.

---

##  Problem Statement
The objective is to parse a raw map image, identify the **Start (Index 3)** and **End (Index 4)** points, and classify intermediate tiles into obstacles or traversable paths with varying costs. The agent must then compute the most efficient path string (e.g., "u", "d", "l", "r") to navigate the grid.

### Key Constraints
* **Input:** Raw images ($W \times H$) representing a $20 \times 20$ grid.
* **Output:** A sequence of moves from Start to End.
* **Ambiguity:** Test images lack ground truth labels; terrain must be inferred visually.

---

##  Methodology

### Data Preparation
The training dataset consisted of full map images paired with JSON files containing the $20 \times 20$ ground truth grid.
* **Slicing Strategy:** Each map image was dynamically sliced into 400 individual tiles (corresponding to grid cells).
* **Labeling:** Each tile was assigned a label (0-4) based on the JSON grid coordinates.
* **Preprocessing:** Tiles were resized to $224 \times 224$ pixels and normalized using ImageNet statistics (Mean: `[0.485, 0.456, 0.406]`, Std: `[0.229, 0.224, 0.225]`).

### Model Architecture
To ensure high accuracy and generalization, an ensemble of two distinct architectures was employed:
1. **ResNet-18:** Captures deep textural features efficiently and prevents vanishing gradients.
2. **EfficientNet-B0:** Selected for superior parameter efficiency and capturing complex spatial hierarchies.

Both models were initialized with pretrained **ImageNet** weights to leverage transfer learning.

### Training Strategy
* **Loss Function:** `CrossEntropyLoss` for multi-class classification.
* **Optimizer:** Adam optimizer with a learning rate of `0.0005`.
* **Splitting:** 80% Training, 20% Validation.
* **Batch Size:** 32.
* **Imbalance Handling:** A `WeightedRandomSampler` oversampled rare classes (Start/End) and undersampled background tiles to prevent bias.
* **Scheduler:** `ReduceLROnPlateau` decayed the learning rate by a factor of 0.5 when validation accuracy plateaued for 2 epochs.
* **Epochs:** Trained for 8 epochs.


---

##  Advanced Inference Techniques

### Ensemble Prediction
During inference, predictions from both ResNet and EfficientNet are averaged to reduce variance and smooth out individual model errors.
$$P_{final} = \frac{P_{ResNet} + P_{EfficientNet}}{2}$$


### Test-Time Augmentation (TTA)
To improve robustness against orientation, TTA was applied:
1. **Forward Pass:** Predict on the original image.
2. **Augmented Pass:** Predict on a horizontally flipped image, then flip the output back.
3. **Aggregation:** Average the two probability maps.

### The "GPS Fix" (Heuristic Post-Processing)
A critical fail-safe for when classification errors miss a Start or End node:
* **Logic:** If 'Start' (Class 3) or 'End' (Class 4) labels are missing from the predicted grid, the system forces the label onto the tile with the highest probability for that class.
* **Impact:** Guarantees a solvable grid for the pathfinding algorithm, preventing runtime crashes.

---

##  Path Planning & Navigation

### Biome Detection
Traversal costs depend on the environment. A heuristic pixel analysis of the global color distribution classifies the map into three biomes:
* **🌲 Forest:** High Green channel values ($G > R+15$ and $G > B+15$). High movement cost.
* **🏜️ Desert:** High "yellow score" ($R+G - 2B$). Medium movement cost.
* **🧪 Lab:** Default environment. Low movement cost.

### Dijkstra's Algorithm
Navigation is modeled as a shortest-path problem on a weighted graph using a priority queue.
* **Nodes:** $20 \times 20$ grid cells.
* **Edges:** Connections to adjacent cells (Up, Down, Left, Right).
* **Weights:** Calculated dynamically:
**Weight = max(0.01, BaseCostBiome − VelocityBoosttile)**


  where velocity boost is retrieved from auxiliary JSON files.

---

## 📊 Results
The solution successfully automates the navigation process with high reliability.

* **Training Accuracy:** > 99% (Converged within 8 epochs)
* **Inference Speed:** ~2.02s per map (including TTA and pathfinding)
* **Submission Validity:** 100% via GPS Fix and TTA.

---

## 👤 Author
**Ayush Raj**
* **GitHub Repository:** [The Blind Flight - Synapse Drive](https://github.com/ayu853/The-Blind-Flight---Synapse-Drive-PS1-)

