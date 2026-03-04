# Ground Control Point (GCP) Classification and Detection
**Author:** Atralita Saha  
**Student ID:** MDS202515  

## 1. Project Objective
The goal of this project is to automate the identification of Ground Control Points (GCPs) in high-resolution drone imagery. The system classifies markers into two distinct geometric categories: **Square** and **Cross**, while providing the pixel coordinates ($x, y$) for each detection.

## 2. Dataset & Challenges
* **Class Imbalance:** The training set exhibited a ~9:1 ratio favoring the "Square" class. Simple training would lead to a model that ignores "Crosses" while maintaining high superficial accuracy.
* **High Resolution I/O:** Reading full-resolution drone images from Google Drive during training created a massive bottleneck, initially leading to epoch times exceeding 30 minutes.
* **Spatial Variance:** GCP markers appear at various orientations and lighting conditions across different survey sites.

## 3. Technical Approach

### 3.1 Model Architecture
- **Backbone:** ResNet-18 (Pre-trained on ImageNet).
- **Modification:** The final fully connected layer was replaced with a Linear layer mapping to 2 classes (Square, Cross).
- **Precision:** The model was trained using the Adam optimizer with a learning rate of $1 \times 10^{-5}$ to ensure stable weight updates given the sensitive nature of small marker features.



### 3.2 Data Engineering Pipeline
- **Local Pre-cropping:** To solve the I/O bottleneck, a pre-processing script was implemented to extract $224 \times 224$ patches around known GCP coordinates and save them to the local Colab `/content` SSD. This reduced training time by **~80%**.
- **Augmentation:** Applied random rotations and horizontal flips to the training set to increase the model's robustness against different drone flight headings.



### 3.3 Loss Function Strategy
To combat the class imbalance, I utilized **Weighted Cross-Entropy Loss**. 
- **Square Weight:** 1.0
- **Cross Weight:** 12.0
This penalty forced the model to prioritize learning the features of the "Cross" markers, preventing the model from defaulting to a "always predict Square" strategy.



## 4. Inference Pipeline
For the unlabeled test dataset, the system implements a recursive directory walker (`os.walk`) to handle nested site folders. 
- **Coordinate Logic:** Based on survey standards where the GCP is typically centered in the provided image frame, the script extracts the center $224 \times 224$ patch for classification.
- **Output:** Results are exported to `final_predictions.json` mapping the relative file path to the predicted shape and coordinates.



## 5. Deployment & Weights
The final model weights (`gcp_resnet18_weights_slim.pth`) are saved in **FP16 (Half-Precision)**. 
- **Benefit:** Reduces file size from 44MB to ~22MB (under the 25MB limit) with negligible impact on accuracy, facilitating easier deployment and sharing.

## 6. How to Run
1. Ensure the dataset is mounted via Google Drive at `/content/drive/MyDrive/SkylarkDrones`.
2. Run the **Environment Setup** to define paths.
3. Execute **Section 3.1** to generate local crops (required for speed).
4. Run the **Training Loop** (Section 5).
5. Execute the **Inference Script** (Section 6) to generate the results.
