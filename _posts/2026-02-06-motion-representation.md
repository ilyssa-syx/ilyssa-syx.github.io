---
layout: post
title: "Motion Representation Summary"
---

## Basic Knowledge for SMPL

### smpl_poses and smpl_trans

`smpl_poses[:3]`: orientation of root relative to world coordinate
 - axis-angle. $p_{\text{world}}=Rp_{\text{root\_local}} + t$
`smpl_poses[3:]`: every joint's rotation relative to root
 - axis-angle
`smpl_trans`: 3d position of root, global coordinate
`smpl.forward()` also gets 3d joints positions, global coordinate, not relative to pelvis


## `raw_pose_processing.ipynb` in [HumanML3D](https://github.com/EricGuo5513/HumanML3D) Repo

Input: SMPL(-H)
Output: 3D joints sequence

Operations:
1. downsample to 20 fps
2. use `body.Jtr()` to get 3d joints positions
 - every joint's position in global coordinate, not relative to pelvis
3. change axis from `x,y,z` to `x,z,y`
4. if not from `humanact12`: from `x,z,y` to `-x,z,y`
5. augmentation: `data_m = swap_left_right(data)`, and save both data and data_m
