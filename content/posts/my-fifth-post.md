+++
title = 'Human-Robot Teleoperation via mVAE: Predicting Robot Motion from Human EMG and IMU Signals'
date = 2024-12-04T05:18:56-06:00
draft = true
+++
<div style="text-align:center;">Authors: Jialu Yu</div>

# Introduction

<!-- {{< youtube >}}   -->

This project applies a Multimodal Variational Autoencoder (mVAE) model to enable human arm movements to control a Franka Emika robotic arm. The user wears a Myo armband that captures both Inertial Measurement Unit (IMU) and Electromyography (EMG) signals. These signals represent the human arm’s movement and muscle activity, which are processed by the mVAE model to predict and reconstruct the corresponding robot joint positions and velocities.

The key advantage of using the mVAE model is its ability to handle missing data. In cases where part of the human or robot data is absent, the model can reconstruct the missing segments, ensuring that the system remains robust even with incomplete input. This ability to deal with partial data is particularly valuable in real-world applications, where signal loss or interference is common.

The potential applications of this project are broad. It can be utilized in automatic warehouse systems for teleoperating robotic arms to move goods, or in the rehabilitation of stroke patients, allowing them to control robotic systems for therapeutic purposes.



# Data Collection


The data collection process is divided into two parts: **Human Arm Data Collection** and **Robot Data Collection**. Both the human and robot perform the same series of tasks to collect their respective data, specifically: picking up and placing a cube.

## Task Setup and Board Design

The task is carried out on the board shown below, which includes one starting position (a square) and 27 target positions on the same plane. The target positions are arranged in a semi-circle, characterized by varying combinations of angles (0°, 22.5°, 45°, 67.5°, 90°, 112.5°, 135°, 157.5°, and 180°), distances (0.1m, 0.2m, 0.3m), and heights (0m and 0.078m). This setup creates a total of 54 distinct target positions (27 2D positions with 2 height variations). 

At the start of each trial, the cube is placed at the square-shaped starting position, and both the human and robot begin from their respective home positions. The task is to grab the cube and place it at a target position.

## Myo Armband for Human Data Collection

Two Myo armbands are used during the human trials: one worn on the upper arm and one on the lower arm, as shown below. These two armbands are connected via serial Bluetooth, and both IMU (Inertial Measurement Unit) and EMG (Electromyography) data are collected through the ROS2 system. 

In each trial, the data is collected throughout the entire task: from the starting position, grabbing the cube, moving to the target position, and finally placing the cube at the target location. The task is repeated four times in each trial to ensure the robustness of the machine learning model.

## Robot Data Collection

For the robot trials, a Franka Emika robot performs the same task as the human subject. Starting from the same home position, the robot grabs the cube, moves to the target position, and places the cube there. Data collection is managed using ROS2 and **rosbag** to record the robot’s joint trajectories. The robot's joint positions and velocities are captured at each timestamp during the trial.

## Cube Force Data Collection

A cube equipped with force sensors on each side is used during both the human and robot trials to measure the force applied to the cube. This data will be used to map the force applied by the human's fingers onto the robot’s gripper strength, which is an important aspect for accurate teleoperation control.


# Data Processing


The EMG data is sampled at 200 Hz, while the IMU data is sampled at 50 Hz, resulting in different data lengths within the same trial. To create a consistent dataset, it is necessary to align the EMG and IMU data with the robot joint velocity, position data, and the cube force sensor data.

## Human Data Processing

1. **Segmentation**:  
   The EMG data is first segmented based on valid movements during the task, which includes the following phases:
   - From the start position to grabbing the cube
   - Moving the cube to the target position
   - Dropping the cube at the target position

   A clustering-based machine learning algorithm is applied to identify the boundaries of these movement phases. This results in four segments of EMG data for each trial: one for the upper Myo armband and one for the lower Myo armband.

2. **Data Smoothing and Rectification**:  
   Due to the noisy nature of the EMG data, a smoothing and rectification process is applied to improve its quality for machine learning. This helps ensure that the data is cleaner and more suitable for model training.

3. **Downsampling**:  
   Next, the EMG data is downsampled to match the time synchronization with the IMU data. Linear interpolation is used to ensure that both the EMG and IMU data segments are aligned in time.

4. **Alignment**:  
   After downsampling, the shortest data segment length among all EMG and IMU segments is identified. All other segments are downsampled to this length to ensure uniformity. At this point, each segment (EMG and IMU) will have the same length for each trial.

5. **Data Combination**:  
   The four segmented datasets (for both the upper and lower armbands) are then combined into four distinct datasets per trial:
   - Upper Myo EMG combined data
   - Upper Myo IMU combined data
   - Lower Myo EMG combined data
   - Lower Myo IMU combined data

   Each dataset now has consistent data length, representing the human’s performance of the task across all four repetitions in a trial.

## Robot Data Processing

1. **Segmentation**:  
   Similar to the human data, the robot data is segmented according to the desired task movements (grabbing, moving, and placing the cube).

2. **Downsampling and Alignment**:  
   The robot data is then downsampled to align with the human EMG and IMU data. A low-pass filter is applied to smooth the robot’s joint positions and velocities, ensuring that the data is consistent and suitable for training.

3. **Repetition**:  
   Since the robot performs the task once per trial (as the robot follows a fixed trajectory), it does not repeat the task like the human data. To align the robot data with the human data (which has been repeated four times), the robot’s data is duplicated four times to match the four repetitions of the human trial. This ensures that the robot’s trajectory is aligned with the four human data segments in each trial.

## Data Processing Steps:

### Human Data:
- Segment and cluster EMG data based on task movements.
- Apply smoothing and rectification to the EMG data.
- Downsample and align EMG and IMU data.
- Ensure uniform length across all data segments.
- Combine the data for both upper and lower armbands.

### Robot Data:
- Segment robot task movements and apply a low-pass filter.
- Downsample robot data to match human data.
- Repeat robot data to align with the four human task repetitions.


# Data Augmentation
- TODO

# mVAE model training
- TODO

# Result
- TODO