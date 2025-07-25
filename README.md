# HandsOnVLM: Vision-Language Models for Hand-Object Interaction Prediction


-----


## Installation

1. Clone the repo and Create a conda env with all the Python dependencies.

```bash
cd HandsOnVLM-release
conda create -n handsonvlm python=3.10
conda activate handsonvlm
conda install pytorch==2.4.1 torchvision==0.19.1 torchaudio==2.4.1 pytorch-cuda=12.4 cuda -c pytorch -c nvidia
pip install -e .
pip install flash-attn==2.6.3 --no-build-isolation
```

## File Structure
The file structure is listed as follows:

`hoi_forecast/`: dataset structure and helper functions for handsonvlm and hoi-forecast

`handsonvlm/`: model and training code for handsonvlm

`lita/`: model and related code for lita

`llava/`: model and related code for llava


## Train

The HandsOnVLM model only uses one stage supervised fine-tuning. The linear projection is initialized by the LLaVA pretrained weights. The training uses 8 H100 GPUs with 80GB memory.

### Prepare public checkpoints from Vicuna, LLaVA

```Shell
git clone https://huggingface.co/lmsys/vicuna-13b-v1.3
git clone https://huggingface.co/liuhaotian/llava-pretrain-vicuna-13b-v1.3
mv vicuna-13b-v1.3 vicuna-v1-3-13b
mv llava-pretrain-vicuna-13b-v1.3 llava-vicuna-v1-3-13b-pretrain
```
Similarly for the 7B checkpoints, replace `13b` with `7b` in the above commands.

### Supervised Fine-tuning

The HandsOnVLM model can be trained using the supervised fine-tuning script [here](scripts/finetune.sh). To co-train with LITA's task, also update LITA dataset directory in data_path (`--data_path`) and checkpoint directory (`./checkpoints`).
```Shell
sh scripts/finetune.sh
```


## Evaluation

We provide the evaluation pipeline for the Reasoning-based EPIC-KITCHEN-100 dataset.

```Shell
python -m handsonvlm.evaluation.evaluate --model-path ./checkpoints/handsonvlm-7b
python -m handsonvlm.evaluation.evaluate --model-path ./checkpoints/handsonvlm-7b --use_reason
```

## CLI Inference

We provide a script for chat with HandsOnVLM with images or videos.
```Shell
python -m handsonvlm.evaluation.chat --model-path ./checkpoints/handsonvlm-7b --visual-path <path-to-HandsOnVLM-release>/docs/epic_kitchen.jpg
Human: Where should my hand move to if I want to reach the oven?
```

The response could be: 
`Agent: response:  Certainly! The hand trajectory for reach the oven is as follows: <hand_traj> <hand_traj> <hand_traj> <hand_traj>  .</s>`

