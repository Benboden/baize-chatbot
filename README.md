<p align="center">
<img width="500px" alt="Project Baize" src="https://user-images.githubusercontent.com/22514219/229195563-0cddfa74-e52f-4413-b4b4-e4ba489c4b3d.png">
</p>
<hr>

## What's Baize?
Baize is an open-source chat model fine-tuned with [LoRA](https://github.com/microsoft/LoRA). It uses 100k dialogs generated by letting ChatGPT chat with itself. We also use Alpaca's data to improve its performance. We have released 7B, 13B and 30B models. 60B model coming soon. 

## Why it's called Baize?
Baize (白泽) is a mythical creature in Chinese folklore, who speaks human languages and knows everything. This is exactly what we expect from a chat model.

## Overview
⚠️ All model weights and data are for **research use ONLY**. Commercial use is **strictly prohibited**. We accept **NO responsibility or liability** for any use of our data, code or weights.

This is the repo for the Baize project, which aims to build and share an Chat LLaMA model. This repository contains:

- 54K/57K [dialogs](data) from Quora, StackOverFlow and MedQA questions
- The [code](collect.py) for collecting self-chat data
- The [code](finetune.py) for fine-tuning the model
- The [code](demo/app.py) for chat model demo (forked from [ChuanhuChatGPT](https://github.com/GaiZhenbiao/ChuanhuChatGPT))

### Model Release
- [Baize-7B](https://huggingface.co/project-baize/baize-lora-7B)
- [Baize-13B](https://huggingface.co/project-baize/baize-lora-13B)
- [Baize-30B](https://huggingface.co/project-baize/baize-lora-30B)
- [Baize Healthcare-7B](https://huggingface.co/project-baize/baize-healthcare-lora-7b)
- Baize Chinese-7B (Coming soon)

## Demo

<p align="center">
  <img alt="Demo" src="example.gif" />
</p>

You can either host it on your local machine or access the [online demo](https://huggingface.co/spaces/project-baize/baize-lora-7B). The demo fetches the [LLaMa](https://huggingface.co/decapoda-research/llama-7b-hf) model and the [LoRA weights](https://huggingface.co/project-baize/baize-lora-7B) from the Hugging Face model hub, then runs a user-friendly Gradio interface for chatting.

### Setup

First, make sure your Python version is 3.8, and then install the required packages using the command below:

```bash
cd demo
pip install -r requirements.txt
```

### Running

You can host the model on your local machine using the following command:

```bash
base_model=decapoda-research/llama-7b-hf
lora_model=project-baize/baize-lora-7B
python app.py $base_model $lora_model
```

## How to Reproduce

### Setup

1. Install dependencies

```bash
pip install -r requirements.txt
```

2. If `bitsandbytes` doesn't work, [install it from source](https://github.com/TimDettmers/bitsandbytes/blob/main/compile_from_source.md). Windows users can follow [these instructions](https://github.com/tloen/alpaca-lora/issues/17).

### Data Collecting

You can use our [released data](data) or collect the data from ChatGPT using the following command:

```bash
num_process=10 # The number of processes to collect data
max_total_tokens=500000 # Set maximum numbers of tokens to collect data
api_key=xxxxxxxxxxxxxxxxx # Set your openai api key
for ((i=0; i<$num_process; i++))
do
    python collect.py $api_key $max_total_tokens $i $num_process stackoverflow &
    python collect.py $api_key $max_total_tokens $i $num_process quora &
    python collect.py $api_key $max_total_tokens $i $num_process medical &
done
```

After collecting data, you use the following command to preprocess data:

```bash
python preprocess.py stackoverflow
python preprocess.py quora
python preprocess.py medical
```

### Use your own data

If there's a specific dataset you want to use as seeds for ChatGPT self-chatting, you can simply modify `collect.py` to load your own data. 

### Training

The fine-tuning code is designed to run on an A100-80G GPU. Th `finetune.py` script accepts three parameters: foundation model size (i.e., 7B, 13B, or 30B), batch size, learning rate and datasets.

```bash
# For the 7B model (takes about 9 hours)
python finetune.py 7b 32 0.0002 alpaca,stackoverflow,quora

# For the 13B model (takes about 16 hours)
python finetune.py 13b 16 0.0001 alpaca,stackoverflow,quora

# For the 30B model (takes about 36 hours)
python finetune.py 30b 8 0.00005 alpaca,stackoverflow,quora
```

### Citation
```bibtex
@article{xu2023baize,
  title={Baize: An Open-Source Chat Model with Parameter-Efficient Tuning on Self-Chat Data},
  author={Xu, Canwen and Guo, Daya and Duan, Nan and McAuley, Julian},
  journal={arXiv preprint},
  year={2023}
}
```

