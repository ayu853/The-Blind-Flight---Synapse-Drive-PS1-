# The-Blind-Flight---Synapse-Drive-PS1-

# 🚁 The Blind Flight: Synapse Drive PS 1

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-red)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📖 Overview
**The Blind Flight** is an autonomous pathfinding project designed to navigate a grid-based map where the terrain is visually distinct but unlabeled. 

This solution employs a **hybrid Artificial Intelligence approach**, combining **Deep Learning (Ensemble CNNs)** for visual terrain recognition and **Graph Theory (Dijkstra's Algorithm)** for optimal route planning. The system is robust against noisy data, utilizing advanced inference techniques like Test-Time Augmentation (TTA) and heuristic post-processing.

---

## 🚀 Key Features
* **🧠 Deep Ensemble Learning:** Combines predictions from **ResNet-18** and **EfficientNet-B0** to achieve >99% classification accuracy on terrain tiles.
* **🔄 Test-Time Augmentation (TTA):** Uses horizontal flipping during inference to smooth out model variance and improve prediction stability.
* **🌍 Biome Detection:** Automatically detects the map environment (Forest, Desert, or Lab) using pixel-level color analysis to dynamically adjust movement costs.
* **📍 GPS Fix Algorithm:** A custom heuristic that ensures valid Start and End points exist on the grid, preventing pathfinding failures even if the model misses a detection.
* **⚡ Smart Pathfinding:** Implements Dijkstra's algorithm with variable edge weights based on terrain friction and velocity boosts.

---

## 🛠️ Tech Stack
* **Language:** Python 3.x
* **Deep Learning:** PyTorch, Torchvision
* **Image Processing:** PIL (Pillow), NumPy
* **Data Handling:** Pandas, CSV, JSON
* **Progress Tracking:** Tqdm

---

## 📂 Project Structure

