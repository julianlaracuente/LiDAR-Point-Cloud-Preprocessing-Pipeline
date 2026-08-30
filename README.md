# LiDAR Point Cloud Preprocessing Pipeline
 
A deterministic ROS-based preprocessing module that prepares raw LiDAR point cloud data (published in Gazebo) for robust plane detection on a drone landing system. This pipeline cleans, filters, and structures point clouds before they are passed to RANSAC-based plane segmentation.
 
## Objective
 
Raw LiDAR data straight out of simulation is noisy, dense, and includes irrelevant points (NaNs, infinities, ceiling returns, out-of-range points). This module solves that by providing a configurable, testable pipeline that outputs a clean, downsampled, region-restricted point cloud ready for downstream plane segmentation.
 
## Features
 
- **LiDAR subscription** with validated message reception and continuous streaming (no frame drops).
- **Invalid point removal** — strips NaN and infinite values, guaranteeing only valid 3D points reach later stages.
- **Voxel grid downsampling** — configurable voxel size for significant point count reduction while preserving structure.
- **Region of Interest (ROI) filtering** — configurable Z-range and XY bounds to discard irrelevant altitude/lateral points (e.g. ceiling returns).
- **Fully parameterized** via ROS parameters — no hardcoded thresholds.
- **Unit-tested** with synthetic, deterministic point clouds covering NaN removal, downsampling, and ROI accuracy.
## Pipeline Architecture
 
The pipeline is organized as a chain of ROS nodes communicating over dedicated topics. Raw sensor data flows from Gazebo through the preprocessing stage and into the RANSAC-based segmentation stage, which produces the final ground/obstacle classification consumed by the landing logic.
 
```
┌───────────────────────┐
│   Gazebo LiDAR Sensor  │
└───────────┬────────────┘
            │ publishes raw, unfiltered cloud
            ▼
   /lidar/points/points
            │
            │ subscribed by preprocessing node
            ▼
┌────────────────────────────────────────────┐
│         Preprocessing Node                  │
│  (NaN/Inf removal → Voxel downsample → ROI) │
└───────────┬──────────────────────────────────┘
            │ publishes cleaned, filtered cloud
            ▼
   /lidar/point/filters
            │
            │ subscribed by RANSAC segmentation node
            ▼
┌────────────────────────────────────────────┐
│        RANSAC Plane Segmentation Node       │
└───────┬───────────────────────────┬──────────┘
        │ publishes                 │ publishes
        ▼                           ▼
/lidar/points/ground        /lidar/point/obstacles
 (ground-plane points)         (obstacle points)
```
## Contributors
- Main collaborators were Victor Santos, Brian Rivera, and myself.
