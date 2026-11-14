# Depth Estimation

This repository contains my work on stereo and monocular depth estimation.

The project started as part of a group robotics vision project at university. My part of the project was camera calibration and depth estimation using stereo images. After working on that, I became interested in other approaches to depth estimation and came across **FastDepth**, a paper on estimating depth from a single RGB image. I found the idea interesting, and decided to try implementing the paper myself.

## Projects

### 1. Stereo Depth Estimation

Developed as part of a university group project on robotics vision. My contribution focused on camera calibration and depth estimation.

The implementation uses OpenCV and includes:

- Camera calibration using chessboard patterns
- Stereo camera calibration
- Image rectification
- Disparity estimation using StereoSGBM
- Depth reconstruction using the reprojection matrix (Q)

### 2. FastDepth: Monocular Depth Estimation

An attempt to reimplement **FastDepth: Fast Monocular Depth Estimation on Embedded Systems** (Wofk et al., ICRA 2019).

Unlike stereo vision, which uses two images to estimate depth, monocular depth estimation predicts depth from a single RGB image. I found this approach interesting and wanted to understand how it could be implemented using deep learning.

The implementation is based on the paper's lightweight encoder-decoder architecture. NetAdapt pruning was skipped.

## Architecture Overview

FastDepth uses an encoder-decoder network designed for low-latency monocular depth estimation on resource-constrained embedded hardware.

- **Encoder:** MobileNet (v1), ImageNet-pretrained, with the final pooling and FC layers removed. A 224×224×3 input produces a 7×7×1024 feature map.
- **Decoder (NNConv5):** five upsampling layers. Each is a 5×5 depthwise convolution followed by a 1×1 pointwise convolution that halves the channel count, then 2× nearest-neighbor upsampling. Channels go 1024 → 512 → 256 → 128 → 64 → 32.
- **Final layer:** a single 1×1 pointwise convolution producing 1 channel (224×224×1 depth).
- **Skip connections:** additive, from the encoder into the outputs of the middle three decoder layers (112×112×64, 56×56×128 and 28×28×256 features).

## Evaluation Results

Evaluated on the **NYU Depth v2** dataset. Our final model significantly improves upon the baseline—reducing RMSE by **12.7%** and increasing δ1 accuracy by **6.5 percentage points**—achieving performance closely aligned with the original unpruned FastDepth paper.

| Model | RMSE | δ1 | Notes |
| :--- | :---: | :---: | :--- |
| **Baseline** | 0.708 | 0.673 | Initial implementation |
| **V2 (Final)** | **0.618** | **0.738** | Final tuned model |
| **FastDepth (Paper)** | 0.599 | 0.775 | Official unpruned benchmark (Wofk et al., ICRA 2019) 

**Paper:** [FastDepth: Fast Monocular Depth Estimation on Embedded Systems (PDF)](https://fastdepth.mit.edu/2019_icra_fastdepth.pdf)

**Original implementation:** [GitHub Repository](https://github.com/dwofk/fast-depth)



## References

Wofk, D., et al. (2019). *FastDepth: Fast Monocular Depth Estimation on Embedded Systems*. IEEE International Conference on Robotics and Automation (ICRA).
