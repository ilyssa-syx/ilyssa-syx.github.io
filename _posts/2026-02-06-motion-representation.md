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


## *raw_pose_processing.ipynb* in [HumanML3D](https://github.com/EricGuo5513/HumanML3D) Repo

Input: SMPL(-H)

Output: 3D joints sequence

Operations:
1. downsample to 20 fps
2. use `body.Jtr()` to get 3d joints positions
 - every joint's position in global coordinate, not relative to pelvis
3. change axis from `x,y,z` to `x,z,y`
4. if not from `humanact12`: from `x,z,y` to `-x,z,y`
5. augmentation: `data_m = swap_left_right(data)`, and save both data and data_m

## perform *raw_pose_processing.ipynb* for AIST++

AIST++ is Y-up!
 - directly use the following script:

```python
from smplx import SMPL

smpl = SMPL(model_path="", gender="neutral", num_betas=10).to("cuda")

# load data from .pkl file
pos = data['smpl_trans']
q = data['smpl_poses']

def process_single(pos, q):
    """
    pos: numpy array, shape (seq_len, 3) - root positions
    q: numpy array, shape (seq_len, 72) - joint rotations
    """ 
    
    device = next(smpl.parameters()).device  # cuda:0
    root_pos = torch.as_tensor(pos, dtype=torch.float32, device=device)  
    local_q  = torch.as_tensor(q,   dtype=torch.float32, device=device)  
    print(local_q.shape)
    
    positions = smpl.forward(global_orient=local_q[:, :3], body_pose=local_q[:, 3:], transl=root_pos)
    
    joints = positions.joints
    return positions_downsampled.detach().cpu().numpy()
```

Also remember to calculate mean and variance for AIST++ dataset!

