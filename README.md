<h1 align="center"> FALCON: Learning Force-Adaptive Humanoid Loco-Manipulation </h1>
<!-- FALCON: 学习力自适应的人形运动操作 -->

<div align="center">

Learning for Dynamics & Control Conference (L4DC) 2026
<b><span style="color:#e63946;">Oral</span></b>
<!-- L4DC 2026 会议口头报告 -->

</div>

<div align="center">

[[Website]](https://lecar-lab.github.io/falcon-humanoid/) <!-- 项目官网 -->
[[Arxiv]](https://lecar-lab.github.io/falcon-humanoid/) <!-- Arxiv 预印本 -->
[[Video]](https://www.youtube.com/watch?v=OfsvJ5-Fyzg) <!-- 视频演示 -->

<img src="assets/ip.png" style="height:100px;" />

[![IsaacGym](https://img.shields.io/badge/IsaacGym-Preview4-b.svg)](https://developer.nvidia.com/isaac-gym) [![Linux platform](https://img.shields.io/badge/Platform-linux--64-orange.svg)](https://ubuntu.com/blog/tag/22-04-lts) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)]()

<img src="assets/hero_static.gif" width="100%"/>

</div>

## TODO
- [x] Release training code <!-- 发布训练代码 -->
- [x] Release sim2sim code <!-- 发布仿真到仿真迁移代码 -->
- [x] Release sim2real code <!-- 发布仿真到实机迁移代码 -->


# Installation
<!-- 安装指南 -->

## IsaacGym Conda Env
<!-- IsaacGym Conda 环境配置 -->

Create mamba/conda environment, in the following we use conda for example, but you can use mamba as well.
<!-- 创建 mamba/conda 环境，下面以 conda 为例，也可使用 mamba -->

```bash
conda create -n fcgym python=3.8
conda activate fcgym
```
### Install IsaacGym
<!-- 安装 IsaacGym -->

Download [IsaacGym](https://developer.nvidia.com/isaac-gym/download) and extract:
<!-- 下载 IsaacGym 并解压 -->

```bash
wget https://developer.nvidia.com/isaac-gym-preview-4
tar -xvzf isaac-gym-preview-4
```

Install IsaacGym Python API:
<!-- 安装 IsaacGym Python API -->

```bash
pip install -e isaacgym/python
```

Test installation:
<!-- 测试安装 --> 

```bash
cd isaacgym/python/examples

python 1080_balls_of_solitude.py  # or
python joint_monkey.py
```

For libpython error:
<!-- 若遇到 libpython 错误： -->

- Check conda path:
    ```bash
    conda info -e
    ```
- Set LD_LIBRARY_PATH:
    ```bash
    export LD_LIBRARY_PATH=</path/to/conda/envs/your_env/lib>:$LD_LIBRARY_PATH
    ```

### Install FALCON
<!-- 安装 FALCON -->

```bash
git clone https://github.com/LeCAR-Lab/FALCON.git
cd FALCON

pip install -e .
pip install -e isaac_utils
```

# Motion Retargetting
<!-- 运动重定向 -->
Please refer to [PHC](https://github.com/ZhengyiLuo/PHC).
<!-- 请参考 PHC 仓库 -->

# FALCON Training
<!-- FALCON 训练 -->

## Unitree G1_29DoF
<!-- Unitree G1 29自由度机器人 -->
<details>
<summary>Training Command</summary>
<!-- 训练命令 -->

```bash
python humanoidverse/train_agent.py \
+exp=decoupled_locomotion_stand_height_waist_wbc_diff_force_ma_ppo_ma_env \
+simulator=isaacgym \
+domain_rand=domain_rand_rl_gym \
+rewards=dec_loco/reward_dec_loco_stand_height_ma_diff_force \
+robot=g1/g1_29dof_waist_fakehand \
+terrain=terrain_locomotion_plane \
+obs=dec_loco/g1_29dof_obs_diff_force_history_wolinvel_ma \
num_envs=4096 \
project_name=g1_29dof_falcon \
experiment_name=g1_29dof_falcon \
+opt=wandb \
obs.add_noise=True \
env.config.fix_upper_body_prob=0.3 \
robot.dof_effort_limit_scale=0.9 \
rewards.reward_initial_penalty_scale=0.1 \
rewards.reward_penalty_degree=0.0001
```

</details>

<details>
<summary>Evaluation Command</summary>
<!-- 评估命令 -->

```bash
python humanoidverse/eval_agent.py \
+checkpoint=<path_to_your_ckpt>
```
</details>


After around `6k` iterations, in `IsaacGym`:
<!-- 约 6k 次迭代后，在 IsaacGym 中： -->
<table>
  <tr>
    <td style="text-align: center;">
      <img src="assets/g1.gif" style="width: 100%;"/>
    </td>
  </tr>
</table>

## Booster T1_29DoF
<!-- Booster T1 29自由度机器人 -->
<details>
<summary>Training Command</summary>
<!-- 训练命令 -->

```bash
python humanoidverse/train_agent.py \
+exp=decoupled_locomotion_stand_height_waist_wbc_diff_force_ma_ppo_ma_env \
+simulator=isaacgym \
+domain_rand=domain_rand_rl_gym \
+rewards=dec_loco/reward_dec_loco_stand_height_ma_diff_force \
+robot=t1/t1_29dof_waist_wrist \
+terrain=terrain_locomotion_plane \
+obs=dec_loco/t1_29dof_obs_diff_force_history_wolinvel_ma \
num_envs=4096 \
project_name=t1_29dof_falcon \
experiment_name=t1_29dof_falcon \
+opt=wandb \
obs.add_noise=True \
env.config.fix_upper_body_prob=0.3 \
robot.dof_effort_limit_scale=0.9 \
rewards.reward_initial_penalty_scale=0.1 \
rewards.reward_penalty_degree=0.0001 \
rewards.feet_height_target=0.08 \
rewards.feet_height_stand=0.02 \
rewards.desired_feet_max_height_for_this_air=0.08 \
rewards.desired_base_height=0.62 \
rewards.reward_scales.penalty_lower_body_action_rate=-0.5 \
rewards.reward_scales.penalty_upper_body_action_rate=-0.5 \
env.config.apply_force_pos_ratio_range=[0.5,2.0]
```

</details>

<details>
<summary>Evaluation Command</summary>
<!-- 评估命令 -->

```bash
python humanoidverse/eval_agent.py \
+checkpoint=<path_to_your_ckpt>
```
</details>


After around `6k` iterations, in `IsaacGym`:
<!-- 约 6k 次迭代后，在 IsaacGym 中： -->
<table>
  <tr>
    <td style="text-align: center;">
      <img src="assets/t1.gif" style="width: 100%;"/>
    </td>
  </tr>
</table>

# FALCON Deploy
<!-- FALCON 部署 -->
We provide seamless sim2sim and sim2real deployment scripts supporting both [unitree_sdk2_python](https://github.com/unitreerobotics/unitree_sdk2_python) and [booster_robotics_sdk](https://github.com/hang0610/booster_robotics_sdk). Please refer to this [README](sim2real/README.md) for details.
<!-- 提供无缝的 sim2sim 和 sim2real 部署脚本，支持 unitree_sdk2_python 和 booster_robotics_sdk。详情请参考 sim2real/README.md -->

<table>
  <tr>
    <td style="text-align: center;">
      <img src="assets/deploy.png" style="width: 99%;"/>
    </td>
  </tr>
</table>

# FALCON Extension
<!-- FALCON 扩展 -->

## Large Workspace
<!-- 大工作空间 -->
FALCON can be easily extended to larger workspace by setting larger torso command range and base height command range. We provide the sim2sim result of Unitree G1 with larger command range as an example:
<!-- 通过设置更大的躯干命令范围和基座高度命令范围，FALCON 可轻松扩展到更大的工作空间。提供了 Unitree G1 扩大命令范围后的 sim2sim 结果示例： -->

https://github.com/user-attachments/assets/2d92000e-b990-45aa-a4ad-032fe0158eba


# Citation
<!-- 引用 -->
If you find our work useful, please consider citing us!
<!-- 如果觉得本工作有用，请考虑引用我们！ -->

```bibtex
@article{zhang2025falcon,
          title={FALCON: Learning Force-Adaptive Humanoid Loco-Manipulation},
          author={Zhang, Yuanhang and Yuan, Yifu and Gurunath, Prajwal and Gupta, Ishita and Omidshafiei, Shayegan and Agha-mohammadi, Ali-akbar and Vazquez-Chanlatte, Marcell and Pedersen, Liam and He, Tairan and Shi, Guanya},
          journal={arXiv preprint arXiv:2505.06776},
          year={2025}
        }
```

Other work also using FALCON's dual-agent framework:
<!-- 其他使用 FALCON 双智能体框架的工作： -->

```bibtex
@article{li2025softa,
          title={Hold My Beer: Learning Gentle Humanoid Locomotion and End-Effector Stabilization Control},
          author={Li, Yitang and Zhang, Yuanhang and Xiao, Wenli and Pan, Chaoyi and Weng, Haoyang and He, Guanqi and He, Tairan and Shi, Guanya},
          journal={arXiv preprint arXiv:2505.24198},
          year={2025}
        }
```

# Acknowledgement
<!-- 致谢 -->
**FALCON** is built upon [ASAP](https://github.com/LeCAR-Lab/ASAP) and [HumanoidVerse](https://github.com/LeCAR-Lab/HumanoidVerse).
<!-- FALCON 基于 ASAP 和 HumanoidVerse 构建。 -->

# License
<!-- 许可证 -->

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
<!-- 本项目采用 MIT 许可证 - 详情见 LICENSE 文件 -->