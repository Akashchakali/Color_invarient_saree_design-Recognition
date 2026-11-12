# Color-Invariant Saree Design Recognition

## 1. Project Overview

This project develops a deep-learning-based saree pattern retrieval system that aims to identify visually similar saree patterns while reducing dependence on color variations.

The system uses a convolutional neural network to generate compact image embeddings. Given a query saree image, it compares its embedding with gallery image embeddings using cosine similarity and ranks the most similar results.

**Main objective:** Encourage the model to recognize saree pattern characteristics rather than relying primarily on color information.

## 2. Objectives

- Develop a color-robust saree pattern representation.
- Generate compact image embeddings using deep learning.
- Perform similarity-based image retrieval.
- Evaluate identification and verification performance.
- Measure model efficiency using parameters, embedding size, and inference latency.

## 3. Datasets

Two datasets specified in the assignment were inspected:

### Indian Saree Patterns Dataset
- Total images: 1,468
- Categories:
  - Banarasi
  - Bandhani
  - Ikat
  - Pichwai
- Training images: 1,293
- Validation images: 115
- Testing images: 60

This dataset was used for model training and evaluation because it provided category labels.

### DeepLure Handloom Saree Dataset
- Handloom subset inspected: 165 images.
- Inspected as part of the dataset exploration.
- Not used for model training or evaluation because reliable individual design labels were unavailable.

**Dataset note:** The original datasets are not included in this project package. Refer to the assignment-provided sources and their applicable access and usage conditions.

## 4. Model Architecture

The project uses a pretrained ResNet-18 backbone.

The original classification layer was replaced with a projection head:

ResNet-18 → Linear(512, 256) → ReLU → Linear(256, 128)

The final 128-dimensional embeddings are L2-normalized before similarity comparison.

- Backbone: ResNet-18
- Embedding dimension: 128
- Total parameters: 11,340,736
- Trainable parameters: 11,340,736
- Embedding size: 512 bytes per image

## 5. Preprocessing and Augmentation

Images are converted to RGB and resized/cropped to 224 × 224 pixels.

Training augmentations include:

- Random Resized Crop
- Random Horizontal Flip
- ColorJitter
  - Brightness
  - Contrast
  - Saturation
  - Hue

Strong color augmentation encourages the model to learn representations that are less sensitive to color changes.

Image normalization uses the standard ImageNet mean and standard deviation.

## 6. Training Strategy

Self-supervised contrastive learning is used.

Two independently augmented views are generated from each training image. These views form a positive pair, while other images in the mini-batch act as negative examples.

**Training configuration:**

| Parameter | Value |
|---|---|
| Framework | PyTorch |
| Backbone | ResNet-18 |
| Loss Function | NT-Xent Contrastive Loss |
| Temperature | 0.2 |
| Optimizer | AdamW |
| Learning Rate | 0.0001 |
| Weight Decay | 0.0001 |
| Batch Size | 32 |
| Epochs | 5 |
| Embedding Dimension | 128 |

## 7. Evaluation Methodology

Two evaluation settings were performed.

### A. Controlled Color-Invariance Evaluation

Original test images were retained in the gallery, while color-transformed versions of the same images were used as queries.

This evaluates embedding consistency under controlled brightness, contrast, saturation, and hue changes.

Identification metrics:
- Top-1 accuracy
- Top-5 accuracy
- Top-10 accuracy

Verification was evaluated using positive and hardest-negative similarities with a selected similarity threshold.

### B. Disjoint Gallery/Query Evaluation

The 60 test images were divided into:

- Gallery images: 29
- Query images: 31

No exact query image was included in the gallery.

The evaluation included:
- Top-1 retrieval accuracy
- Top-5 retrieval accuracy
- Top-10 retrieval accuracy
- Confusion matrix
- Per-category precision, recall, and F1-score

This is the primary category-level retrieval evaluation.

## 8. Results

### Controlled Color-Invariance Evaluation

| Metric | Result |
|---|---:|
| Top-1 | 100.00% |
| Top-5 | 100.00% |
| Top-10 | 100.00% |
| Verification Accuracy | 93.33% |
| Selected Threshold | 0.897 |

### Disjoint Gallery/Query Evaluation

| Metric | Result |
|---|---:|
| Top-1 | 70.97% |
| Top-5 | 96.77% |
| Top-10 | 100.00% |

### Efficiency

| Metric | Result |
|---|---:|
| Total Parameters | 11,340,736 |
| Embedding Dimension | 128 |
| Embedding Size | 512 bytes |
| Average GPU Latency | 2.809 ms/image |
| Throughput | 355.97 images/second |

## 9. Project Files

The project package contains:

- `color_invariant_saree_design_recognition.ipynb` — Complete implementation, training, inference, and evaluation.
- `saree_design_recognition_final.pth` — Trained model checkpoint.
- `saree_design_recognition_results.csv` — Controlled evaluation and efficiency results.
- `saree_disjoint_evaluation_results.csv` — Disjoint gallery/query evaluation results.
- `README.md` — Project documentation.

## 10. How to Run

1. Open the Kaggle notebook.
2. Make sure the Indian Saree Patterns dataset is available in the Kaggle environment.
3. Enable GPU acceleration if available.
4. Execute the notebook cells from beginning to end.
5. The notebook performs preprocessing, contrastive training, embedding generation, retrieval, and evaluation.
6. The trained checkpoint and evaluation CSV files are saved in `/kaggle/working/`.

The notebook contains the complete implementation and the dataset paths used during development. Update dataset paths if running in a different environment.

## 11. Limitations

- The available datasets do not provide reliable individual saree-design IDs.
- Therefore, the evaluation primarily measures category-level retrieval and controlled color invariance rather than independent matching of the same individual design across different color palettes.
- The controlled verification threshold was selected using the same controlled test pairs, so its accuracy should not be interpreted as an independent verification benchmark.
- Some visually similar patterns may be assigned to different categories, and some different designs may share the same category.

The DeepLure handloom subset was inspected but not used for training or evaluation.

## 12. Conclusion

The project demonstrates a contrastive-learning-based approach to color-robust saree pattern representation and similarity-based retrieval.

The ResNet-18 model generates compact 128-dimensional embeddings and achieves 70.97% Top-1, 96.77% Top-5, and 100% Top-10 accuracy in the disjoint category-level retrieval evaluation.

The results demonstrate the potential of deep-learning-based image embeddings for saree pattern retrieval while highlighting the need for reliable individual design labels for stronger design-level evaluation.

---

**Technologies Used:** Python, PyTorch, Torchvision, NumPy, Pandas, Matplotlib, Scikit-learn, Kaggle Notebook.