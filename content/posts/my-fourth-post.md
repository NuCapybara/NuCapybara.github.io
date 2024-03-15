+++
title = 'Robot doing Mouth Care for Unconscious Residents'
date = 2024-03-15T16:03:35-05:00
draft = true
+++

## Introduction

{{< youtube M1ve4sudqlI>}}

Patients in an unconscious state often encounter hydration issues, leading to dryness in their lips and the interior of their mouths. Given their inability to consume water independently, administering regular quantities of water poses a risk. Caregivers typically resort to using a moisturized cotton swab to gently moisten the patient's lips, which serves the dual purpose of hydrating the area and assisting with oral cleanliness, as well as facilitating the practice of swallowing functions. However, this method demands continuous, repetitive effort from caregivers, potentially around the clock. 

The introduction of a robotic solution could address this challenge efficiently, allowing healthcare professionals and caregivers to allocate their time to other critical tasks.

The project's goal is to enable a robot to detect lip landmarks and accurately apply a cotton swab to the lips.

## Overall System 

The image below shows the different nodes in the system and how they communicate with each other. Each node's funtionalities will be explained below.

## Computer Vision

The computer vision component of the application harnesses the power of the dlib library, renowned for its deep learning to perform facial landmark detection by the shape predictor model, trained on an extensive dataset of facial landmarks. This model is adept at pinpointing the precise locations of significant points on the face, ensuring high accuracy in facial feature recognition.

Following the detection of facial landmarks, the 2D to 3D coordinate transformation is achieved by RealSense camera's depth sensing. We integrate the Intel RealSense camera's capabilities to align color and depth frame of image. This process involves capturing real-time video streams, processing the color images to identify facial landmarks, and then correlating these points with depth information from the aligned depth frame.

The face_detection node will publish the 7 lip landmarks coordinates in an PoseArray to communicate with the MoveIt Node.
