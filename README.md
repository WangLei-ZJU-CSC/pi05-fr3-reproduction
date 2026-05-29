# π0.5 FR3 Reproduction

Real-world reproduction and deployment of π0.5 on Franka Research 3 robots based on the OpenPI framework, featuring VR teleoperation data collection, LoRA fine-tuning, and real-time inference on physical robots.

---

## Overview

This project focuses on reproducing π0.5 policies on real FR3 robots using the [openpi](https://github.com/Physical-Intelligence/openpi) framework.

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

## Single-arm Task: Move Cup

The robot learns to move a cup to the other side of the table using π0.5 fine-tuning on VR-collected demonstrations.

### Demo Video

<!-- Drag and drop your MP4 video here -->
https://github.com/user-attachments/assets/a6614d0a-fcb9-41e7-a121-7260b886e29e


### Task Description

* Platform: Franka Research 3 (FR3) + Franka Hand
* Policy: π0.5 + LoRA fine-tuning with 80 demonstrations
* Input: Multi-view RGB observations
* Output: Joint velocity commands and gripper open/close state


---

## Bimanual Task: Bag Bottle

One arm lifts the bag while the other arm grasps and inserts the bottle into the bag.

This task demonstrates dual-arm coordination and sequential manipulation capability on real robots.

### Demo Video

<!-- Drag and drop your MP4 video here -->

https://github.com/user-attachments/assets/2b7eddae-7ee0-46b2-a1f0-e71dabc8f76c

### Task Description

* Platform: Dual Franka Research 3 (FR3) + Robotiq 2F-85 Gripper
* Policy: π0.5 + LoRA fine-tuning with 100 demonstrations
* Input: Multi-view RGB observations
* Output: Dual-arm joint velocity commands and gripper open/close state

### Robustness Evaluation

To evaluate the robustness and consistency of the learned bimanual policy, we additionally conducted five consecutive real-world trials without manual intervention.
The policy achieved:

* **5 / 5 successful task completions**
* **100% success rate**
* Stable dual-arm coordination across repeated executions
demonstrating stable dual-arm coordination and reliable real-time deployment performance on physical robots.

### Consecutive 5-Trial Demo

<!-- Drag and drop your MP4 video here -->


https://github.com/user-attachments/assets/25c6696b-f57c-4cb4-ad11-dded7360fa26

---

# VR Teleoperation

We developed a VR-based teleoperation pipeline for real-world data collection.

The VR system enables intuitive robot control and efficient demonstration collection for manipulation tasks.

### VR Teleoperation Demo

<!-- Drag and drop your MP4 video here -->
https://github.com/user-attachments/assets/15f3ce3d-1ead-4d35-9829-53188bfeaee1



---


# Dataset & Model
## Single-arm Task: Move Cup
The policy is initialized from the pretrained **pi05_droid** checkpoint released by openpi and fine-tuned with the following training configuration:
```python
    TrainConfig(
        # This config is for fine-tuning pi05-DROID on a custom (smaller) DROID dataset.
        # LoRA fine-tuning for 48GB GPU.
        name="pi05_droid_finetune",
        model=pi0_config.Pi0Config(
            pi05=True,
            discrete_state_input=False,
            action_dim=32,
            action_horizon=16,
            paligemma_variant="gemma_2b_lora",
            action_expert_variant="gemma_300m_lora",
        ),
        data=LeRobotDROIDDataConfig(
            repo_id="local/fr3_droid_finetune",
            base_config=DataConfig(prompt_from_task=True),
            assets=AssetsConfig(
                # assets_dir="gs://openpi-assets/checkpoints/pi05_droid/assets",
                assets_dir="/home/pc/Projects/pi0_franka/openpi-main/assets/pi05_droid_finetune/local/fr3_droid_finetune",
                asset_id="droid_move_cap",
            ),
        ),
        weight_loader=weight_loaders.CheckpointWeightLoader("/home/pc/Projects/pi0_franka/openpi-main/checkpoints/pi05_droid_jax/params"),
        lr_schedule=_optimizer.CosineDecaySchedule(
            warmup_steps=500,
            peak_lr=2e-4,         
            decay_steps=30_000,
            decay_lr=1e-5,
        ),
        num_train_steps=30_000,
        batch_size=32,
        log_interval=1,
        save_interval=5000,              
        keep_period=5000,
        freeze_filter=pi0_config.Pi0Config(
            pi05=True,
            paligemma_variant="gemma_2b_lora",
            action_expert_variant="gemma_300m_lora",
        ).get_freeze_filter(),
        ema_decay=None,
    ),
```
Visualization of the training results:
<img width="3508" height="1013" alt="image" src="https://github.com/user-attachments/assets/5cb2e32e-fb57-4e75-9666-db7fb6b9c7f7" />

Huggingface repository:

[Dataset Link](https://huggingface.co/datasets/WANGLeiZJUCSC2025/fr3-move-cup-ds)

[Model Link](https://huggingface.co/WANGLeiZJUCSC2025/fr3-move-cup-pi05-ckpt)

## Bimanual Task: Bag Bottle
The policy is initialized from the pretrained **pi05_base** checkpoint released by openpi nd fine-tuned with the following training configuration:
```python
    TrainConfig(
        name="pi05_franka_bimanual",

        model=pi0_config.Pi0Config(
            pi05=True,
            action_horizon=16,
            action_dim=32,
            discrete_state_input=False,
            paligemma_variant="gemma_2b_lora",
            action_expert_variant="gemma_300m_lora",
        ),

        data=LeRobotAlohaDataConfig(
            repo_id="local/pi05_franka_bimanual",
            use_delta_joint_actions=False,
            adapt_to_pi=False,
            base_config=DataConfig(prompt_from_task=True),

            assets =AssetsConfig(
                assets_dir="/home/pc/Projects/pi0_franka/openpi-main/assets",
                asset_id="pi05_franka_bimanual",
            ),

            repack_transforms=_transforms.Group(
                inputs=[
                    _transforms.RepackTransform(
                        {
                            "images":{
                                "cam_high":         "observation.images.cam_high",
                                "cam_left_wrist":   "observation.images.cam_left_wrist",
                                "cam_right_wrist":  "observation.images.cam_right_wrist",
                            },
                            "state":    "observation.state",
                            "actions":  "action",
                            "prompt":   "prompt",
                        }
                    )
                ]
            ),
        ),

        weight_loader=weight_loaders.CheckpointWeightLoader(
            "/home/pc/Projects/pi0_franka/openpi-main/checkpoints/pi05_base_jax/params"
        ),

        num_train_steps=50_000,
        batch_size=32,
        lr_schedule=_optimizer.CosineDecaySchedule(
            warmup_steps=300,
            peak_lr=2e-4,
            decay_steps=50_000,
            decay_lr=1e-5,
        ),
        optimizer=_optimizer.AdamW(clip_gradient_norm=1.0),
        ema_decay=None,
        save_interval=2_000,
        keep_period=5_000,
        freeze_filter=pi0_config.Pi0Config(
            pi05=True,
            paligemma_variant="gemma_2b_lora",
            action_expert_variant="gemma_300m_lora",
        ).get_freeze_filter(),
    ),
```
Visualization of the training results:

<img width="3512" height="1015" alt="image" src="https://github.com/user-attachments/assets/bc998205-2eda-46aa-9dc4-dc7d258f5db4" />

Huggingface repository:

[Dataset Link](https://huggingface.co/datasets/WANGLeiZJUCSC2025/bi-fr3-put-bottle-ds)

[Model Link](https://huggingface.co/WANGLeiZJUCSC2025/bi-fr3-put-bottle-pi05-ckpt)


---

# System Architecture
<img width="2162" height="1082" alt="arc" src="https://github.com/user-attachments/assets/7ad0c512-a14f-45d3-b7af-39219eded66d" />


---

# Hardware Setup

## Robot

* Franka Research 3 (FR3)
* Franka Hand for single-arm task
* Robotiq 2F-85 gripper for bimanual task


## Sensors

* Wrist camera : Intel RealSense D405
* Scene camera : Intel RealSense D435i

## VR Device
* PICO

## Computing

* Ubuntu 22.04 LTS (with RT kernel)
* Intel Core Ultra 9 285K
* NVIDIA GeForce RTX 4090 48G
* ROS2 Humble


---

# Acknowledgement

This project is built upon the OpenPI framework and π0.5 policy model.

Thanks to the authors for open-sourcing their excellent work.
