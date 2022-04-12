# GDR-Net
This repo provides the PyTorch implementation of the work:

**Gu Wang, Fabian Manhardt, Federico Tombari, Xiangyang Ji. GDR-Net: Geometry-Guided Direct Regression Network for Monocular 6D Object Pose Estimation. In CVPR 2021.**
[[Paper](https://openaccess.thecvf.com/content/CVPR2021/html/Wang_GDR-Net_Geometry-Guided_Direct_Regression_Network_for_Monocular_6D_Object_Pose_CVPR_2021_paper.html)][[ArXiv](http://arxiv.org/abs/2102.12145)][[Video](https://www.bilibili.com/video/BV1dU4y1G7Ku?share_source=copy_web)][[bibtex](#Citation)]


## News
* [2021/08] An extension of this work, [SO-Pose](https://arxiv.org/abs/2108.08367) by Di et al. (ICCV 2021), has been released ([SO-Pose code](https://github.com/shangbuhuan13/SO-Pose), [mirror](https://github.com/THU-DA-6D-Pose-Group/SO-Pose)).

## Overview
<p align="center">
<img src='assets/gdrn_architecture.png' width='800'>
<p>



## Requirements
* Ubuntu 16.04/18.04, CUDA 10.1/10.2, python >= 3.6, PyTorch >= 1.6, torchvision
* Install `detectron2` from [source](https://github.com/facebookresearch/detectron2)
* `sh scripts/install_deps.sh`
* Compile the cpp extension for `farthest points sampling (fps)`:
    ```
    sh core/csrc/compile.sh
    ```

## Datasets
Download the 6D pose datasets (LM, LM-O, YCB-V) from the
[BOP website](https://bop.felk.cvut.cz/datasets/) and
[VOC 2012](https://pjreddie.com/projects/pascal-voc-dataset-mirror/)
for background images.
Please also download the `image_sets` and `test_bboxes` from
here ([BaiduNetDisk](https://pan.baidu.com/s/1gGoZGkuMYxhU9LBKxuSz0g), [OneDrive](https://1drv.ms/u/s!Ah83ZdJvIaBnnjqVy9Eyn0yxDb8i?e=0Q3qRU), password: qjfk).

The structure of `datasets` folder should look like below:
```
# recommend using soft links (ln -sf)
datasets/
├── BOP_DATASETS
    ├──lm
    ├──lmo
    ├──ycbv
├── lm_imgn  # the OpenGL rendered images for LM, 1k/obj
├── lm_renders_blender  # the Blender rendered images for LM, 10k/obj (pvnet-rendering)
├── VOCdevkit
```

* `lm_imgn` comes from [DeepIM](https://github.com/liyi14/mx-DeepIM), which can be downloaded here ([BaiduNetDisk](https://pan.baidu.com/s/1e9SJoqb0EmyqVLEVlbNQIA), [OneDrive](https://1drv.ms/u/s!Ah83ZdJvIaBnoEz5BM4Ho6_W_UUA?e=pj7Y7i), password: vr0i).

* `lm_renders_blender` comes from [pvnet-rendering](https://github.com/zju3dv/pvnet-rendering), note that we do not need the fused data.


## Training GDR-Net
`./core/gdrn_modeling/train_gdrn.sh <config_path> <gpu_ids> (other args)`

Example:
```
./core/gdrn_modeling/train_gdrn.sh configs/gdrn/lm/a6_cPnP_lm13.py 0  # multiple gpus: 0,1,2,3
# add --resume if you want to resume from an interrupted experiment.
```


Our trained GDR-Net models can be found here ([BaiduNetDisk](https://pan.baidu.com/s/1_MEZJBd67hdxcE8JzmnOtA), [OneDrive](https://1drv.ms/u/s!Ah83ZdJvIaBnnj88MpeoTjXtge8R?e=hzjxLE), password: kedv). <br />
<sub><sup>(Note that the models for BOP setup in the supplement were trained using a refactored version of this repo (not compatible), they are slightly better than the models provided here.)</sup></sub>


## Evaluation
`./core/gdrn_modeling/test_gdrn.sh <config_path> <gpu_ids> <ckpt_path> (other args)`

Example:
```
./core/gdrn_modeling/test_gdrn.sh configs/gdrn/lmo/a6_cPnP_AugAAETrunc_BG0.5_lmo_real_pbr0.1_40e.py 0 output/gdrn/lmo/a6_cPnP_AugAAETrunc_BG0.5_lmo_real_pbr0.1_40e/gdrn_lmo_real_pbr.pth
```


## Citation
If you find this useful in your research, please consider citing:
```
@InProceedings{Wang_2021_GDRN,
    title     = {{GDR-Net}: Geometry-Guided Direct Regression Network for Monocular 6D Object Pose Estimation},
    author    = {Wang, Gu and Manhardt, Fabian and Tombari, Federico and Ji, Xiangyang},
    booktitle = {IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month     = {June},
    year      = {2021},
    pages     = {16611-16621}
}
```

Results Linemod:
objects  ape     benchvise  camera  can     cat     driller  duck    eggbox  glue    holepuncher  iron    lamp   phone  Avg(13)
ad_2     14.95   43.94      28.73   35.43   26.35   38.65    16.81   60.85   46.14   25.12        41.98   50.77  27.86  35.20
ad_5     48.67   84.97      72.35   81.10   67.86   84.24    52.77   95.68   90.93   64.03        86.11   90.88  71.95  76.27
ad_10    78.95   98.74      94.12   97.64   92.91   97.72    82.72   99.72   99.23   88.49        97.85   98.85  93.77  93.90
rete_2   62.10   65.57      66.96   65.65   57.29   59.96    58.69   75.31   45.17   64.80        55.98   67.37  51.75  61.27
rete_5   96.76   98.06      98.63   97.54   97.80   96.63    97.28   98.78   94.31   98.57        94.79   98.75  94.52  97.11
rete_10  99.62   99.90      99.90   99.90   99.80   99.90    99.72   99.72   99.71   99.71        99.69   99.71  99.06  99.72
re_2     62.76   66.83      67.35   66.44   58.28   61.84    59.44   75.96   46.72   65.75        56.89   68.43  53.07  62.29
re_5     96.86   98.06      98.63   97.54   97.80   97.22    97.46   98.78   94.31   98.67        94.79   98.75  94.52  97.18
re_10    99.62   99.90      99.90   99.90   99.80   99.90    99.72   99.72   99.71   99.71        99.69   99.71  99.06  99.72
te_2     95.24   97.38      97.06   97.64   97.21   94.95    96.24   97.18   91.60   95.72        94.79   96.16  92.82  95.69
te_5     99.33   99.90      99.80   99.80   99.80   99.31    99.62   99.81   99.81   99.62        100.00  99.71  99.15  99.67
te_10    99.90   100.00     99.90   99.90   100.00  100.00   100.00  99.91   100.00  99.90        100.00  99.81  99.91  99.94
proj_2   94.00   79.24      87.16   84.35   94.31   64.82    93.43   93.15   88.90   95.53        72.52   69.77  78.56  84.29
proj_5   98.67   99.22      99.41   99.31   99.60   98.32    98.97   98.87   99.23   99.62        96.94   97.70  97.73  98.74
proj_10  100.00  100.00     99.90   100.00  99.90   99.90    99.91   99.91   99.90   99.90        100.00  99.71  99.72  99.90
re       2.04    1.81       1.82    1.85    1.98    1.97     2.01    1.60    2.40    1.84         2.16    1.83   2.33   1.97
te       0.01    0.01       0.01    0.01    0.01    0.01     0.01    0.01    0.01    0.01         0.01    0.01   0.01   0.01
