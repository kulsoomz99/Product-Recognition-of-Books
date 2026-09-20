# Product Recognition of Books
**Image Processing and Computer Vision**

## 📖 Overview
This project implements a robust computer vision pipeline to detect, localize, and count multiple instances of specific books within cluttered scene images. By leveraging **SIFT (Scale-Invariant Feature Transform)**, **homography estimation**, and **RANSAC-based geometric verification**, the system accurately identifies books under varying viewpoints, scales, and lighting conditions.

---

## 🛠️ Methodology

### 1. Image Preprocessing
To ensure reliable feature extraction, scene and model images undergo several preprocessing steps:
* **Blur Detection:** Uses the variance of the Laplacian operator to identify blurry images (threshold = `100`).
* **CLAHE:** Applies Contrast Limited Adaptive Histogram Equalization to enhance local contrast without amplifying noise.
* **Sharpening & Blurring:** Accentuates edges for SIFT while applying a mild Gaussian blur to reduce spurious noise keypoints.

### 2. Feature Extraction & Matching
* **SIFT:** Extracts distinctive keypoints and descriptors that are invariant to scale and rotation.
* **BFMatcher & Lowe’s Ratio Test:** Matches descriptors using a Brute-Force Matcher. Ambiguous matches are filtered out using Lowe’s ratio test (threshold = `0.75`).

### 3. Geometric Verification
* **Homography & RANSAC:** Computes the homography matrix using RANSAC (reprojection threshold = `3 pixels`) to map the book model's bounding box onto the scene, effectively rejecting outlier matches.
* **Geometry Validation:** Filters out implausible detections by verifying the bounding box's aspect ratio, minimum size, and boundary constraints.

### 4. Multiple Instance Detection & NMS
* **Iterative Detection:** To find multiple copies of the same book, the algorithm iteratively removes matched inlier keypoints from the scene descriptors (capped at 5 instances per model).
* **Non-Maximum Suppression (NMS):** Utilizes OpenCV’s optimized `cv2.dnn.NMSBoxes` with an IoU threshold of `0.3` to suppress duplicate, overlapping bounding boxes.

### 5. Orientation & Visualization
* Calculates the average rotation angle of the bounding box.
* Draws **green** bounding boxes for horizontal books (within ±10°) and **red** for tilted books.
* Labels each detection with the Book ID and confidence score.

---

## 📊 Evaluation

The system is evaluated using an **instance-based approach** against manually annotated ground truth counts. 

### Metrics
* **True Positives (TP):** Correct detections matching the ground truth count.
* **False Positives (FP):** Extra detections (false alarms).
* **False Negatives (FN):** Missed ground truth instances.
* **Precision:** $TP / (TP + FP)$
* **Recall:** $TP / (TP + FN)$
* **F1 Score:** Harmonic mean of Precision and Recall.

### Results
| Metric | Value |
| :--- | :--- |
| **Total Ground Truth Instances** | 38 |
| **Total Detected Instances** | 40 |
| **Correctly Detected (TP)** | 33 |
| **Precision** | **0.82** |
| **Recall** | **0.87** |
| **F1 Score** | **0.85** |

*The results validate the soundness of the methodology, demonstrating a strong balance between detection accuracy and completeness in realistic, cluttered scenarios.*

---

## 📦 Dependencies
* Python 3.x
* `opencv-python` (cv2)
* `numpy`
* `matplotlib`
* Standard libraries: `os`, `collections`

---

## 🚀 Usage
1. **Directory Setup:** Ensure you have a `models/` directory (containing individual book cover images) and a `scenes/` directory (containing the cluttered scene images).
2. **Update Paths:** Modify the `MODELS_DIR` and `SCENES_DIR` variables in the notebook to point to your local or cloud storage paths.
3. **Run Notebook:** Execute the Jupyter Notebook cells sequentially to:
   * Load and preprocess model features.
   * Detect and visualize instances in scene images.
   * Compute and print the final evaluation metrics.
