# Cross-Camera Surveillance System

An end-to-end Computer Vision system for detecting, tracking, and re-identifying people across multiple surveillance camera views.

The system combines **YOLOv12**, **StrongSORT**, **OSNet**, **FAISS**, and **Qwen3-VL-2B** to perform person detection, multi-object tracking, appearance-based identity matching, and visual verification of ambiguous matches.

## Overview

The system is designed for post-incident CCTV investigation where an investigator may need to follow the movement of a person across multiple camera views.

Instead of processing each camera independently, tracked individuals are represented using appearance embeddings that can be searched across camera views.

Ambiguous identity matches can then be further examined using a Visual Language Model.

## Pipeline

```text
CCTV Video
     |
     v
YOLOv12 Person Detection
     |
     v
StrongSORT Multi-Object Tracking
     |
     v
OSNet Re-ID Embeddings
     |
     v
FAISS Similarity Search
     |
     v
Cross-Camera Identity Matching
     |
     v
Qwen3-VL-2B Verification

Computer Vision Components
YOLOv12

YOLOv12 is used for person detection in surveillance frames.

The detector provides person bounding boxes that are passed to the tracking pipeline.

StrongSORT

StrongSORT performs multi-object tracking and maintains persistent track identities within each camera stream.

The tracker also makes use of appearance information for maintaining identities through the video.

OSNet

OSNet is used as the person Re-ID feature extractor.

For each detected person, the model produces a 512-dimensional appearance embedding that represents visual characteristics useful for cross-camera identity matching.

FAISS

FAISS provides efficient similarity search over the extracted person embeddings.

The system uses the embedding space to retrieve candidate identities from previously observed camera views.

Qwen3-VL-2B

Qwen3-VL-2B provides an additional visual reasoning stage for ambiguous identity matches.

It is used as a verification mechanism when appearance-based retrieval alone does not provide sufficient confidence.

Key Features
YOLOv12 person detection
StrongSORT multi-object tracking
OSNet person Re-ID
512-dimensional appearance embeddings
FAISS vector similarity search
Cross-camera identity association
Qwen3-VL-2B visual verification
Multi-camera video processing
Python-based Computer Vision pipeline
Evaluation

The system was evaluated using standard Person Re-ID metrics:

Rank-1
Rank-5
Rank-10
mAP

The evaluation included comparisons between the baseline Re-ID pipeline and the enhanced pipeline incorporating visual reasoning.

Technologies
Python
PyTorch
YOLOv12
StrongSORT
OSNet
FAISS
Qwen3-VL-2B
OpenCV
BoxMOT
TrackEval
HuggingFace Transformers
Project Status

Completed as part of a Final Year Project at FAST-NUCES Karachi.

The original project repository was maintained as a private repository during development.

Source Code

https://github.com/muhammadrayan-codes/Cross-Camera-Surveillance-Full-Stack-System
