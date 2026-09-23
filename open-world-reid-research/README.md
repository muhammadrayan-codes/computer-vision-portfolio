
# `open-world-reid-research/README.md`

```markdown
# Calibrated Selective Identity Association for Open-World Person Re-ID

### A Three-Way MATCH / DEFER / UNKNOWN Decision Framework

**Author:** Muhammad Rayan

**Research Area:** Computer Vision, Person Re-Identification, Open-World Recognition, Selective Prediction, Model Calibration

**Status:** Manuscript completed, unpublished

## Abstract

Person Re-Identification systems commonly return the nearest gallery identity even when the correct identity may be absent from the gallery or when multiple candidates are visually similar.

This research investigates a lightweight reliability layer that operates on top of an existing Person Re-ID retrieval pipeline and introduces three possible outcomes:

```text
MATCH
DEFER
UNKNOWN

The proposed reliability estimator combines similarity margin, gallery ambiguity, crop quality, and track-level temporal consistency using logistic regression.

The approach was evaluated on Market-1501 and PETS09.

Research Objective

The research investigates whether a Person Re-ID system can distinguish between:

Confident identity matches
Ambiguous identity associations
Cases where the correct identity is absent from the gallery

Instead of forcing every query into a gallery identity, the system introduces selective decisions that allow uncertain cases to be deferred or rejected.

Method

The reliability layer operates after the standard detection, tracking, embedding, and retrieval stages.

Person Detection
       |
       v
Person Tracking
       |
       v
OSNet Re-ID Embedding
       |
       v
FAISS Retrieval
       |
       v
Reliability Estimator
       |
       +------> MATCH
       |
       +------> DEFER
       |
       +------> UNKNOWN

Reliability Signals
Similarity Margin

The difference between the selected identity's similarity score and the strongest competing identity.

Gallery Ambiguity

An entropy-based measure describing how ambiguous the strongest competing gallery identities are.

Crop Quality

A Laplacian-variance based measure of image sharpness used as a proxy for crop quality.

Track-Level Temporal Consistency

For video tracks, the fraction of frames agreeing with the track's majority identity.

This provides temporal evidence beyond individual frame-level similarity.

Reliability Estimator

The four signals are used to construct a lightweight reliability estimator based on logistic regression.

The estimator is applied after retrieval and does not modify the underlying Re-ID model.

Datasets
Market-1501

Market-1501 was used to evaluate the reliability framework in an image-based Person Re-ID setting.

PETS09

PETS09 was used to investigate reliability in a multi-camera video setting.

A video-track benchmark was constructed using the detection and tracking pipeline, followed by manual correction of identity errors.

The resulting benchmark contains approximately 4,500 person crops.

Results
Market-1501

99.12% accuracy at 83.0% coverage

PETS09

99.45% accuracy at 59.0% coverage

The experiments also showed that track-level temporal consistency provided the strongest additional reliability signal beyond similarity margin in the video-track evaluation.

Key Findings
Raw Similarity Is Poorly Calibrated

Raw Re-ID similarity scores do not directly provide a reliable measure of whether the retrieved identity is correct.

Temporal Consistency Adds Useful Information

Track-level temporal consistency provides additional reliability information beyond individual image similarity.

DEFER and UNKNOWN Are Distinct

The experiments investigate the separation between ambiguous matches and cases where the correct identity is absent from the gallery.

Open-World Failure Modes Differ

The PETS09 experiments showed concentrated false matches involving a small number of identities, while the Market-1501 experiments showed a more diffuse pattern across multiple gallery identities.

The results indicate that a single universal UNKNOWN threshold may not be sufficient across different datasets.

Research Pipeline

The experiments in this research used a separate pipeline from the main Cross-Camera Surveillance System:

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
Calibrated Reliability Estimator
   |
   v
MATCH / DEFER / UNKNOWN

Limitations
PETS09 contains a relatively small number of identities.
The PETS09 benchmark was constructed using the same detection and tracking pipeline used during evaluation.
Identity corrections were performed manually by a single annotator.
Some parameters were tuned using the full query set.
PETS09 video tracks can contribute frames to both fitting and evaluation subsets.
The OSNet model used for Market-1501 evaluation was pretrained on Market-1501.
A single UNKNOWN threshold did not provide consistent behavior across datasets.
Future Work
Evaluation on larger multi-camera video datasets
Larger-scale open-world identity evaluation
Dataset-independent UNKNOWN thresholding
Improved absolute resemblance features
Track-level uncertainty modeling
Additional calibration methods
More robust open-set Person Re-ID benchmarks
