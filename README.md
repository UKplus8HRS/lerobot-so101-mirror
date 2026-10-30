<h1 align="center">Reproducing SO-101: Imitation Learning → Reinforcement Learning on a Real Robot Arm</h1>

<p align="center">
  A reproduction of <a href="https://github.com/atomictan/lerobot-so101">atomictan/lerobot-so101</a>:
  teaching a 5-DOF desk robot arm to pick up a tube — first by imitation, then by
  reinforcement learning — on physical hardware, end to end.
</p>

<p align="center"><sub>Original research, data and results by atomictan · Reproduction by UKplus8HRS</sub></p>

<p align="center">
  <a href="./LICENSE"><img src="https://img.shields.io/badge/license-Apache--2.0-blue" alt="License: Apache-2.0"></a>
  <img src="https://img.shields.io/badge/python-3.12%2B-3776AB?logo=python&logoColor=white" alt="Python 3.12+">
  <a href="https://github.com/huggingface/lerobot"><img src="https://img.shields.io/badge/built%20on-%F0%9F%A4%97%20LeRobot-yellow" alt="Built on LeRobot"></a>
  <a href="https://huggingface.co/datasets/Atomictan/grab_tube_merged_v3"><img src="https://img.shields.io/badge/dataset-grab__tube__merged__v3-orange?logo=huggingface" alt="Dataset"></a>
  <a href="#-my-reproduction"><img src="https://img.shields.io/badge/reproduction-planned-lightgrey" alt="Reproduction status: planned"></a>
</p>

<p align="center">
  <img src="./media/so101/grab_tube.webp" alt="SO-101 arm picking up a tube and dropping it into a bin" width="560">
  <br>
  <em>One teleoperated demonstration from the original training dataset (side camera).<br>
  In the original project, an ACT policy trained on these 118 episodes reached 96.7% success over 30 trials.</em>
</p>

<p align="center">
  <a href="#-at-a-glance">At a glance</a> ·
  <a href="#-my-reproduction">My reproduction</a> ·
  <a href="#-original-results">Original results</a> ·
  <a href="#-whats-here">What's here</a> ·
  <a href="#-fixes-to-upstream-lerobot">Fixes</a> ·
  <a href="#-how-to-reproduce">How to reproduce</a> ·
  <a href="#-中文简介">中文简介</a>
</p>

---

This repository is my reproduction of [atomictan/lerobot-so101](https://github.com/atomictan/lerobot-so101),
a working fork of [🤗 LeRobot](https://github.com/huggingface/lerobot) that records one hobbyist's
research project: the tooling built along the way, sixteen fixes to the upstream RL stack, and a
written record of what worked and what didn't. I'm re-running the experiments and documenting my
own results in [My reproduction](#-my-reproduction). Everything under
[Original results](#-original-results) is atomictan's work.

## 📌 At a glance

| | |
|---|---|
| **Robot** | SO-101 (5-DOF, two cameras: wrist + side) |
| **Task** | "Grab the tube" — pick a tube from a mat, drop it in a bin |
| **Original project** | [atomictan/lerobot-so101](https://github.com/atomictan/lerobot-so101) |
| **Original best real-hardware result** | **96.7%** success (29/30) with ACT |
| **Dataset** | [`Atomictan/grab_tube_merged_v3`](https://huggingface.co/datasets/Atomictan/grab_tube_merged_v3) — 118 episodes, public |
| **Upstream README** | [UPSTREAM_README.md](./UPSTREAM_README.md) |

## 🧪 My reproduction

Status: **planned — no runs yet**. Results are added here only after I run them myself.

| Experiment | Original | Mine | Hardware / notes |
|------------|---------:|-----:|------------------|
| ACT v5 on `grab_tube_merged_v3`, real SO-101 | 96.7% (29/30) | — | — |
| SAC / HIL-SERL, `gym_hil` pick-cube (sim) | 0% → 100% in 1,104 episodes | — | — |
| SAC / HIL-SERL, real SO-101 | stalled at ~2,600 transitions | — | — |

Log for each run: date, hardware (GPU, robot, cameras), exact command and commit, evaluation
protocol, result, and what differed from the original.

## 📊 Original results

> These results were reported by atomictan in the original project.

### Imitation learning (ACT), real hardware — 40% → 96.7%

Three cycles of *evaluate → diagnose → collect → retrain*, changing only the data.
Architecture, hyperparameters and hardware were held constant.

| Policy | Episodes | Success | Protocol |
|--------|---------:|--------:|----------|
| v2 | 50 | 40% | 20 trials |
| v3 | 75 | 60% | 2×20 trials |
| v4 | 107 | 82.5% | 2×20 trials |
| **v5** | **118** | **96.7%** | 30 trials |

### Reinforcement learning (SAC / HIL-SERL)

The pipeline is validated: in simulation it solved a pick task unattended with zero human
intervention, 0% → 100% over 1,104 episodes. The learning curve turned out to be the most
useful artifact of the whole project:

```
ep    1-138    4.3% success   ← flat for ~26,000 transitions
ep  139-276    3.6%
ep  277-414   52.9%           ← phase transition
ep  415-552  100.0%
```

> [!TIP]
> RL improves as a **phase transition, not a gradient**. A run killed at episode 250 looks
> identical to one that will never work. This also explained the stalled real-robot run: at
> ~2,600 transitions it hadn't failed — it hadn't started.

### 📖 Full write-ups

- **[PROJECT_LOG.md](./scripts/learning/PROJECT_LOG.md)**: all three phases (imitation, simulation, RL), lessons and file index.
- **[GRASP_IMPROVEMENT_SUMMARY.md](./scripts/learning/GRASP_IMPROVEMENT_SUMMARY.md)**: the 40 → 96.7% methodology in detail.

## 🗂 What's here

Everything project-specific lives in [`scripts/learning/`](./scripts/learning/):

| File | Purpose |
|------|---------|
| `evaluate_policy.py` | Scored eval harness — keyboard start/stop, failure collection |
| `label_reward.py` | Motor-signal labelling rule → `next.reward` |
| `crop_dataset.py` | Crop/scale to the 128×128 the reward classifier requires |
| `eval_reward_classifier.py` | Per-frame P/R sweep + episode-level scoring |
| `build_offline_buffer.py` | Demonstrations → bounds-consistent offline replay buffer |
| `urdf_grid_check.py` | Validate URDF scale against a printed grid (Umeyama fit) |
| `gripper_sign_check.py` | Hardware check for gripper action polarity |
| `push_dataset_to_hub.py` | Publish the dataset with its card |
| `hilserl_grab_tube.json` | Real-arm actor/learner config |
| `gym_hil_pickcube.json` | Sim config that validated the RL pipeline |

The command list the project actually used (motor setup, calibration, teleop, recording,
training, rollout) is in [`cmd_list`](./cmd_list).

## 🔧 Fixes to upstream LeRobot

Sixteen defects found by running the RL stack against real hardware, where the
keyboard-intervention and checkpointing paths see far less use than the gamepad and
logging paths the rest of the code assumes. **Most raised no error at all:**

| Defect | What went wrong |
|--------|-----------------|
| **Resume fed the encoder images 255× darker** | `make_dataset()` yields uint8 `[0,255]`, but the resume path's bare `LeRobotDataset` yields float32 `[0,1]`. A resumed run silently trained on near-black images. |
| **Replay-buffer dumps contained no images** | `to_lerobot_dataset()` passed float images to a writer that accepts float only in `[0,1]`; every frame failed, producing an unusable resume that surfaced only much later. |
| **Convergence metrics were computed and discarded** | Episodic reward and intervention rate never printed unless an external logging service was enabled — so the two numbers that show whether HIL-SERL is working were invisible. |
| **Released keys cancelled held keys** | Human intervention derived movement from *every* key ever pressed, so a stale release zeroed an active press, with dict insertion order deciding the winner. |

See [`PROJECT_LOG.md`](./scripts/learning/PROJECT_LOG.md#4-sixteen-fixes-to-the-library-itself)
for the full list.

## 🚀 How to reproduce

Standard LeRobot installation from source — see [UPSTREAM_README.md](./UPSTREAM_README.md).
The project scripts additionally expect an SO-101 URDF and meshes; paths are set at the top
of `build_offline_buffer.py` and `urdf_grid_check.py`.

```bash
git clone https://github.com/UKplus8HRS/lerobot-so101-mirror.git
cd lerobot-so101-mirror
uv sync --locked --extra feetech            # add --extra hilserl for the RL stack
git lfs install && git lfs pull
```

Train the final ACT policy (v5) on the public dataset, as documented in
[GRASP_IMPROVEMENT_SUMMARY.md](./scripts/learning/GRASP_IMPROVEMENT_SUMMARY.md):

```bash
lerobot-train \
  --dataset.repo_id=Atomictan/grab_tube_merged_v3 \
  --policy.type=act \
  --policy.device=cuda \
  --steps=57000 \
  --policy.push_to_hub=false \
  --output_dir=outputs/train/act_grab_tube_v5
```

Score it on the real arm with the eval harness:

```bash
python scripts/learning/evaluate_policy.py \
  --policy-path outputs/train/act_grab_tube_v5/checkpoints/last/pretrained_model \
  --num-episodes 30
```

For the SO-101 hardware steps (finding ports, calibration, teleoperation, recording), see
[`cmd_list`](./cmd_list) and [AGENT_GUIDE.md §4](./AGENT_GUIDE.md).

## 🇨🇳 中文简介

<details>
<summary>点击展开</summary>

本仓库是我对 [atomictan/lerobot-so101](https://github.com/atomictan/lerobot-so101) 的**复现项目**，
基于 Hugging Face 的 [LeRobot](https://github.com/huggingface/lerobot) 库。“原始结果”中的实验、视频和数据
均归原作者 atomictan 所有；我自己的复现进展和结果记录在 [My reproduction](#-my-reproduction) 一节。

**原项目内容**：用 5 自由度的 SO-101 机械臂（腕部和侧面两个摄像头）在真机上完成“抓试管放进盒子”任务。

- **模仿学习（ACT）**：模型和超参数不变，只通过“评估 → 诊断失败 → 补采数据 → 重训”迭代数据，
  真机成功率从 40%（50 条示教）提升到 **96.7%**（118 条示教，29/30）。
- **强化学习（SAC / HIL-SERL）**：仿真中无人干预地从 0% 学到 100%。关键发现是 RL 的进步呈
  **相变式**：约 26,000 个 transition 内几乎没有进展，然后突然跃升。
- **对 LeRobot 的 16 处修复**：大多是不报错的静默 bug，例如断点续训时图像暗了 255 倍、
  replay buffer 导出缺失图像、键盘干预时松开的键会抵消按住的键。

**从哪里开始**：先读 [PROJECT_LOG.md](./scripts/learning/PROJECT_LOG.md)，再看
[`scripts/learning/`](./scripts/learning/) 中的工具和 [`cmd_list`](./cmd_list) 中的实际命令。
来源与许可说明见 [MIRROR.md](MIRROR.md)。

</details>

## 🙏 Credits

- Original SO-101 research, dataset, demonstrations and results: **atomictan** and the original
  contributors — [atomictan/lerobot-so101](https://github.com/atomictan/lerobot-so101).
- Underlying library: [🤗 LeRobot](https://github.com/huggingface/lerobot) by Hugging Face and its contributors.
- Snapshot details and asset notes: [MIRROR.md](MIRROR.md).

## 📄 License

Apache-2.0, as upstream. LeRobot is © Hugging Face and its contributors; the original project's
additions and this reproduction's additions are under the same license.
