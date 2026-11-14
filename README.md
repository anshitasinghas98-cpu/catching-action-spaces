# Arm Action Spaces for Learning to Catch

A controlled study of how the arm action space affects reinforcement learning for catching objects in flight with a manipulator and a dexterous hand.

**[Project page](https://anshitasinghas98-cpu.github.io/catching-action-spaces/)** | **[Videos](https://huggingface.co/spaces/anshi13110/catching-action-spaces)**

## Overview

We train policies that catch thrown objects in the simulated catching task of [Catch It!](https://arxiv.org/abs/2409.10319) (MuJoCo). The robot has a wheeled base, a six-joint xArm6 arm and a LEAP Hand with 16 joints, 12 of which the policy controls. We compare eight arm action spaces. Within each group and learner, the base and hand actions, the observation, the throws, the hyperparameters and the evaluation are the same; only the arm action space changes (for the joint action spaces, the arm action penalty is rescaled to match the four-dimensional end-effector action; the stiffness component is not penalized).

## Arm action spaces

| Action space | Arm action | Controller |
|---|---|---|
| End-effector position | 4 (hand position and yaw steps) | Inverse kinematics, then proportional-integral-derivative joint control |
| Joint position, fixed gains | 6 (joint target steps) | Proportional-integral-derivative joint control |
| Joint position, variable gains | 7 (joint target steps and one stiffness multiplier) | Same, with the joint gains scaled by the multiplier |
| Joint torque | 6 (joint torques) | Torques applied directly |
| Impedance, low / medium / high stiffness | 4 (hand position and yaw steps) | Operational-space impedance control with fixed stiffness |
| Variable impedance | 5 (hand position and yaw steps and one stiffness multiplier) | Operational-space impedance control with the position stiffness set by the policy |

In every action space, gravity and Coriolis torques are compensated, and all action spaces share the same torque noise and torque limit.

## Experiments performed

### Training groups

| | Group A | Group B | Group C |
|---|---|---|---|
| Base | Mobile (moved by the policy) | Fixed (commanded to stay still) | Mobile (moved by the policy) |
| Throws | Not aimed | Aimed within reach of the arm | Not aimed |
| Learner | PPO | PPO | PPO and TD-MPC2 |
| Action spaces | All eight | All eight | Impedance, medium stiffness; variable impedance |
| Seeds | 6 | 5 | 5 per learner |
| Tracking stage | 8 million steps | 4 million steps | 5 million steps |
| Catching stage | 6 million steps | 6 million steps | 5 million steps |
| Total training | 14 million steps | 10 million steps | 10 million steps |

In addition, six Group A policies of joint position with fixed gains were trained with the arm joint angles and velocities added to the observation.

- **Control experiments** (setup of Group A): *speed-matched end-effector position*, whose position change per policy step is scaled by 1.7 so that its hand speed under random actions matches that of impedance with medium stiffness (six seeds), and end-effector position and impedance with medium stiffness trained with corrected object dynamics (three seeds each).

- **Training:** training has a tracking stage, with the fingers at a neutral pose, followed by a catching stage. PPO uses the hyperparameters of Catch It!, unchanged for every action space; TD-MPC2 uses its default hyperparameters with episodic termination and a discount of 0.99, its hand actions are masked in the tracking stage, and its replay buffer is reset between the two stages. The catch rate was measured on 200 throws at every saved checkpoint.
- **Main evaluation:** every final policy is tested without exploration noise on a fixed set of 500 throws per group at restitution 0.39 (the other two levels, 0.10 and 0.66, were also recorded). A throw counts as a catch when the object stays within 12 cm of the palm, in contact with the hand (gaps of up to 50 ms allowed) and slower than 0.3 m/s relative to the palm for at least 0.5 s, and is not dropped afterwards. We also record hand contact and, for the variable action spaces, the stiffness multiplier.
- **Conditions not encountered during training** (first 200 throws, one condition at a time): added action delay, unseen object shapes, altered contact parameters, faster throws (Mobile Base) or heavier objects (Fixed Base), fixed stiffness (variable action spaces), and random actions as a reference. We also delay only the base, only the arm (including the stiffness multiplier) or only the hand command by one policy step.
- **Statistics:** the training seed is the unit of analysis; we report means with 95% confidence intervals, Welch's t-tests with Holm's correction, paired t-tests for the conditions not encountered during training, and two one-sided equivalence tests at ±10 percentage points; pairs in which both action spaces catch fewer than 3% of the throws are not tested.
- **Note:** in Groups A and C, the quantities that MuJoCo derives from the object mass were not recomputed after the mass was resampled, so heavier objects accelerated downwards faster than gravity (up to about twice). Group B recomputes them. All policies within a group face the same throws, so comparisons within a group are unaffected.

## Results

Selected results are shown on the [project page](https://anshitasinghas98-cpu.github.io/catching-action-spaces/). The full results and the data will be released upon publication.

## Names in the code

The code uses short names. This table maps them to the terms used above.

| In the code | Meaning |
|---|---|
| `setting="B"` | Mobile Base (Groups A and C) |
| `setting="C"` | Fixed Base (Group B); the base velocity command is set to zero |
| `runs/`, `runs_tv/` | Group A (`runs_tv/`: joint torque) |
| `runs_fbm/` | Group B |
| `runs_lc/` | Group C (PPO and TD-MPC2) |
| `runs_joint_obs/` | Group A, joint position with fixed gains, with arm joint angles and velocities observed |
| `ee` | End-effector position |
| `joint` | Joint position, fixed gains |
| `jvi` | Joint position, variable gains |
| `torque` | Joint torque |
| `implow`, `imp`, `imphigh` | Impedance, low / medium / high stiffness |
| `vi` | Variable impedance |
| `study_stage=1` / `study_stage=2` | Tracking stage / catching stage |
| `self.stage = "tracking"` / `"grasping"` (Catch It!) | Inside the catching stage: before / after the hand first comes within 0.25 m of the object |
| `restitution="0.1"`, `"0.4"`, `"0.7"` | Restitution levels 0.10, 0.39, 0.66 (measured) |
| `restitution="default"` | Original Catch It! contact parameters |
| `extra_latency=k` | k added policy steps of action delay (0 in training) |
| `STUDY_EE_MOVE_SCALE` | Position step of speed-matched end-effector position |
| `STUDY_FIX_MASS=1` | Corrected object dynamics |

## Videos

Videos of the trained policies: [Hugging Face Space](https://huggingface.co/spaces/anshi13110/catching-action-spaces). Each run loads the final trained policy (checkpoint) of the chosen action space and seed and simulates it live in the same MuJoCo environment used for training and evaluation.

## Code

The trained policies can be run in the Hugging Face Space. The checkpoints, the environment and the evaluation code will be released upon publication.

## Citation

The citation for this work will be added upon publication. This work builds on the Catch It! environment; please also cite:

```bibtex
@inproceedings{zhang2025catchit,
  title={Catch It! Learning to Catch in Flight with Mobile Dexterous Hands},
  author={Zhang, Yuanhang and Liang, Tianhai and Chen, Zhenyang and Ze, Yanjie and Xu, Huazhe},
  booktitle={IEEE International Conference on Robotics and Automation (ICRA)},
  year={2025}
}
```

