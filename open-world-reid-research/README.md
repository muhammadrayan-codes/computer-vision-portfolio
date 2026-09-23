# Calibrated Selective Identity Association for Open-World Person Re-ID

### A Three-Way MATCH / DEFER / UNKNOWN Decision Framework

**Author:** Muhammad Rayan
**Research Area:** Computer Vision, Person Re-Identification, Open-World Recognition, Selective Prediction, Model Calibration
**Status:** Manuscript completed, unpublished

---

## Abstract

Person Re-Identification (Re-ID) systems commonly return the nearest gallery identity, even when the correct identity may be absent from the gallery or when multiple candidates are visually similar.

This research investigates a lightweight reliability layer that operates on top of a standard Person Re-ID retrieval pipeline and introduces three possible outcomes:

**MATCH / DEFER / UNKNOWN**

The proposed reliability estimator combines similarity margin, gallery ambiguity, crop quality, and track-level temporal consistency using logistic regression.

The framework is evaluated on **Market-1501** and **PETS09**.

The results show that combining multiple reliability signals substantially improves the reliability of accepted identity matches while allowing uncertain cases to be deferred rather than automatically assigned.

---

## 1. Introduction

Person Re-Identification systems are generally designed to retrieve the most similar identity from a gallery.

A typical system therefore produces a decision such as:

```text
Query Person
     |
     v
Re-ID Embedding
     |
     v
Gallery Retrieval
     |
     v
Top-1 Identity
```

This approach becomes problematic in open-world scenarios.

The correct person may not exist in the gallery, multiple identities may have similar appearances, or the query image may contain a low-quality crop.

In these situations, returning the top-ranked identity does not necessarily mean that the identity association is reliable.

This research therefore explores a selective identity association framework where the system can explicitly distinguish between:

```text
MATCH
DEFER
UNKNOWN
```

Instead of forcing every query into a gallery identity, uncertain cases can be deferred or rejected.

---

# 2. Research Objective

The primary objective is to investigate whether reliability signals available after standard Person Re-ID retrieval can be used to determine whether an identity association should be:

* **MATCH:** sufficiently reliable to accept automatically
* **DEFER:** ambiguous and requiring additional evidence or review
* **UNKNOWN:** likely to represent an identity absent from the gallery

The research focuses on lightweight reliability estimation rather than modifying or retraining the underlying Re-ID model.

---

# 3. Proposed Framework

The proposed framework adds a reliability estimation layer after standard detection, tracking, embedding extraction, and similarity retrieval.

```text
┌─────────────────────┐
│   Person Detection  │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   Person Tracking   │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│  OSNet Re-ID Model  │
└──────────┬──────────┘
           │
           v
┌─────────────────────┐
│   FAISS Retrieval   │
└──────────┬──────────┘
           │
           v
┌─────────────────────────────────────┐
│     Reliability Estimator           │
│                                     │
│  • Similarity Margin                │
│  • Gallery Ambiguity                │
│  • Crop Quality                     │
│  • Track Temporal Consistency       │
└──────────┬──────────────────────────┘
           │
           v
      ┌────┴────┐
      │ Decision│
      └────┬────┘
           │
     ┌─────┼─────┐
     v     v     v
  MATCH  DEFER UNKNOWN
```

The reliability estimator uses logistic regression to combine the available signals into a single reliability probability.

---

# 4. Reliability Signals

## 4.1 Similarity Margin

The similarity margin measures the separation between the selected identity and the strongest competing identity.

```text
Margin =
Top-1 similarity - strongest different-identity similarity
```

A larger margin generally indicates greater separation between the selected identity and its closest competing identity.

Importantly, the competing identity is defined by identity label rather than simply taking the literal rank-2 retrieval result.

---

## 4.2 Gallery Ambiguity

Similarity scores alone do not always capture how ambiguous a gallery is.

The framework therefore incorporates an entropy-based ambiguity measure derived from the strongest competing identity scores.

The temperature parameter was dataset-specific:

| Dataset     | Temperature |
| ----------- | ----------: |
| Market-1501 |       0.005 |
| PETS09      |       0.002 |

This provides an additional signal describing how concentrated or ambiguous the candidate identities are.

---

## 4.3 Crop Quality

Image quality can influence Re-ID reliability.

A Laplacian-variance measure is used as a proxy for crop sharpness.

The framework uses the minimum quality between the query and gallery crop:

```text
Crop Quality = min(Query Quality, Gallery Quality)
```

For the PETS09 reliability model, crop quality provided negligible additional information and was therefore excluded from the final model.

---

## 4.4 Track-Level Temporal Consistency

When video tracks are available, individual frame predictions can be combined into a temporal signal.

Track-level temporal consistency is defined as the fraction of frames that agree with the majority identity assigned to the track.

```text
Temporal Consistency =
Frames agreeing with track majority
-----------------------------------
Total frames in track
```

This provides information that is unavailable in isolated image-based Re-ID.

In the experiments, temporal consistency was the strongest additional reliability signal beyond similarity margin in the video-track setting.

---

# 5. Reliability Estimator

The reliability signals are standardized and combined using logistic regression.

The resulting probability is used as a reliability measure for the retrieved identity.

The model is intentionally lightweight and operates after the existing Re-ID retrieval pipeline.

The underlying detection, tracking, and Re-ID models are not modified.

---

# 6. Experimental Pipeline

The research uses a separate experimental pipeline from the Cross-Camera Surveillance System presented elsewhere in this portfolio.

```text
YOLO26x
   |
   v
BoTSORT
   |
   v
OSNet
   |
   v
FAISS
   |
   v
Reliability Estimator
   |
   v
MATCH / DEFER / UNKNOWN
```

## Components

| Component           | Purpose                 |
| ------------------- | ----------------------- |
| YOLO26x             | Person detection        |
| BoTSORT             | Multi-object tracking   |
| OSNet               | Person Re-ID embeddings |
| FAISS               | Similarity retrieval    |
| Logistic Regression | Reliability estimation  |

OSNet uses the `osnet_x1_0` model with 512-dimensional embeddings.

FAISS uses an `IndexFlatIP` index over L2-normalized embeddings.

---

# 7. Datasets

## 7.1 Market-1501

Market-1501 is used to evaluate the framework in an image-based Person Re-ID setting.

The experiments investigate identity association reliability and open-world behavior using the Market-1501 identity gallery.

### Important Evaluation Consideration

The OSNet model used in the experiment was pretrained on Market-1501.

Therefore, the Market-1501 experiment should not be interpreted as a fully independent held-out benchmark.

---

## 7.2 PETS09

PETS09 is used to investigate reliability in a multi-camera video setting.

A crop-level benchmark was constructed from the video data using:

```text
PETS09 Video
     |
     v
YOLO Detection
     |
     v
BoTSORT Tracking
     |
     v
OSNet Embeddings
     |
     v
Manual Identity Correction
     |
     v
Person Crop Benchmark
```

The resulting benchmark contains approximately **4,501 person crops**.

### Dataset Construction Limitation

The same detection and tracking pipeline used to construct the PETS09 benchmark was also used during evaluation.

Identity errors were manually corrected by a single annotator.

These factors introduce potential sources of evaluation bias and should be considered when interpreting the results.

---

# 8. Evaluation Protocol

The query set was divided into fitting and evaluation subsets at the query level.

The split was stratified according to retrieval correctness.

For the video-track experiment, frames from the same track could appear in both subsets.

This is an important limitation because temporal correlation between frames can make the evaluation more optimistic than a completely track-disjoint split.

---

# 9. MATCH Decision

A MATCH threshold is used to determine when the reliability estimate is sufficiently high for automatic identity acceptance.

The threshold was selected on the evaluation set to achieve approximately **99% accuracy**.

| Dataset     | MATCH Threshold | Accuracy | Coverage |
| ----------- | --------------: | -------: | -------: |
| PETS09      |          0.9328 |   99.45% |    59.0% |
| Market-1501 |          0.9476 |   99.12% |    83.0% |

Here, **coverage** represents the proportion of queries automatically accepted as MATCH.

The remaining queries are not automatically accepted and can enter the DEFER / UNKNOWN analysis.

---

# 10. Open-World Evaluation

A separate leave-one-identity-out procedure was used to investigate open-world behavior.

The objective was to examine whether cases where the true identity is absent from the gallery could be separated from ambiguous but potentially valid matches.

### PETS09

All 10 identities were evaluated in the leave-one-identity-out experiment.

### Market-1501

A sample of 60 identities was used from approximately 750 identities.

---

# 11. DEFER and UNKNOWN

The framework separates uncertain cases into two concepts:

### DEFER

The system does not have sufficient evidence to confidently accept the retrieved identity.

The case may still contain a valid gallery identity, but additional evidence or human review is required.

### UNKNOWN

The system estimates that the true identity may not exist in the available gallery.

The experiments investigated whether these two populations could be empirically separated.

---

# 12. DEFER Composition

The composition of deferred cases was analyzed at different containment levels.

The percentages represent real-match and absent-identity cases within the DEFER population.

### PETS09

| Containment | Real Match | Absent Identity |
| ----------- | ---------: | --------------: |
| 80%         |      36.8% |           63.2% |
| 85%         |      41.6% |           58.4% |
| 90%         |      46.4% |           53.6% |
| 95%         |      56.0% |           44.0% |

### Market-1501

| Containment | Real Match | Absent Identity |
| ----------- | ---------: | --------------: |
| 80%         |      73.3% |           26.7% |
| 85%         |      77.4% |           22.6% |
| 90%         |      82.6% |           17.4% |
| 95%         |      80.0% |          20.0%* |

*The 95% Market-1501 condition contained only 10 items, so the estimate is based on a very small sample.

---

# 13. UNKNOWN Threshold Analysis

The study also examined the cost of containing the UNKNOWN class at different operating points.

| Containment | PETS09 | Market-1501 |
| ----------- | -----: | ----------: |
| 80%         |  18.93 |        9.69 |
| 85%         |  21.11 |       10.78 |
| 90%         |  25.46 |       12.42 |
| 95%         |  30.90 |       16.54 |

These results show different open-world behavior across the two datasets.

The experiments do not support the use of a single universal UNKNOWN threshold across both datasets.

---

# 14. Failure Mode Analysis

## 14.1 PETS09

The PETS09 false-match behavior was highly concentrated.

Two of the ten identities accounted for the overwhelming majority of the observed false matches.

These identities were incorrectly associated with the same gallery identity in approximately:

* **96.7% of frames** for one identity
* **89.0% of frames** for the other

Both identities also exhibited lower image quality / greater blur.

This suggests that a small number of visually similar or low-quality identities can dominate the failure behavior in a small gallery.

---

## 14.2 Market-1501

The Market-1501 false matches showed a more diffuse pattern.

Among the sampled identities, false matches were distributed across approximately ten different gallery identities.

This behavior is different from the concentrated failure mode observed in the smaller PETS09 identity set.

The difference suggests that gallery scale and identity diversity can influence the behavior of open-world rejection.

---

# 15. Main Results

The primary results demonstrate that the reliability layer can substantially increase the accuracy of automatically accepted identity associations while selectively rejecting uncertain cases.

### PETS09

```text
Accuracy: 99.45%
Coverage: 59.0%
```

### Market-1501

```text
Accuracy: 99.12%
Coverage: 83.0%
```

The results also indicate that:

1. Raw similarity is not sufficiently calibrated to represent identity correctness.
2. Similarity margin provides useful reliability information.
3. Track-level temporal consistency adds valuable information in video settings.
4. DEFER and UNKNOWN exhibit distinguishable behavior.
5. Open-world failure modes depend strongly on the dataset and gallery structure.
6. A single universal UNKNOWN threshold is not supported by the current experiments.

---

# 16. Limitations

The current study has several limitations.

### Dataset Size

The PETS09 evaluation uses a relatively small identity set.

### Benchmark Construction

The PETS09 crop benchmark was generated using the same detection and tracking pipeline used for evaluation.

### Manual Annotation

PETS09 identity corrections were performed by a single annotator.

### Data Leakage / Correlation

The fitting and evaluation split was performed at the query level.

Frames from the same video track could therefore appear in both subsets.

### Market-1501 Pretraining

The OSNet model used for Market-1501 experiments was pretrained on the same dataset.

### Parameter Tuning

Some parameters, including ambiguity temperatures, were tuned using the full query set before the final fit/evaluation split.

### UNKNOWN Threshold

The experiments did not identify a single satisfactory UNKNOWN threshold that transferred consistently between datasets.

---

# 17. Conclusion

This research presents a lightweight selective identity association framework for Person Re-ID that extends conventional top-1 retrieval with three possible decisions:

```text
MATCH
DEFER
UNKNOWN
```

The approach combines similarity margin, gallery ambiguity, crop quality, and temporal consistency using logistic regression.

The experiments on Market-1501 and PETS09 demonstrate that these additional signals can improve the reliability of automatically accepted identity matches.

The results also highlight the importance of temporal information in video-based Re-ID and show that open-world rejection behavior can vary substantially with dataset and gallery structure.

Rather than treating every top-1 retrieval as a valid identity, the framework provides a mechanism for explicitly representing uncertainty and allowing ambiguous cases to remain unresolved.

---

# 18. Future Work

Future research directions include:

* Evaluation on larger multi-camera video datasets
* Track-disjoint evaluation protocols
* Larger-scale open-world identity experiments
* Dataset-independent UNKNOWN thresholding
* Improved absolute resemblance features
* Additional calibration techniques
* Track-level uncertainty modeling
* More robust open-set Person Re-ID benchmarks
* Evaluation across different Re-ID architectures
* Human-in-the-loop investigation workflows

---

# 19. References

1. Chow, C. K. (1970). On optimum recognition error and reject tradeoff.

2. Brier, G. W. (1950). Verification of forecasts expressed in terms of probability.

3. Liao, S., Hu, Y., Zhu, X., & Li, S. Z. (2015). Person re-identification by local maximal occurrence representation and metric learning.

4. Hou, Y., et al. (2021). Learning a unified embedding for person re-identification.

5. Naeini, M. P., Cooper, G. F., & Hauskrecht, M. (2015). Obtaining well calibrated probabilities using Bayesian binning.

6. Platt, J. (1999). Probabilistic outputs for support vector machines and comparisons to regularized likelihood methods.

---

# 20. Research Status

**Manuscript completed and currently unpublished.**

The work is being developed as an independent Computer Vision research project focused on reliability and selective decision-making in open-world Person Re-Identification.

