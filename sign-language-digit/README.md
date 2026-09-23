# Sign Language Digit Identifier

A deep learning Computer Vision project for recognizing **American Sign Language (ASL) digits from 0 to 9** using transfer learning and fine-tuning with MobileNet.

---

## 1. Overview

The project focuses on image classification of hand gestures representing the ten ASL digit classes.

Instead of training a convolutional neural network completely from scratch, a pretrained **MobileNet** model is adapted to the target classification task through transfer learning and subsequently fine-tuned on the sign language dataset.

The project demonstrates a practical image classification workflow covering data preparation, model development, transfer learning, fine-tuning, and evaluation.

---

## 2. Objective

The objective is to build a deep learning model capable of identifying ASL hand gestures corresponding to the digits:

```text
0  1  2  3  4  5  6  7  8  9
```

---

## 3. Approach

The overall workflow is:

```text
Input Image
     |
     v
Image Preprocessing
     |
     v
Pretrained MobileNet
     |
     v
Transfer Learning
     |
     v
Fine-Tuning
     |
     v
Image Classification
     |
     v
ASL Digit: 0 - 9
```

---

## 4. Model Architecture

### MobileNet

MobileNet is used as the convolutional feature extractor.

The pretrained network provides visual representations learned from a large image dataset, reducing the amount of training required for the target task.

Its relatively lightweight architecture also makes it suitable for applications where computational efficiency is important.

---

## 5. Transfer Learning

The project uses a pretrained MobileNet model as the starting point for the ASL digit classification task.

The pretrained visual features are transferred to the new classification problem, with the final classification layers adapted to the ten ASL digit classes.

---

## 6. Fine-Tuning

After the initial transfer learning stage, the model is fine-tuned using the target sign language dataset.

Fine-tuning allows the pretrained visual features to adapt to characteristics specific to hand gestures and ASL digit recognition.

---

## 7. Classification Classes

The model recognizes ten classes:

| Class | Digit |
| ----: | ----: |
|     0 |  Zero |
|     1 |   One |
|     2 |   Two |
|     3 | Three |
|     4 |  Four |
|     5 |  Five |
|     6 |   Six |
|     7 | Seven |
|     8 | Eight |
|     9 |  Nine |

---

## 8. Results

The trained model achieved approximately:

**99% validation accuracy**

This result demonstrates that transfer learning with MobileNet can provide strong performance for the target ASL digit classification task.

---

## 9. Technologies

### Programming

* Python

### Deep Learning

* TensorFlow
* Keras
* MobileNet
* Convolutional Neural Networks
* Transfer Learning
* Fine-Tuning

### Development

* Jupyter Notebook
* NumPy
* Matplotlib

---

## 10. Repository Structure

```text
Sign_Language_Digit_Identifier/
│
├── Sign_Languge_Digit_Recognition_CNN.ipynb
└── README.md
```

The Jupyter notebook contains the model development, training, and evaluation workflow.

---

## 11. What This Project Demonstrates

* Image classification
* Convolutional neural networks
* Transfer learning
* Fine-tuning pretrained models
* MobileNet
* TensorFlow / Keras
* Deep learning model training
* Model evaluation

---

## 12. Possible Extensions

The project could be extended with:

* Real-time webcam-based recognition
* Additional image augmentation
* Per-class precision and recall
* Confusion matrix analysis
* Real-time inference
* Lightweight deployment
* Web or mobile application integration

