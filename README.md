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
[cite_start]This project addresses the **"Blind Flight"** challenge, requiring an autonomous agent to navigate a grid-based map from a start point to a destination[cite: 14]. [cite_start]The core difficulty is that terrain types in the test set are provided only as visual images without explicit labels[cite: 15].

[cite_start]To solve this, a **hybrid system** was developed combining **Computer Vision** and **Graph Theory**[cite: 16]. [cite_start]The solution utilizes an ensemble of deep convolutional neural networks (**ResNet-18** and **EfficientNet-B0**) to classify terrain tiles, followed by a robust pathfinding algorithm (**Dijkstra**) that adapts to environmental "biomes" and velocity boosts[cite: 17]. [cite_start]The system features advanced inference techniques ensuring high reliability and optimal path generation[cite: 18].

---

##  Problem Statement
[cite_start]The objective is to parse a raw map image, identify the **Start (Index 3)** and **End (Index 4)** points, and classify intermediate tiles into obstacles or traversable paths with varying costs[cite: 20]. [cite_start]The agent must then compute the most efficient path string (e.g., "u", "d", "l", "r") to navigate the grid[cite: 21].

### Key Constraints
* [cite_start]**Input:** Raw images ($W \times H$) representing a $20 \times 20$ grid[cite: 23].
* [cite_start]**Output:** A sequence of moves from Start to End[cite: 24].
* [cite_start]**Ambiguity:** Test images lack ground truth labels; terrain must be inferred visually[cite: 25].

---

##  Methodology

### Data Preparation
[cite_start]The training dataset consisted of full map images paired with JSON files containing the $20 \times 20$ ground truth grid[cite: 28].
* [cite_start]**Slicing Strategy:** Each map image was dynamically sliced into 400 individual tiles (corresponding to grid cells)[cite: 29].
* [cite_start]**Labeling:** Each tile was assigned a label (0-4) based on the JSON grid coordinates[cite: 30].
* [cite_start]**Preprocessing:** Tiles were resized to $224 \times 224$ pixels and normalized using ImageNet statistics (Mean: `[0.485, 0.456, 0.406]`, Std: `[0.229, 0.224, 0.225]`)[cite: 31].

### Model Architecture
[cite_start]To ensure high accuracy and generalization, an ensemble of two distinct architectures was employed[cite: 34]:
1. [cite_start]**ResNet-18:** Captures deep textural features efficiently and prevents vanishing gradients[cite: 38].
2. [cite_start]**EfficientNet-B0:** Selected for superior parameter efficiency and capturing complex spatial hierarchies[cite: 39].

[cite_start]Both models were initialized with pretrained **ImageNet** weights to leverage transfer learning[cite: 40].

### Training Strategy
* [cite_start]**Loss Function:** `CrossEntropyLoss` for multi-class classification[cite: 44].
* [cite_start]**Optimizer:** Adam optimizer with a learning rate of `0.0005`[cite: 45].
* [cite_start]**Splitting:** 80% Training, 20% Validation[cite: 46].
* [cite_start]**Batch Size:** 32[cite: 47].
* [cite_start]**Imbalance Handling:** A `WeightedRandomSampler` oversampled rare classes (Start/End) and undersampled background tiles to prevent bias[cite: 48].
* [cite_start]**Scheduler:** `ReduceLROnPlateau` decayed the learning rate by a factor of 0.5 when validation accuracy plateaued for 2 epochs[cite: 49].
* [cite_start]**Epochs:** Trained for 8 epochs[cite: 50].

---

##  Advanced Inference Techniques

### Ensemble Prediction
[cite_start]During inference, predictions from both ResNet and EfficientNet are averaged to reduce variance and smooth out individual model errors[cite: 53, 56].
$$P_{final} = \frac{P_{ResNet} + P_{EfficientNet}}{2}$$

### Test-Time Augmentation (TTA)
[cite_start]To improve robustness against orientation, TTA was applied[cite: 58]:
1. [cite_start]**Forward Pass:** Predict on the original image[cite: 59].
2. [cite_start]**Augmented Pass:** Predict on a horizontally flipped image, then flip the output back[cite: 61].
3. [cite_start]**Aggregation:** Average the two probability maps[cite: 62].

### The "GPS Fix" (Heuristic Post-Processing)
[cite_start]A critical fail-safe for when classification errors miss a Start or End node[cite: 68]:
* [cite_start]**Logic:** If 'Start' (Class 3) or 'End' (Class 4) labels are missing from the predicted grid, the system forces the label onto the tile with the highest probability for that class[cite: 70].
* [cite_start]**Impact:** Guarantees a solvable grid for the pathfinding algorithm, preventing runtime crashes[cite: 71].

---

##  Path Planning & Navigation

### Biome Detection
Traversal costs depend on the environment. [cite_start]A heuristic pixel analysis of the global color distribution classifies the map into three biomes[cite: 75]:
* ** Forest:** High Green channel values ($G > R+15$ and $G > B+15$). [cite_start]High movement cost[cite: 76].
* ** Desert:** High "yellow score" ($R+G - 2B$). [cite_start]Medium movement cost[cite: 77].
* ** Lab:** Default environment. [cite_start]Low movement cost[cite: 78].

### Dijkstra's Algorithm
[cite_start]Navigation is modeled as a shortest-path problem on a weighted graph using a priority queue[cite: 80, 81].
* [cite_start]**Nodes:** $20 \times 20$ grid cells[cite: 82].
* [cite_start]**Edges:** Connections to adjacent cells (Up, Down, Left, Right)[cite: 83].
* **Weights:** Calculated dynamically:
  $$\text{Weight} = \max(0.01, \text{Base Cost}_{Biome} - \text{Velocity Boost}_{tile})$$
  [cite_start]where velocity boost is retrieved from auxiliary JSON files[cite: 85, 86].

---

##  Results
The solution successfully automates the navigation process with high reliability.

* [cite_start]**Training Accuracy:** > 99% (Converged within 8 epochs) [cite: 95]
* [cite_start]**Inference Speed:** $\approx 2.02s$ per map (including TTA and pathfinding) [cite: 96]
* [cite_start]**Submission Validity:** 100% via GPS Fix and TTA[cite: 89].

---

## 👤 Author
**Ayush Raj**
* [cite_start]**GitHub Repository:** [The Blind Flight - Synapse Drive](https://github.com/ayu853/The-Blind-Flight---Synapse-Drive-PS1-) [cite: 4]

