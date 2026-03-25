# 🧬 Clustering-Based Tissue Classification for Colorectal Cancer Detection

> Evaluating unsupervised clustering algorithms on histological image patches of human colorectal cancer (CRC) tissue using multiple CNN-based feature representations.

Colorectal cancer (CRC) is one of the most commonly diagnosed cancers, affecting the inner walls of the large intestine. Early detection is critical, as the disease typically exhibits no symptoms until its later stages.

This project applies **unsupervised clustering** techniques to 5,000 non-overlapping image patches from hematoxylin & eosin (H&E) stained histological slides of CRC and normal tissue. Four clustering algorithms are benchmarked across two CNN-based feature extractors and two dimensionality reduction strategies.

Although clustering is inherently unsupervised, the dataset includes ground truth labels for nine tissue classes, enabling quantitative validation of clustering performance.

### Tissue Classes

| Label | Tissue Type |
|-------|-------------|
| ADI | Adipose |
| BACK | Background |
| DEB | Debris |
| LYM | Lymphocytes |
| MUC | Mucus |
| MUS | Smooth Muscle |
| NORM | Normal Colon Mucosa |
| STR | Cancer-Associated Stroma |
| TUM | Colorectal Adenocarcinoma Epithelium |

## Dataset

- **Size:** 5,000 colorectal tissue patches
- **Source:** H&E stained histological images
- **Feature Extractors:** PathologyGAN, VGG16, ResNet50, InceptionV3
- **Focus:** PathologyGAN (PGE) and VGG16 feature sets
- **Dimensionality Reduction:** PCA and UMAP (reduced to 100 components)

Feature representations used in this study:

| Feature Set | Dimensionality Reduction |
|-------------|--------------------------|
| PGE_PCA | PathologyGAN + PCA |
| PGE_UMAP | PathologyGAN + UMAP |
| VGG16_PCA | VGG16 + PCA |
| VGG16_UMAP | VGG16 + UMAP |

## Algorithms

### K-Means
A partition-based clustering technique that assigns data points to the nearest centroid and iteratively updates centroids until convergence.

- **Parameter Selection:** Optimal `k` determined via the **Elbow Method** (WCSS plateau), followed by a grid search around the elbow point.

### Gaussian Mixture Model (GMM)
A probabilistic model that assumes clusters follow Gaussian distributions, capable of identifying complex, non-spherical cluster shapes.

- **Parameter Selection:** Number of components selected using **AIC** (preferred over BIC for high-dimensional data). Grid search over covariance types, iterations, and initialisation methods.

### DBSCAN
A density-based algorithm that identifies clusters as dense regions of points and labels sparse areas as noise — robust to outliers.

- **Parameter Selection:** `min_samples` set to at least `2 × dimensions` (≥ 200 for 100-dimensional data). Optimal `eps` identified from the **K-Distance Graph** elbow point, followed by grid search.

### Louvain Community Detection
A greedy modularity-optimisation algorithm that builds a K-NN graph and iteratively merges communities to maximise within-community edges.

- **Parameter Selection:** Resolution `γ` controls cluster granularity (γ < 1 → larger communities; γ > 1 → smaller communities). Optimal resolution selected by jointly analysing Silhouette score, V-Measure, and cluster size entropy.

---

## Evaluation Metrics

| Metric | Range | Description |
|--------|-------|-------------|
| **Silhouette Score** | −1 to +1 | Measures intra-cluster cohesion vs. inter-cluster separation. Higher is better. |
| **V-Measure** | 0 to 1 | Measures alignment between predicted clusters and ground truth labels. Higher is better. |

---

## Results

### PathologyGAN Features

| Feature Type | Metric | K-Means | GMM | DBSCAN | Louvain |
|-------------|--------|---------|-----|--------|---------|
| PCA | Silhouette | 0.258 | 0.005 | 0.329 | 0.138 |
| PCA | V-Measure | 0.279 | 0.349 | 0.329 | 0.372 |
| UMAP | Silhouette | 0.518 | 0.539 | 0.537 | 0.313 |
| UMAP | V-Measure | 0.337 | 0.560 | 0.491 | 0.356 |

### VGG16 Features

| Feature Type | Metric | K-Means | GMM | DBSCAN | Louvain |
|-------------|--------|---------|-----|--------|---------|
| PCA | Silhouette | 0.141 | 0.023 | 0.342 | 0.082 |
| PCA | V-Measure | 0.512 | 0.405 | 0.256 | 0.637 |
| UMAP | Silhouette | 0.668 | 0.533 | 0.674 | 0.409 |
| UMAP | V-Measure | 0.580 | 0.712 | 0.563 | 0.632 |

### Key Findings

- **Best overall configuration:** VGG16 + UMAP, where K-Means, GMM, DBSCAN, and Louvain all achieved strong scores.
- **Louvain** delivered the highest V-Measure on VGG16_PCA (0.637), excelling at recovering ground truth structure in non-linear feature spaces.
- **GMM** achieved the highest V-Measure on VGG16_UMAP (0.712), demonstrating its strength in modelling complex cluster shapes.
- **DBSCAN** performed consistently across both feature extractors, benefiting from VGG16's more discriminative hierarchical features.
- **UMAP** outperformed PCA across all algorithms, confirming that non-linear dimensionality reduction better preserves tissue-type structure.

## Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/anjalireddy1706/Colorectal_Cancer_Tissue_Analysis.git
   cd <repo-name>
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Run the notebook**
   ```bash
   jupyter notebook case_study1_Clustering_Group-193_final.ipynb
   ```
