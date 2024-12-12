+++
title = 'Human-Robot Teleoperation via mVAE: Predicting Robot Motion from Human EMG and IMU Signals'
date = 2024-12-04T05:18:56-06:00
draft = false
+++
<div style="text-align:center;">Authors: Jialu Yu</div>



<!-- {{< youtube >}}   -->


# Motivation

Rehabilitation and automation are two fields where robotics can make a real difference in people’s lives. For stroke patients undergoing upper-body rehabilitation, traditional exercises can feel repetitive and dull, making it hard to stay motivated. By adding robotics into the mix, we can turn these exercises into dynamic and engaging activities that not only boost engagement but also improve muscle activation and recovery. Similarly, in warehouses, many tasks still rely heavily on manual labor. Human-controlled robotic systems offer an exciting way to combine human intuition with robotic precision, creating safer and more efficient workflows—even in challenging environments where signal loss or interference can occur.

This project was inspired by the need to bridge the gap between human intent and robotic action. By using Myo armbands to capture human arm movements and muscle activity through IMU (Inertial Measurement Unit) and EMG (Electromyography) signals, I trained a Multimodal Variational Autoencoder (mVAE) to predict and control a Franka Emika robotic arm. What makes the mVAE special is its ability to handle missing or incomplete data, ensuring the system remains reliable even in less-than-ideal conditions. This versatility makes it a promising solution for both rehabilitation and automation, where adaptability and precision are key.

# Mission

The objective of this project is to develop and deploy a robust machine learning framework that **translates human arm movements** into **precise robotic control**. Specifically, this includes:


- **Data Collection and Synchronization** Capturing synchronized IMU and EMG signals from the Myo armbands, cube force sensor data, along with the robotic arm’s joint data, ensuring high-quality, multimodal input for training the model.

- **Data process** The data is processed through various filters and downsampling to ensure IMU, EMG, and robot joint data are corresponding with each other when feeding into the model. 

- **Model Training and Adaptation** Designing and training a Multimodal Variational Autoencoder (mVAE) to predict and reconstruct robotic joint positions and velocities based on human input. The model’s robustness is enhanced through data augmentation techniques such as *time-step concatenation* and *input masking*, allowing it to perform reliably even with partial or noisy data.

- **Real-Time Deployment** Implementing the trained mVAE model to control a Franka Emika robotic arm in both simulated and real-world environments, achieving responsive and accurate robotic movements based on human intent.



By achieving these objectives, the project demonstrates the feasibility of human-driven robotic control and its potential impact in diverse fields.


# Data Collection


The data collection process is divided into two parts: **Human Arm Data Collection** and **Robot Data Collection**. Both the human and robot perform the same series of tasks to collect their respective data, specifically: picking up and placing a cube.

## Task Setup and Board Design


![targets](/images/plate_itself.jpg )
The task is carried out on the board shown above, which includes one starting position (a square) and 27 target positions on the same plane. The target positions are arranged in a semi-circle, characterized by varying combinations of angles (0°, 22.5°, 45°, 67.5°, 90°, 112.5°, 135°, 157.5°, and 180°), distances (0.1m, 0.2m, 0.3m), and heights (0m and 0.078m). This setup creates a total of 54 distinct target positions (27 2D positions with 2 height variations), which leads to 54 raw datasets among all human, robot and cube data. 

At the start of each trial, the cube is placed at the square-shaped starting position, and both the human and robot begin from their respective home positions. The task is to grab the cube and place it at a target position.

## Myo Armband for Human Data Collection

<!-- ![targets](/images/Cube_with_force_sensor.png) -->
<div style="text-align: center;">
   <img src="/images/Cube_with_force_sensor.png" alt="targets" width="400"/>
</div>
During the human trials, two Myo armbands are used: one worn on the upper arm and the other on the lower arm, as illustrated below. These armbands are connected via serial Bluetooth, allowing for seamless collection of both IMU (Inertial Measurement Unit) and EMG (Electromyography) data through the ROS2 system.

Each trial involves collecting data across the entire task cycle: starting from the initial position, grabbing the cube, moving to the target position, and finally placing the cube at the designated location. To monitor the gripping force, each side of the cube is equipped with a force sensor, which records force data during the task. To ensure robustness and reliability in the machine learning model, the entire task is repeated four times in each trial.

## Cube Force Data Collection

A cube equipped with force sensors on each side is used during both the human and robot trials to measure the force applied to the cube. This data will be used to map the force applied by the human's fingers onto the robot’s gripper strength, which is an important aspect for accurate teleoperation control. 

## Robot Data Collection

For the robot trials, a Franka Emika robot performs the same task as the human subject. Starting from the same home position, the robot grabs the cube, moves to the target position, and places the cube there. Data collection is managed using ROS2 and **rosbag** to record the robot’s joint trajectories. The robot's joint positions and velocities are captured at each timestamp during the trial.

# Data Processing

The EMG data is sampled at 200 Hz, while the IMU data is sampled at 50 Hz, resulting in different data lengths within the same trial. To create a consistent dataset, it is necessary to align the EMG and IMU data with the robot joint velocity, position data, and the cube force sensor data.

## Human Data Processing

1. **Segmentation**:  

   The EMG data is first segmented based on valid movements during the task, which includes the following phases:
   - From the start position to grabbing the cube
   - Moving the cube to the target position
   - Dropping the cube at the target position

   A clustering-based machine learning algorithm is employed to identify the boundaries of the movement phases, dividing the task into distinct segments. This process results in four segments of EMG and IMU data for each trial: one set from the upper Myo armband and another from the lower Myo armband. These segments are then combined to create a comprehensive dataset that captures both muscle activity and movement dynamics, as shown below.
   <div style="text-align: center;">
      <img src="/images/Combine_emg_imu_unprocessed.svg" alt="targets" width="400"/>
   </div>

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