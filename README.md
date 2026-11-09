# Arm Action Spaces for Learning to Catch

A controlled study of how the arm action space affects reinforcement learning for catching objects in flight with a mobile manipulator and a dexterous hand.

**[Project page](https://YOUR-GITHUB-NAME.github.io/catching-action-spaces/)** | **[Videos](https://huggingface.co/spaces/anshi13110/catching-action-spaces)**

## Overview

We train policies that catch thrown objects in the simulated catching task of [Catch It!](https://arxiv.org/abs/2409.10319) (MuJoCo). The robot has a wheeled base, a six-joint xArm6 arm and a LEAP Hand with 16 joints, 12 of which the policy controls. We compare eight arm action spaces. Within each comparison, only the arm action space changes: the base and hand actions, the observation, the reward, the throws and the learner are the same.

## Arm action spaces

| Action space | Arm action | Controller |
|---|---|---|
| End-effector position | 4 (hand position and yaw steps) | Inverse kinematics, then proportional-integral-derivative joint control |
| Joint position, fixed gains | 6 (joint target steps) | Proportional-integral-derivative joint control |
| Joint position, variable gains | 7 (joint target steps and one gain multiplier) | Same, with gains scaled by the policy |
| Joint torque | 6 (joint torques) | Torques applied directly |
| Impedance, low / medium / high stiffness | 4 (hand position and yaw steps) | Operational-space impedance control with fixed stiffness |
| Variable impedance | 5 (hand steps and one stiffness multiplier) | Operational-space impedance control with stiffness set by the policy |

In every action space, gravity and Coriolis torques are compensated, and all action spaces share the same torque noise and torque limit.

## Experiments performed

### Training groups

| | Group A | Group B | Group C |
|---|---|---|---|
| Base | Mobile | Fixed | Mobile |
| Throws | Not aimed | Aimed within reach of the arm | Not aimed |
| Learner | PPO | PPO | PPO and TD-MPC2 |
| Action spaces | All eight | All eight | Impedance, medium stiffness; variable impedance |
| Seeds | 6 | 5 | 5 per learner |
| Tracking stage | 8 million steps | 4 million steps | 5 million steps |
| Catching stage | 6 million steps | 6 million steps | 5 million steps |
| Total training | 14 million steps | 10 million steps | 10 million steps |

In addition, six Group A policies of joint position with fixed gains were trained with the arm joint angles and velocities added to the observation (joint-observation check). In total, 114 policies were trained.

- **Training:** PPO with the hyperparameters of Catch It!, unchanged for every action space; TD-MPC2 with its default settings. Training has a tracking stage, with the fingers at a neutral pose, followed by a catching stage. The catch rate was measured on 200 throws at every saved checkpoint.
- **Main evaluation:** every final policy is tested with deterministic actions on a fixed set of 500 throws per group, at each of three restitution levels (0.10, 0.39 and 0.66). A throw counts as a catch when the object stays within 12 cm of the palm, in contact with the hand and slower than 0.3 m/s relative to the palm for at least 0.5 s, and is not dropped afterwards. We also record hand contact and, for the variable action spaces, the stiffness or gain multiplier.
- **Conditions not seen in training** (first 200 throws, restitution 0.39): added action delay of 1, 2 or 4 policy steps (40, 80 or 160 ms), unseen object shapes, MuJoCo default contact parameters, 1.3 times faster throws (Groups A and C), every object at 0.075 kg (Group B), the stiffness or gain multiplier held at 0.25, 1 or 1.75 (variable action spaces), and uniformly random actions as a reference.
- **Statistics:** the training seed is the unit of analysis; we report means with 95% confidence intervals, Welch's t-tests with Holm's correction, paired t-tests for the conditions not seen in training, and two one-sided equivalence tests at ±10 percentage points.
- **Note:** in Groups A and C, the resampled object mass was not fully propagated in MuJoCo, so objects fell at about 1.0 to 2.1 times gravity. All policies within a group face the same throws, so comparisons within a group are unaffected, but absolute catch rates are not comparable across groups.

## Results

Selected results are shown on the [project page](https://YOUR-GITHUB-NAME.github.io/catching-action-spaces/), and the numbers behind them, for every seed, are in [`results/`](results). The full results will be released upon publication.

## Videos

Videos of the trained policies: [Hugging Face Space](https://huggingface.co/spaces/anshi13110/catching-action-spaces).

## Code

The code, the trained policies and the evaluation scripts will be released upon publication.

## Citation

The citation for this work will be added upon publication. This work builds on the Catch It! environment; please also cite:

```bibtex
@article{zhang2024catchitlearningcatch,
  title={Catch It! Learning to Catch in Flight with Mobile Dexterous Hands},
  author={Zhang, Yuanhang and Liang, Tianhai and Chen, Zhenyang and Ze, Yanjie and Xu, Huazhe},
  year={2024},
  journal={arXiv preprint arXiv:2409.10319}
}
```
