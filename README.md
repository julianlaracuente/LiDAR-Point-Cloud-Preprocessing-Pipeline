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

## Vizualization

- Gazebo Simulation :
<img width="1162" height="1744" alt="image" src="https://github.com/user-attachments/assets/c4dec28e-9efc-40d1-9124-3f2a6188fc62" />

- lidar/points/points topic. (RAW Lidar Points)
<img width="1090" height="1084" alt="image" src="https://github.com/user-attachments/assets/0f2e45af-0ce5-4534-ba27-3d84c8b72e53" />

- lidar/points/filtered
  <img width="1080" height="1176" alt="image" src="https://github.com/user-attachments/assets/a0020642-41d7-41a9-baed-00bd312bbfbe" />

- lidar/points/ground
  <img width="1088" height="1196" alt="image" src="https://github.com/user-attachments/assets/5532e19d-7e25-4caa-b04a-7e8d01e8755d" />

- lidar/points/obstacles
  <img width="1072" height="858" alt="image" src="https://github.com/user-attachments/assets/c259da15-4507-4e2b-bc9d-3f2a78c14002" />

## Contributors
- Main collaborators were Victor Santos, Brian Rivera, and myself.
