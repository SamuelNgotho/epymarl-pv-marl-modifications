# EPyMARL modifications for multi-agent PV reactive power control

<!--
This repository contains the changes made to the EPyMARL library for the MARL agents of the paper

> S. Ngotho, M. Conlon, and M. Basu, "Centralized-Zonal Voltage Management Under Uncertainty: A Two-Timescale Robust MPC and Multi-Agent Reinforcement Learning Framework for PV–Energy Storage Coordination," IEEE Access (under review).
-->

This repository contains modifications of the EPyMARL library and a MAPPO training configuration for multi-agent reactive power control of PV inverters.

Only the modifications of EPyMARL files and the EPyMARL training configuration are provided here.

## Base version

- Library: EPyMARL, https://github.com/uoe-agents/epymarl
- Commit: `cbc38c09588064eab978501d0f12c2cf58fa7fc2` (24 September 2024)

## How to apply

```bash
git clone https://github.com/uoe-agents/epymarl.git
cd epymarl
git checkout cbc38c09588064eab978501d0f12c2cf58fa7fc2
git apply epymarl_cbc38c0_modifications.patch
```

## Changes

| File | Change |
|---|---|
| `src/envs/gymma.py` | The Gymnasium wrapper supports agents whose observation vectors have different lengths. Observations are zero padded to the longest one, sizes use `flatdim`, and `get_obs_agent` returns the observation (the original raised it). |
| `src/run.py` | The policies are updated only after `batch_size` new episodes from the current policy. With `save_best_only: True`, only the checkpoint with the highest mean validation return is kept. The run name is a valid Windows path. |
| `src/components/action_selectors.py` | In evaluation (`test_mode` with `test_greedy: True`), the most probable action is selected instead of a sampled one. |
| `src/runners/episode_runner.py` | The episode loop is reorganised. The stored state, observation, action, reward, and termination data are the same as in the original. |
| `src/main.py` | Shorter results path, needed on Windows. |
| `src/config/algs/mappo_pv.yaml` | New configuration file with the MAPPO training settings. |

The MAPPO learner, the actor, and the critic are not changed.

## Training settings

All settings are in `src/config/algs/mappo_pv.yaml`. Settings not listed there are the EPyMARL defaults of this commit.

| Setting | Value |
|---|---|
| Learning rate | 3e-4 |
| Discount factor | 0.99 |
| Clip range | 0.2 |
| PPO epochs per update | 5 |
| Entropy coefficient | 0.01 |
| n-step return | 5 |
| Batch size / buffer size | 16 / 16 episodes |
| Actor | GRU, hidden size 128, parameters shared by all agents, one-hot agent index |
| Critic | Centralised (`cv_critic`) |
| Training steps | 1e6 |
| Validation | 2 episodes every 4608 steps, best checkpoint kept |
| Random seeds | 1, 2, 3 |

## Configurations

The four MARL configurations use the same settings and differ only in the observation and reward options.

| Configuration | Observation | Reward | Options |
|---|---|---|---|
| LI | local | individual | `env_args.full_observable=False common_reward=False` |
| LS | local | shared | `env_args.full_observable=False common_reward=True` |
| GI | global | individual | `env_args.full_observable=True common_reward=False` |
| GS | global | shared | `env_args.full_observable=True common_reward=True` |

Example (LI, seed 1):

```bash
python src/main.py --config=mappo_pv --env-config=gymma with env_args.key=<environment key> env_args.time_limit=288 env_args.full_observable=False common_reward=False seed=1
```
