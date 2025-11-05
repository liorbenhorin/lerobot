# Proposal for teloperation in sim

Goal is to add/substatuite this [https://huggingface.co/docs/lerobot/il_sim](https://huggingface.co/docs/lerobot/il_sim)

Suggsting

```shell
lerobot-record \
  # removing the --robot, replacing with --sim
  --sim.env=${HF_USER}/so101-teleop-table-cleanup'
  --teleop.type=so101_leader \
  --teleop.port={leaer_port} \
  --teleop.id=real_leader \
  --display_data=true \
  --dataset.repo_id=<my_username>/<my_dataset_name> \
  --dataset.num_episodes=2 \
  --dataset.single_task="put the appels in the bin"
```

This should, when called, override `lerobot_record.py` at some point 



And wrap the loaded sim.env (using `make_env()`) which will replace the real robot interface with the observations/actions from the simulation.

The environment will have to implement a robot pre/post processors.
eg:

```python
from lerobot.processors import (
    IsaacLabPreProcessor, 
    IsaacLabPostProcessor 
) # or similar
```
This will be used to map real device units to sim units, and back.


### Next steps

This can be foundation to also running Policies eval, training, benchmarking, etc



---


# Installation

### All commands below assume you start from a workspace directory of your choice (e.g., `~/workspace`).

```bash

# make conda env
conda create -n lerobot_isaac python=3.11 # Note python 3.11
conda activate env_isaaclab
conda install -y ffmpeg=7.1.1 -c conda-forge

# install isaac sim
pip install "isaacsim[all,extscache]==5.1.0" --extra-index-url https://pypi.nvidia.com

# install isaac lab
git clone git@github.com:isaac-sim/IsaacLab.git
cd IsaacLab
./isaaclab -i
cd ..

# install lerobot, but slowly to prevent conflicts
git clone https://github.com/huggingface/lerobot.git
cd lerobot
pip install -e . --no-deps
pip install --constraint <(echo -e "lxml==5.4.0\nnumpy==1.26.0\npackaging==23.0") \
    --upgrade-strategy only-if-needed \
    "datasets>=4.0.0,<4.2.0" \
    "diffusers>=0.27.2,<0.36.0" \
    "torchcodec>=0.2.1,<0.6.0" \
    "wandb>=0.20.0,<0.22.0" \
    "draccus==0.10.0" \
    "jsonlines>=4.0.0,<5.0.0" \
    "av>=15.0.0,<16.0.0" \
    "pyserial>=3.5,<4.0" \
    "pynput>=1.7.7,<1.9.0" \
    "deepdiff>=7.0.1,<9.0.0" \
    "accelerate>=1.10.0,<2.0.0" \
    "cmake>=3.29.0.1,<4.2.0" \
    "packaging>=23.0,<26.0" \
    "feetech-servo-sdk>=1.0.0,<2.0.0"
pip install --no-deps "rerun-sdk>=0.24.0,<0.27.0"
```