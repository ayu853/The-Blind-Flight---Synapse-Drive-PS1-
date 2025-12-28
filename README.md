# The-Blind-Flight---Synapse-Drive-PS1-

![Language](https://img.shields.io/badge/Language-Python_3.11-blue?style=for-the-badge&logo=python)
![Framework](https://img.shields.io/badge/Framework-PyTorch_2.0-red?style=for-the-badge&logo=pytorch)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)




##  Project Overview
**The Blind Flight** is an autonomous navigation system developed for the Synapse Drive challenge. The objective is to navigate an agent from a **Start Point (Index 3)** to an **End Point (Index 4)** on a $20 \times 20$ grid map.

**The Challenge:** The test maps are provided as raw images without any labels. The terrain (obstacles, roads, boosters) must be visually recognized by the AI before a path can be calculated.

**The Solution:** This project implements a **Vision-Language-Planning** pipeline:
1.  **Vision:** An ensemble of ResNet-18 and EfficientNet-B0 classifies map tiles.
2.  **Logic:** Heuristic algorithms detect "Biomes" (Forest/Desert/Lab) to determine movement costs.
3.  **Planning:** Dijkstra's algorithm computes the optimal path, accounting for velocity boosts and terrain friction.

---

##  Key Features
* **Ensemble Neural Networks:** Averages predictions from two distinct architectures (ResNet + EfficientNet) to maximize classification accuracy (>99%).
* **Test-Time Augmentation (TTA):** Uses horizontal flipping during inference to eliminate orientation bias.
* **Robust "GPS Fix":** A fail-safe mechanism that forces valid Start/End points onto the grid if the model misses them, ensuring 100% submission validity.
* **Adaptive Biome Detection:** Uses pixel-level color analysis to classify the global environment and adjust traversal weights dynamically.
* **Class Imbalance Handling:** Implements `WeightedRandomSampler` during training to ensure rare classes (Start/End points) are learned effectively.

