# Ground Control Point (GCP) Classification
Student Name: Atralita Saha
Student ID: MDS202515
### 1. Project Overview
This project focuses on the automated detection and classification of Ground Control Points (GCPs) from high-resolution drone imagery. The objective is to identify whether a marker is a Square or a Cross and provide its coordinates.
### 2. MethodologyModel Architecture: 
Used a ResNet-18 backbone with Transfer Learning (pre-trained on ImageNet). The final fully connected layer was modified to output 2 classes.Handling Class Imbalance: The training data was heavily skewed toward "Squares." To ensure the model learned to identify "Crosses" effectively, I implemented a Weighted Cross-Entropy Loss with a weight factor of 12.0 for the minority class.Pipeline Optimization: To overcome Google Drive's slow I/O speed, I developed a Local Pre-cropping Pipeline. This script pre-processed images into $224 \times 224$ patches on the local SSD, reducing training time from ~37 minutes to under 8 minutes.Precision: Used a low learning rate of 0.00001 with the Adam optimizer to ensure stable convergence on the specific marker features.
### 3. Inference Strategy
For the unlabeled test dataset, the project utilizes a recursive directory walker. Since GCPs are typically centered in these survey images, a center-cropping strategy was applied to extract the marker region for classification.
### 4. Repository Contents
MDS202515_GCP_ASSIGNMENT2.ipynb: Complete source code and execution logs.final_predictions.json: The final output containing image paths, predicted shapes, and $(x, y)$ coordinates.
gcp_resnet18_weights_slim.pth: Trained model weights, optimized to ~22 MB using FP16 half-precision for easier portability and to meet repository size limits.
