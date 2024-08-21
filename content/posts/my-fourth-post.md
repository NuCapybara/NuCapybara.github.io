+++
title = 'Robotic Assistant for Lip Moisturizing: Enhancing Care for Unconscious Patients'
date = 2024-03-15T16:03:35-05:00
draft = false
+++


{{< youtube M1ve4sudqlI>}}

##
## Project Overview

This 10-week project aims to develop a small, affordable robot that can recognize lip movements and accurately apply a cotton swab to the lips. It's designed to help patients who are unconscious and suffering from dry lips and mouth due to their inability to drink water on their own. Typically, caregivers moisten the patient's lips with a moist cotton swab to hydrate and clean the area, which is a repetitive task that requires constant attention. By automating this process, the robot can alleviate the burden on healthcare workers and caregivers, freeing them up to focus on other important duties.

## Overall System 

The image below shows the main nodes in the system and how they communicate to achieve a task flow.

![targets](/images/winter_flow_chart.png)

## Hardware

The system is composed of an interbotix WX-200 robot and an Intel RealSense D435i Depth camera. The connected black 3D parts are designed for precise calibration between camera and robot.

![targets](/images/robot_rs4.jpg)

## Computer Vision


The application's computer vision utilizes the dlib library for facial landmark detection with deep learning, using a shape predictor model trained on a large dataset to accurately identify facial features.

Facial landmarks are converted from 2D to 3D using the Intel RealSense camera's depth sensing, aligning color and depth frames for accurate depth information. The set of landmarks are then published as coordinates in a PoseArray for the Robot control Node. 

<!-- ![target](/images/oldman_rviz_image2.png) -->

<div style="text-align: center;">
    <img src="/images/oldman_rviz_image2.png" alt="Computer Vision">
</div>

The circular image above displays the camera's detection of 2D lip landmarks. In contrast, the simulation incorporating depth clouds illustrates the lip points as arrays in 3D coordinates, marked by light blue spheres. During execution, the designated target points are indicated by red sphere markers. A static transformation between the robot and camera, based on the head-link frame representing the leftmost lip point on a male mannequin, allows the robot to accurately locate lip points.


## Results

<div style="text-align: center;">
    <img src="/images/compare_wig_3.gif" alt="Mouth Care Process Accuracy">
</div>

<div style="text-align: center;">
    The mouth care process maintains high accuracy across various facial structures.
</div>

<div style="text-align: center;">
    <img src="/images/compare_wig_2.gif" alt="Adapting to Face Position Changes">
</div>

<div style="text-align: center;">
    The mouth care process adapts to changes in the face's position and angle.
</div>

## Future Work

The face landmarks detection goes beyond identifying lip points, also capturing features like eyes and nose. This opens avenues for extending the application to tasks like facial cleaning, feeding, or rehabilitation processes. Please feel free to use my code in GitHub for future extended works :)