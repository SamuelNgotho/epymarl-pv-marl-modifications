# EPyMARL modifications for multi-agent PV reactive power control

This repository contains the changes made to the EPyMARL library for the MARL agents of the paper

> S. Ngotho, M. Conlon, and M. Basu, "Centralized-Zonal Voltage Management Under Uncertainty: A Two-Timescale Robust MPC and Multi-Agent Reinforcement Learning Framework for PV–Energy Storage Coordination," IEEE Access (under review).

Only modifications of existing EPyMARL files are provided here.

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

The MAPPO learner, the actor, and the critic are not changed.
