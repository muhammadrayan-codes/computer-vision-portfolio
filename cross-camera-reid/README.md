# Cross-Camera Surveillance System

An end-to-end Computer Vision surveillance system for **multi-camera person tracking, cross-camera person re-identification, and visual identity verification**.

The system combines **YOLOv12, StrongSORT, OSNet, FAISS, and Qwen3-VL-2B** to detect and track people within video streams, generate appearance embeddings, search for matching identities across cameras, and verify ambiguous identity associations.

---

## 1. Overview

In a multi-camera surveillance environment, the same person can appear across different camera views with changes in:

* Camera angle
* Lighting
* Background
* Scale
* Pose
* Viewing direction
* Occlusion

The system addresses this by combining object detection, multi-object tracking, person re-identification, vector similarity search, and visual-language-model verification.

The overall workflow is:

```text
Camera 1 ──┐
Camera 2 ──┤
Camera 3 ──┤
            │
            v
      YOLOv12 Detection
            |
            v
      StrongSORT Tracking
            |
            v
      OSNet Re-ID Embeddings
            |
            v
       FAISS Search
            |
            v
   Cross-Camera Candidates
            |
            v
    Qwen3-VL-2B Verification
            |
            v
     Identity Association
```

---

# 2. Objective

The primary objective is to build a system capable of following people across different camera views by combining:

* Person detection
* Multi-object tracking
* Person Re-ID
* Appearance embeddings
* Vector similarity search
* Visual verification

Rather than relying only on the track identity generated within an individual camera, the system uses visual appearance representations to associate the same person across different camera streams.

---

# 3. System Architecture

The system consists of a frontend, backend, Computer Vision pipeline, vector retrieval component, and visual verification component.

```text
                         ┌──────────────────┐
                         │   React / Vite   │
                         │    Frontend      │
                         └────────┬─────────┘
                                  |
                                  v
                         ┌──────────────────┐
                         │     FastAPI      │
                         │     Backend      │
                         └────────┬─────────┘
                                  |
                                  v
                         ┌──────────────────┐
                         │  CV Processing   │
                         │     Pipeline     │
                         └────────┬─────────┘
                                  |
                    ┌─────────────┼─────────────┐
                    |             |             |
                    v             v             v
                 YOLOv12      StrongSORT      OSNet
                    |             |             |
                    |             |             v
                    |             |       Re-ID Embeddings
                    |             |             |
                    |             +-------------+
                    |                           |
                    v                           v
              Person Boxes                  FAISS
                                              |
                                              v
                                    Candidate Identities
                                              |
                                              v
                                      Qwen3-VL-2B
                                              |
                                              v
                                      Verification
```

---

# 4. Computer Vision Pipeline

## 4.1 Person Detection

**YOLOv12** is used to detect people in incoming video frames.

The detector produces bounding boxes that are passed to the tracking stage.

```text
Video Frame
     |
     v
YOLOv12
     |
     v
Person Bounding Boxes
```

---

## 4.2 Multi-Object Tracking

**StrongSORT** is used to maintain individual track identities within each camera stream.

The tracker associates detections across consecutive frames and maintains a persistent track for each detected person.

```text
Frame t
   |
   v
Detections
   |
   v
StrongSORT
   |
   v
Track IDs

Frame t+1
   |
   v
Detections
   |
   v
StrongSORT
   |
   v
Updated Tracks
```

This allows the system to build continuous observations of people within a camera.

---

# 5. Person Re-Identification

## OSNet

**OSNet** is used as the Person Re-ID feature extractor.

For each tracked person, the system extracts an appearance embedding representing visual characteristics of that person.

The embeddings can then be compared across different camera views.

```text
Person Crop
     |
     v
OSNet
     |
     v
512-D Appearance Embedding
```

The Re-ID representation allows the system to perform identity matching even when the same person receives a different tracking ID in another camera.

---

# 6. FAISS Similarity Search

The extracted Re-ID embeddings are indexed using **FAISS**.

FAISS provides efficient similarity search over the stored appearance representations.

The cross-camera matching process is:

```text
Current Person
      |
      v
OSNet Embedding
      |
      v
FAISS Similarity Search
      |
      v
Top Candidate Identities
```

The retrieved candidates are then passed to the verification stage when additional visual reasoning is required.

---

# 7. Visual Verification

## Qwen3-VL-2B

The system uses **Qwen3-VL-2B** as an additional visual verification component.

Appearance-based retrieval can produce ambiguous candidates when multiple people have similar clothing or visual characteristics.

The visual-language model provides an additional verification stage for these cases.

```text
             FAISS
               |
               v
       Candidate Identities
               |
               v
          Qwen3-VL-2B
               |
               v
       Visual Verification
               |
               v
      Identity Association
```

This creates a two-stage matching process:

```text
Stage 1
Appearance Retrieval
        |
        v
      FAISS

Stage 2
Visual Verification
        |
        v
   Qwen3-VL-2B
```

---

# 8. Cross-Camera Identity Association

The main purpose of combining tracking and Re-ID is to connect observations of the same person across different camera streams.

For example:

```text
Camera 1
   |
   v
Track 17
   |
   v
OSNet Embedding
   |
   v
FAISS
   |
   +----------------------+
                          |
                          v
                     Candidate
                          |
                          v
                       Camera 2
                          |
                          v
                       Track 43
```

The local tracking IDs from different cameras do not need to be identical.

Instead, appearance embeddings are used to associate tracks across camera boundaries.

---

# 9. Full Processing Pipeline

The complete Computer Vision workflow is:

```text
                    Video Input
                         |
                         v
                ┌────────────────┐
                │    YOLOv12     │
                │ Person Detect  │
                └───────┬────────┘
                        |
                        v
                ┌────────────────┐
                │   StrongSORT   │
                │ Multi-Tracking │
                └───────┬────────┘
                        |
                        v
                ┌────────────────┐
                │     OSNet      │
                │   Person Re-ID │
                └───────┬────────┘
                        |
                        v
                ┌────────────────┐
                │     FAISS      │
                │ Vector Search  │
                └───────┬────────┘
                        |
                        v
                ┌────────────────┐
                │ Qwen3-VL-2B    │
                │ Verification   │
                └───────┬────────┘
                        |
                        v
              Cross-Camera Identity
                  Association
```

---

# 10. System Features

### Person Detection

Detects people from surveillance video using YOLOv12.

### Multi-Object Tracking

Maintains persistent person tracks using StrongSORT.

### Person Re-ID

Generates appearance embeddings using OSNet.

### Vector Retrieval

Uses FAISS for efficient similarity search across stored person representations.

### Cross-Camera Matching

Associates person tracks observed by different cameras.

### Visual Verification

Uses Qwen3-VL-2B to provide an additional verification stage for ambiguous matches.

### Video Processing

Supports processing and streaming of surveillance video through the application.

---

# 11. Backend Architecture

The backend is implemented using **FastAPI**.

The Computer Vision processing pipeline is separated into components responsible for:

```text
Detection
Tracking
Embedding
Indexing
Verification
```

The backend exposes API endpoints for interacting with the system and processing uploaded surveillance videos.

---

# 12. Frontend

The system includes a web-based frontend built with:

* React
* Vite
* TypeScript

The frontend provides the interface for interacting with the surveillance pipeline and viewing processed video streams.

---

# 13. API Components

The backend includes functionality for:

```text
/health
/upload
/stream/{filename}
```

The original system also includes authentication-related functionality for face-based login and user management.

These components support the complete surveillance application around the Computer Vision pipeline.

---

# 14. Project Structure

```text
Cross-Camera-Surveillance-Full-Stack-System/
│
├── backend/
│   ├── main.py
│   ├── core/
│   │   ├── pipeline.py
│   │   ├── indexer.py
│   │   └── verifier.py
│   │
│   └── routers/
│       └── auth.py
│
├── frontend/
│   ├── App.tsx
│   ├── components/
│   └── services/
│
├── weights/
│
├── requirements.txt
├── Dockerfile
└── README.md
```

---

# 15. Technologies

## Computer Vision

* YOLOv12
* StrongSORT
* OSNet
* FAISS
* OpenCV
* BoxMOT
* TrackEval

## Deep Learning

* PyTorch
* HuggingFace
* Qwen3-VL-2B

## Backend

* Python
* FastAPI
* REST APIs
* WebSocket / video streaming

## Frontend

* React
* Vite
* TypeScript

## Database / Storage

* Vector embeddings
* FAISS index
* Application data storage

## Development

* Git
* Docker

---

# 16. Evaluation

The Person Re-ID component was evaluated using standard Re-ID metrics:

* Rank-1
* Rank-5
* Rank-10
* Mean Average Precision (mAP)

The evaluation compared the baseline Re-ID pipeline with an enhanced pipeline incorporating additional verification.

These metrics were used to measure identity retrieval performance rather than general object detection performance.

---

# 17. Applications

The system was designed around a surveillance investigation workflow where an investigator may need to examine the movement of a person across multiple cameras after an incident.

Potential use cases include:

* Post-incident CCTV investigation
* Cross-camera person search
* Multi-camera tracking
* Person trajectory reconstruction
* Candidate identity retrieval
* Visual verification of ambiguous matches

The system is intended as an investigation and analysis tool rather than a real-time autonomous decision-making system.

---

# 18. Key Computer Vision Concepts Demonstrated

This project brings together several major Computer Vision components in a single system:

```text
Object Detection
       +
Multi-Object Tracking
       +
Person Re-Identification
       +
Vector Similarity Search
       +
Visual Reasoning
       =
Cross-Camera Identity Association
```

The project therefore demonstrates experience across both individual Computer Vision models and the engineering required to connect them into a complete application.

---

# 19. Project Status

The system was developed as a Final Year Project at FAST-NUCES Karachi.

The original project repository was maintained as a private repository during development.

The portfolio description focuses on the Computer Vision components and their integration into the larger surveillance application.
