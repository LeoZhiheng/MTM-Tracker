## Motion-to-Matching: A Mixed Paradigm for 3D Single Object Tracking

This is the official code release of "Motion-to-Matching: A Mixed Paradigm for 3D Single Object Tracking"

![](https://github.com/LeoZhiheng/MTM-Tracker/blob/main/Picture/Quantitative_results.png)

## Abstract

3D single object tracking with LiDAR points is an important task in the computer vision field. Previous methods usually adopt the matching-based or motion-centric paradigms to estimate the current target status. However, the former is sensitive to the similar distractors and the sparseness of point clouds due to relying on appearance matching, while the latter usually focuses on short-term motion clues (eg. two frames) and ignores the long-term motion pattern of target. To address these issues, we propose a mixed paradigm with two stages, named **MTM-Tracker**, which combines motion modeling with feature matching into a single network. Specifically, in the first stage, we exploit the continuous historical boxes as motion prior and propose an encoder-decoder structure to locate target coarsely. Then, in the second stage, we introduce a feature interaction module to extract motion-aware features from consecutive point clouds and match them to refine target movement as well as regress other target states. Extensive experiments validate that our paradigm achieves competitive performance on large-scale datasets (70.9\% in KITTI and 51.70\% in NuScenes).

## Method

![](https://github.com/LeoZhiheng/MTM-Tracker/blob/main/Picture/MTM-Tracker.png)

## Performance

![](https://github.com/LeoZhiheng/MTM-Tracker/blob/main/Picture/Performance++.png)
**Tip: Open source performance is tested on NVIDIA RTX 4090.** If you test on NVIDIA RTX 3090, the overall performance will also be higher than our paper, but slightly lower than 4090.

## Setup
### Install basic environment
```
conda create -n mtm python=3.9 -y
conda activate mtm

# please ensure that the pytorch version is correct
pip install torch==1.8.1+cu111 torchvision==0.9.1+cu111 torchaudio==0.8.1 -f https://download.pytorch.org/whl/torch_stable.html

# please refer to https://github.com/traveller59/spconv
pip install spconv-cu113

git clone https://github.com/LeoZhiheng/MTM-Tracker.git
cd MTM-Tracker/
pip install -r requirements.txt

python setup.py develop
```

### Install other library
```
cd ltr/ops/deformattn/
python setup.py develop

cd ltr/ops/points_op/
python setup.py develop
```

### Dataset preparation
Download the dataset from [KITTI Tracking](https://www.cvlibs.net/datasets/kitti/) and organize the downloaded files as follows:

```
MTM-Tracker                                           
|-- data                                     
|   |-- kitti                                                                          
│   │   └── training
│   │       ├── calib
│   │       ├── label_02
│   │       └── velodyne
```

## QuickStart
### Train
For training, you can customize the training by modifying the parameters in the yaml file of the corresponding model, such as '**CLASS_NAMES**'.

After configuring the yaml file, run the following command to parser the path of config file (e.g. **cfgs/kitti/mtm-ped.yaml**).

```
cd tools/
python -m torch.distributed.launch --nproc_per_node=2 train_track.py --launcher pytorch --batch_size 32 --epoch 40 --cfg_file $model_config_path --fix_random_seed --sync_bn --extra_tag base
```

### Eval
The pretrained model could be downloaded at this Link.

```
cd tools/
# for single model
python test_track.py --cfg_file $model_config_path --ckpt $your_saved_ckpt --extra_tag base

# for all saved model
python test_track.py --cfg_file $model_config_path --ckpt $your_saved_ckpt --extra_tag base --eval_all
```

## Acknowledgment
- This repo is built upon [OpenPCDet](https://github.com/open-mmlab/OpenPCDet).
- Thank [traveller59](https://github.com/traveller59) for his implementation of [Spconv](https://github.com/traveller59/spconv).
- Thank [tianweiy](https://github.com/tianweiy) for his implementation of [CenterPoint](https://github.com/tianweiy/CenterPoint).
- Thank [3bobo](https://github.com/3bobo) for his implementation of [LTTR](https://github.com/3bobo/lttr).
- Thank [jiashunwang](https://github.com/jiashunwang) for his implementation of [MRT](https://github.com/jiashunwang/MRT).

## Citation
If you find the project useful for your research, you may cite,

```
@article{li2023motion,
  title={Motion-to-Matching: A Mixed Paradigm for 3D Single Object Tracking},
  author={Li, Zhiheng and Lin, Yu and Cui, Yubo and Li, Shuo and Fang, Zheng},
  journal={IEEE Robotics and Automation Letters},
  year={2023},
  publisher={IEEE}
}
```
