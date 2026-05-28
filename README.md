# π0.5 FR3 Reproduction

Real-world reproduction and deployment of π0.5 on Franka Research 3 robots based on the OpenPI framework, featuring VR teleoperation data collection, LoRA fine-tuning, and real-time inference on physical robots.

---

## Overview

This project focuses on reproducing π0.5 policies on real FR3 robots using the OpenPI framework.

The project includes:

* VR teleoperation system
* Real-world data collection
* HuggingFace dataset construction
* LoRA fine-tuning
* Real-time inference deployment
* Single-arm manipulation tasks
* Bimanual manipulation tasks

---

# Task Showcase

## Single-arm Task: MoveCup

The robot learns to move a cup to the other side of the table using π0.5 fine-tuning on VR-collected demonstrations.

### Demo Video

<!-- Drag and drop your MP4 video here -->
https://github.com/user-attachments/assets/a6614d0a-fcb9-41e7-a121-7260b886e29e


### Task Description

* Platform: Franka Research 3 (FR3) + Franka Hand
* Policy: π0.5 + LoRA fine-tuning
* Input: Multi-view RGB observations
* Output: Joint velocity commands and gripper open/close state


---

## Bimanual Task: Bagging Bottle

One arm lifts the bag while the other arm grasps and inserts the bottle into the bag.

This task demonstrates dual-arm coordination and sequential manipulation capability on real robots.

### Demo Video

<!-- Drag and drop your MP4 video here -->

https://github.com/user-attachments/assets/2b7eddae-7ee0-46b2-a1f0-e71dabc8f76c

### Task Description

* Platform: Dual Franka Research 3 (FR3) + Robotiq 2F-85 Gripper
* Policy: π0.5 + LoRA fine-tuning
* Input: Multi-view RGB observations
* Output: Dual-arm joint velocity commands and gripper open/close state

### Robustness Evaluation

To evaluate the robustness and consistency of the learned bimanual policy, we additionally conducted five consecutive real-world trials without manual intervention.

The policy successfully completed the bagging task across repeated executions, demonstrating stable dual-arm coordination and reliable real-time deployment performance on physical robots.

### Consecutive 5-Trial Demo

<!-- Drag and drop your MP4 video here -->
https://github.com/user-attachments/assets/5984dfb7-b32c-44eb-9e09-84e22a71e1fc


### Consecutive Trial Results

| Trial | Result  |
| ----- | ------- |
| 1     | Success |
| 2     | Success |
| 3     | Success |
| 4     | Success |
| 5     | Success |


---

# VR Teleoperation

We developed a VR-based teleoperation pipeline for real-world data collection.

The VR system enables intuitive robot control and efficient demonstration collection for manipulation tasks.

### VR Teleoperation Demo

<!-- Drag and drop your MP4 video here -->
https://github.com/user-attachments/assets/57158965-54eb-4ab7-b680-8cab4530f8e9
---

# Real-time Inference

The fine-tuned policy is deployed for real-time inference on physical robots.

### Inference Demo

<!-- Drag and drop your MP4 video here -->

---

# Dataset

## HuggingFace Dataset

[Dataset Link](https://huggingface.co/datasets/YOUR_DATASET_LINK)

### Dataset Information

* Real-world robot demonstrations
* VR teleoperation trajectories
* Multi-view RGB observations
* Single-arm and bimanual tasks

---

# Model

## HuggingFace Model

[Model Link](https://huggingface.co/YOUR_MODEL_LINK)

### Training Details

* Base Policy: π0.5
* Framework: OpenPI
* Fine-tuning Method: LoRA
* Training Platform: Local multi-GPU workstation

---

# System Architecture

## Pipeline

```text
VR Teleoperation
        ↓
Real-world Data Collection
        ↓
Dataset Construction
        ↓
OpenPI / π0.5 Fine-tuning
        ↓
Real-time Robot Inference
```

---

# Hardware Setup

## Robot

* Franka Research 3 (FR3)
* Dual-arm Franka setup

## Computing

* Ubuntu 22.04
* ROS2 Humble
* CUDA
* PyTorch

## Sensors

* Multi-view RGB cameras
* VR teleoperation devices

---

# My Contributions

* Built local OpenPI training and inference environment
* Developed VR teleoperation data collection pipeline
* Collected real-world manipulation datasets
* Adapted π0.5 for Franka robot tasks
* Performed LoRA fine-tuning on custom datasets
* Deployed real-time inference system on physical robots
* Conducted single-arm and bimanual manipulation experiments

---

# Future Work

* More complex long-horizon tasks
* Tactile sensing integration
* Better bimanual coordination
* More efficient data collection

---

# Acknowledgement

This project is built upon the OpenPI framework and π0.5 policy model.

Thanks to the authors for open-sourcing their excellent work.

---

# References

* OpenPI
* π0 / π0.5
* LeRobot
* OpenVLA

