+++
title = 'Human-Robot Teleoperation via mVAE: Predicting Robot Motion from Human EMG and IMU Signals'
date = 2024-12-04T05:18:56-06:00
draft = false
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

The task is carried out on the board shown below, which includes one starting position (a square) and 27 target positions on the same plane. The target positions are arranged in a semi-circle, characterized by varying combinations of angles (0°, 22.5°, 45°, 67.5°, 90°, 112.5°, 135°, 157.5°, and 180°), distances (0.1m, 0.2m, 0.3m), and heights (0m and 0.78m). This setup creates a total of 54 distinct target positions (27 2D positions with two height variations). 

At the start of each trial, the cube is placed at the square-shaped starting position, and both the human and robot begin from their respective home positions. The task is to grab the cube and place it at a target position.

## Myo Armband for Human Data Collection

Two Myo armbands are used during the human trials: one worn on the upper arm and one on the lower arm, as shown below. These two armbands are connected via serial Bluetooth, and both IMU (Inertial Measurement Unit) and EMG (Electromyography) data are collected through the ROS2 system. 

In each trial, the data is collected throughout the entire task: from the starting position, grabbing the cube, moving to the target position, and finally placing the cube at the target location. The task is repeated four times in each trial to ensure the robustness of the machine learning model.

## Robot Data Collection

For the robot trials, a Franka Emika robot performs the same task as the human subject. Starting from the same home position, the robot grabs the cube, moves to the target position, and places the cube there. Data collection is managed using ROS2 and **rosbag** to record the robot’s joint trajectories. The robot's joint positions and velocities are captured at each timestamp during the trial.

## Cube Force Data Collection

A cube equipped with force sensors on each side is used during both the human and robot trials to measure the force applied to the cube. This data will be used to map the force applied by the human's fingers onto the robot’s gripper strength, which is an important aspect for accurate teleoperation control.
