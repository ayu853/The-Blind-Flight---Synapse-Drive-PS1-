# The-Blind-Flight---Synapse-Drive-PS1-

![Language](https://img.shields.io/badge/Language-Python_3.11-blue?style=for-the-badge&logo=python)
![Framework](https://img.shields.io/badge/Framework-PyTorch_2.0-red?style=for-the-badge&logo=pytorch)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

##  Table of Contents
1. [Project Overview](#-project-overview)
2. [Key Features](#-key-features)
3. [Directory Structure](#-directory-structure)
4. [Prerequisites & Installation](#-prerequisites--installation)
5. [How to Run](#-how-to-run)
6. [Technical Architecture](#-technical-architecture)
    - [Data Pipeline](#1-data-pipeline-slicing--preprocessing)
    - [Deep Learning Models](#2-deep-learning-ensemble)
    - [Inference Strategy (TTA & GPS Fix)](#3-inference-strategy-tta--gps-fix)
    - [Pathfinding Logic (Biomes & Dijkstra)](#4-pathfinding-logic)
7. [Results](#-results)
8. [Author](#-author)

---

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

