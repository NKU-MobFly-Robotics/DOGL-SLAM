<p align="center">
  <h1 align="center">DOGL-SLAM: Dynamic Object-level SLAM via Joint Gaussian-Landmark Tracking</h1>
</p>
  <h3 align="center"><a href="https://ieeexplore.ieee.org/document/11278733">Paper</a> | <a href="https://www.bilibili.com/video/BV1B4ijBiEd8">Video</a> 
  <div align="center"></div>

# Abstract
Recent advancements in 3D Gaussian Splatting (3DGS) have significantly improved the mapping quality and computational efficiency of visual Simultaneous Localization and Mapping (SLAM). We propose DOGL-SLAM, a novel framework that integrates 3DGS into its core pipeline, enabling accurate camera pose tracking, object-level interaction, and high-fidelity scene reconstruction in dynamic environments. Firstly, a joint graph optimization module incorporates both dense Gaussian and sparse landmark constraints, enabling precise alignment between camera tracking and mapping. Secondly, a consistent object-level semantic fusion module embeds category labels into 3D Gaussians, grouping them via semantic consistency and optimizing distributions through cross-view losses to support scene manipulation. Finally, a hierarchical dynamic filtering pipeline is introduced, consisting of segmentation, dynamic feature exclusion, gradient masking, and visibility-aware Gaussian pruning across three parallel threads. Our system is evaluated across multiple datasets, showing a significant improvement in high-quality view synthesis for dynamic scenes. Additionally, the generated object-level semantic maps facilitate advanced downstream tasks, highlighting the potential of a robust SLAM framework. 


# Pipeline Overview

Overview of DOGL-SLAM architecture. The system consists of five parallel threads: segmentation, tracking, local mapping, loop closing, and Gaussian
mapping. The RGB-D input is processed to extract dynamic masks and features, enabling dynamic-aware Gaussian-landmark tracking for pose estimation.
Optimized keyframes and semantics are used to refine 3D Gaussian representations through gradient masking and visibility-aware pruning, resulting in accurate,
editable, and semantically meaningful maps even in dynamic environments.
![overview](assets/overview.png)


# Getting Started
## Installation

Install the docker image and clone the repo.
Our Docker is based on [the Dockerfile of Photo-SLAM](reference.Dockerfile).
```
docker pull wsy588/doglslam:latest

git clone https://github.com/NKU-MobFly-Robotics/DOGL-SLAM.git --recursive

# creat a container, eg:
sudo docker run -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=:0 --net=host -e GDK_SCALE -v /your/dataset/path:/dataset -v /your/code/path:/DOGL-SLAM  -e GDK_DPI_SCALE  --privileged --gpus all -e NVIDIA_DRIVER_CAPABILITIES=all --name YOURNAME wsy588/doglslam:latest  /bin/bash

cd DOGL-SLAM

sh build.sh
```
## Dataset

The benchmark datasets include: [TUM RGB-D](https://cvg.cit.tum.de/data/datasets/rgbd-dataset/download), [BONN](https://www.ipb.uni-bonn.de/data/rgbd-dynamic-dataset). the `Datasets` directory is expected to have the following structure.

```
Datasets
├── BONN
| ├── rgbd_bonn_balloon
| ├── rgbd_bonn_crowd
| ├── rgbd_bonn_synchronous
| └── rgbd_bonn_synchronous
|     ├── depth
|     ├── depth.txt
|     ├── groundtruth.txt
|     ├── object_mask
|     ├── rgb
|     ├── rgb.txt
|     └── dynamic_mask
└── TUM_dynamic
  ├── rgbd_dataset_freiburg3_walking_halfsphere
  ├── rgbd_dataset_freiburg3_walking_rpy
  ├── rgbd_dataset_freiburg3_walking_static
  └── rgbd_dataset_freiburg3_walking_xyz
      ├── accelerometer.txt
      ├── depth
      ├── depth.txt
      ├── groundtruth.txt
      ├── object_mask
      ├── rgb
      ├── rgb.txt
      └── dynamic_mask
```

For `object_mask`, instance segmentation masks can be obtained using [DEVA](https://github.com/hkchengrex/Tracking-Anything-with-DEVA) with [gaussian-grouping](https://github.com/lkeab/gaussian-grouping) or directly using [SAM](https://github.com/facebookresearch/segment-anything). During segmentation, specify the category to generate a binary image `dynamic_mask` of the dynamic object.




## Examples
```
# use
cd ./scripts

sh all.sh

# or one by one, eg:
cd ./scripts

../bin/tum_rgbd \
    ../ORB-SLAM3/Vocabulary/ORBvoc.txt \
    ../cfg/ORB_SLAM3/RGB-D/TUM/tum_freiburg3_walking_halfsphere.yaml \
    ../cfg/gaussian_mapper/RGB-D/TUM/tum_freiburg3_walking_halfsphere.yaml \
    ../../dataset/TUM_dynamic/rgbd_dataset_freiburg3_walking_halfsphere \
    ../cfg/ORB_SLAM3/RGB-D/TUM/associations/fr3_walking_halfsphere.txt \
    ../results/tumdy_rgbd/rgbd_dataset_freiburg3_walking_halfsphere 

```


## Running Evaluations
The evaluation is located in the `DOGL-SLAM-eval` directory. To run the evaluation, navigate to the `DOGL-SLAM-eval` directory and execute the appropriate script. 



## Reproducibility
There might be minor differences between the released version and the results in the paper after developing this multi-processing version. 
We run all our experiments on an RTX 4090, and the performance may differ when running with a different GPU.

## Acknowledgement
We sincerely thank the developers and contributors of the many open-source projects that our code is built upon.
- [ORB-SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3)
- [Photo-SLAM](https://github.com/HuajianUP/Photo-SLAM)
- [gaussian-grouping](https://github.com/lkeab/gaussian-grouping)


# Citation
If you found this code/work to be useful in your own research, please considering citing the following:

```bibtex
@ARTICLE{2026_doglslam,
  title={DOGL-SLAM: Dynamic Object-Level SLAM via Joint Gaussian-Landmark Tracking}, 
  author={Wu, Songyang and Zhang, Xuebo and Zhang, Shiyong and Yao, Runzhao and Song, Zhixing and Tong, Yunze and Yuan, Jing},
  journal={IEEE Robotics and Automation Letters}, 
  year={2026},
  volume={11},
  number={2},
  pages={1114-1121}
}
```