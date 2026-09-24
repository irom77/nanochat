# 🚀 NanoChat - Pretrained & Fine-Tuned (Depth-12)

Welcome to your personalized **NanoChat** repository! This project hosts a custom Depth-12 (286M parameter) GPT-2 grade model, pretrained and fine-tuned from scratch on RunPod.

- **Upstream Repository Details & Guide:** See **[README_original.md](README_original.md)** for the original Karpathy framework features and codebase guides.
- **Hugging Face Model Repository:** Both your pretrained Base and conversational SFT weights (totaling ~3.8 GB) are safely hosted and publicly available at **[huggingface.co/niuk77/nanochat-d12](https://huggingface.co/niuk77/nanochat-d12)**.

---

# RunPod Training Log & Guide

This file documents the remote training setup created on RunPod, including how to connect, monitor, and manage the active pod.

---

## 🚀 Active Pod Configuration

- **Pod Name:** `nanochat-training`
- **Pod ID:** `s06fmh4bdw8vuy`
- **GPU:** 1x NVIDIA GeForce RTX 4090 (24GB VRAM)
- **Hourly Cost:** $0.74/hr (Secure Cloud)
- **Template:** Runpod Pytorch 2.4.0 (`runpod-torch-v240`)
- **Docker Image:** `runpod/pytorch:2.4.0-py3.11-cuda12.4.1-devel-ubuntu22.04`

---

## 🛠️ What Was Set Up

1. **Codebase Synchronization:**
   - Synced the local `/home/irom/nanochat/` directory to `/workspace/nanochat/` on the remote pod using `rsync` (ignoring `.git`, `.venv`, `.cache`, and `__pycache__`).
2. **Environment & Dependencies:**
   - Installed `screen` on the pod to run persistent background tasks.
   - Installed `python3-dev` on the pod to resolve PyTorch Triton compiler issues (missing `Python.h`).
   - Installed `uv` (fast Python package manager) in the pod.
   - Created a local virtual environment (`.venv`) and installed GPU dependencies (`uv sync --extra gpu`).
3. **Dataset Pre-downloading:**
   - Downloaded all 171 base pretraining shards (~15 GB of ClimbMix data) directly to `/root/.cache/nanochat` in less than 5 seconds using RunPod's high-speed connection.
4. **Base Model Training:**
   - Completed standard Depth-12 pretraining (1,680 iterations) inside a `screen` session named `training`.
   - Reached a validation compression (bits-per-byte) of `0.868`.
5. **Supervised Fine-Tuning (SFT):**
   - Executed SFT to train the base model for chat and conversational capabilities.
   - SFT checkpoints generated and saved successfully.

---

## 🔌 How to Connect (SSH)

Run the following command from your local machine to SSH into the remote pod:

```bash
ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673
```

---

## 📊 How to Monitor Training

You can monitor the training from your local terminal or by SSHing into the pod first.

### Option A: From your Local Machine (No interactive SSH needed)

1. **Watch the logs live (tail):**
   - **For Base Pretraining:**
     ```bash
     ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673 "tail -f /workspace/nanochat/training.log"
     ```
   - **For SFT Fine-Tuning:**
     ```bash
     ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673 "tail -f /workspace/nanochat/sft.log"
     ```

2. **Check GPU usage & temperature (nvidia-smi):**
   ```bash
   ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673 "nvidia-smi"
   ```

### Option B: Interactive Monitoring (Inside the Pod)

Once you have SSH'd into the pod (`ssh ...`):

1. **Attach to the running screen session:**
   ```bash
   screen -r training
   ```
   *To detach and leave it running in the background, press `Ctrl + A`, then `D`.*

2. **Watch the GPU utility live:**
   ```bash
   watch -n 1 nvidia-smi
   ```

---

## 🛑 How to Stop or Restart

If you need to stop, kill, or restart the training process:

1. **To kill the screen session and stop all training:**
   ```bash
   screen -S training -X quit
   ```
2. **To kill python processes directly:**
   ```bash
   pkill -f "python"
   ```

---

## 💬 Testing the Model & Prompts Guide

You can prompt or chat with either the **Pretrained Base Model** or the **Conversational SFT Model** in two ways:
1. **Directly from your local laptop terminal** (one-off single prompt via SSH)
2. **Interactively from inside the pod** (multi-turn conversation)

---

### 📥 One-Off Prompting (From Local Laptop via SSH)

You can query the model directly from your local machine's shell by adding the `-p "<prompt>"` flag:

#### 1. Pretrained Base Model (Raw Completion)
```bash
ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673 "cd /workspace/nanochat && source .venv/bin/activate && python -m scripts.chat_cli -i base -p 'Why is the sky blue?'"
```
*Expected completion behavior:* Starts repeating phrases or babbling, as it is just predicting the next word without instruction-tuning.

#### 2. Conversational SFT Model (Instruction Answer)
```bash
ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673 "cd /workspace/nanochat && source .venv/bin/activate && python -m scripts.chat_cli -i sft -p 'Why is the sky blue?'"
```
*Expected completion behavior:* Responds in a cohesive, helpful assistant format.

---

### 💬 Interactive Chat (From inside the RunPod Container)

First, SSH into your pod (`ssh -i ... root@157.157.221.29 -p 32673`), navigate to the repository, and activate your environment:
```bash
cd /workspace/nanochat
source .venv/bin/activate
```

#### 1. Interactive chat with raw Base Model:
```bash
python -m scripts.chat_cli -i base
```
#### 2. Interactive chat with Conversational SFT Model:
```bash
python -m scripts.chat_cli -i sft
```
*Type `exit` or `quit` to exit, and `clear` to start a new chat history.*

---

### 🧪 Model Comparison Examples

#### Prompt: `"Why is the sky blue?"`
- **Base Model (Raw):**
  > *The sky is a blueish blue because of the water that washes over it. It is not because of the sun but because of the water... [repeats]*
- **SFT Model (Chat):**
  > *The sky is blue, a vibrant, unforgiving landscape that is steeped in history and mythology. This blue sky is a result of the interplay of light, air, and water...*

#### Try these other test prompts & Actual SFT Responses:

- **Prompt:** `"What is the capital of France?"`
  - **SFT Response:**
    > *The capital of France, Paris, is a city that is renowned for its charming architecture, charming boutiques, and stunning natural beauty. With a rich history, the city has become a hub for trade, art, and culture...*

- **Prompt:** `"What is the chemical symbol of gold?"`
  - **SFT Response:**
    > *The chemical symbol of gold is Au, which is the Latin word for "gold." This symbol is derived from the Latin word for "gold," meaning "gold" or "gold gold," which means "gold is a metal of great rarity and beauty."*

- **Prompt:** `"What is the opposite of hot?"`
  - **SFT Response:**
    > *Hot is a term that refers to the cooking process where food is cooked at a high temperature, often between 400°F and 140°F, and then cooled to a low temperature... [The model goes on a culinary babble about slow-cookers!]*

---

## 📊 Quick Model Evaluation Guide

To run a rapid, targeted evaluation benchmark without evaluating everything (which can take a while), use the `-a` (task-name) and `-x` (max-problems) parameters.

### 1. From your Local Laptop Terminal (using SSH)
Execute this command on your laptop to run a fast 10-problem test on the `ARC-Easy` benchmark of your SFT model:
```bash
ssh -i /home/irom/.runpod/ssh/runpodctl-ssh-key root@157.157.221.29 -p 32673 "cd /workspace/nanochat && source .venv/bin/activate && python -m scripts.chat_eval -i sft -a 'ARC-Easy|ARC-Challenge|MMLU|GSM8K|HumanEval' -x 10"
```

### 2. From inside the RunPod Container
Once SSH'd in:
```bash
python -m scripts.chat_eval -i sft -a "ARC-Easy|ARC-Challenge|MMLU|GSM8K|HumanEval" -x 10
```

### 🏆 Fast Evaluation Results (10-problem SFT snapshot):

| Task / Benchmark | Accuracy | Type | Description |
| :--- | :---: | :---: | :--- |
| **`MMLU`** | **50.00%** | Multiple Choice | Academic & professional knowledge questions |
| **`ARC-Easy`** | **50.00%** | Multiple Choice | Elementary-level science questions |
| **`ARC-Challenge`**| **30.00%** | Multiple Choice | Harder, reasoning-heavy science questions |
| **`GSM8K`** | **0.00%** | Open-Ended | Grade-school math word problems |
| **`HumanEval`** | **0.00%** | Open-Ended | Python code-generation challenges |
| **`ChatCORE` Score**| **0.1467** | **Aggregate** | Mean centered accuracy score (Up from base model's 0.1399!) |

### Available Evaluation Tasks:
- `ARC-Easy` (Multiple Choice)
- `ARC-Challenge` (Multiple Choice)
- `MMLU` (Multiple Choice)
- `GSM8K` (Math / Tool Use)
- `HumanEval` (Python Code Generation)

---

## 📦 Copying Results Back to Local Machine

When training completes and you want to download checkpoints or results back to your laptop:

Run this command **on your local laptop** to download both the Base and SFT checkpoint directories:
```bash
rsync -avz -e "ssh -i ~/.runpod/ssh/runpodctl-ssh-key -p 32673" root@157.157.221.29:/root/.cache/nanochat/base_checkpoints/ ~/nanochat/base_checkpoints/
rsync -avz -e "ssh -i ~/.runpod/ssh/runpodctl-ssh-key -p 32673" root@157.157.221.29:/root/.cache/nanochat/chatsft_checkpoints/ ~/nanochat/chatsft_checkpoints/
```

---

## 🤗 Hugging Face Backup

Both your pretrained **Base** and fine-tuned **SFT** checkpoints have been successfully uploaded and are publicly/safely backed up on the Hugging Face Hub:

- **Hugging Face Repository:** **`niuk77/nanochat-d12`**
- **SFT Checkpoints Folder:** `/sft`
- **Base Checkpoints Folder:** `/base`

You can view, share, or download them directly from the web interface at:
👉 **[huggingface.co/niuk77/nanochat-d12](https://huggingface.co/niuk77/nanochat-d12)**

---

### 🗂️ What Was Downloaded to Your Laptop:

For both **Base** and **SFT** runs, a total of **~3.8 GB** of data has been transferred to your laptop, structured under `~/nanochat/base_checkpoints/d12/` and `~/nanochat/chatsft_checkpoints/d12/`:

1.  **`model_001680.pt` / `model_000007.pt` (757 MB each):**
    -   Contains the **286 million** model parameters (weights).
    -   Saved in **`bfloat16`** precision format (`2 bytes` per parameter, resulting in the ~572 MB pure parameter footprint + layer scales/heads).
2.  **`optim_001680_rank0.pt` / `optim_000007_rank0.pt` (1.2 GB each):**
    -   Contains the **AdamW optimizer states** (momentum & variance buffers) for restarting training.
    -   Takes up more space as optimizer states require 32-bit float accuracy (`4 bytes` per tracked parameter).
3.  **`meta_001680.json` / `meta_000007.json` (~1 KB each):**
    -   JSON files specifying configuration (e.g., number of layers, heads, dimension) and training meta-metrics (like validation loss).

---

## 🔄 Resuming or Restoring on a New Pod (Future Training / Testing)

Yes, absolutely! Because you have successfully downloaded the checkpoints (`.pt` weights and `.pt` optimizer files) to your laptop, you can confidently delete your active pod now to save costs. 

In the future, you can spin up a new pod and fully restore your model to:
- **Continue testing / chatting** with the model.
- **Resume pretraining further** (starting right where you left off).
- **Run more SFT fine-tuning epochs**.

---

### Step 1: Re-Sync Your Codebase to the New Pod
Deploy a new pod, install `rsync`, and sync your codebase from your laptop:
```bash
# On your local laptop:
rsync -rltvz -e "ssh -i ~/.runpod/ssh/runpodctl-ssh-key -p <NEW_PORT>" \
  --exclude=".git" --exclude=".venv" --exclude="__pycache__" --exclude=".cache" \
  ~/nanochat/ root@<NEW_IP>:/workspace/nanochat/
```

### Step 2: Upload Your Local Checkpoints Back to the Pod
Upload your saved checkpoints from your laptop back to the new pod's cache directory:
```bash
# On your local laptop:
rsync -avz -e "ssh -i ~/.runpod/ssh/runpodctl-ssh-key -p <NEW_PORT>" \
  ~/nanochat/base_checkpoints/ root@<NEW_IP>:/root/.cache/nanochat/base_checkpoints/

rsync -avz -e "ssh -i ~/.runpod/ssh/runpodctl-ssh-key -p <NEW_PORT>" \
  ~/nanochat/chatsft_checkpoints/ root@<NEW_IP>:/root/.cache/nanochat/chatsft_checkpoints/
```

### Step 3: Run / Continue From Your Uploaded Checkpoints

#### 💬 To Chat with your Uploaded Model:
Simply run the chat CLI inside the new pod; it will auto-detect the uploaded checkpoints:
```bash
python -m scripts.chat_cli -i sft
```

#### 📈 To Resume Pretraining (Further Pretraining):
Run the pretraining script and use the `--resume-from-step` flag. This will automatically load your weights AND load the FP32 AdamW optimizer momentums (`optim_001680_rank0.pt`) to seamlessly resume training:
```bash
torchrun --standalone --nproc_per_node=1 -m scripts.base_train -- \
  --depth=12 \
  --target-param-data-ratio=8 \
  --device-batch-size=16 \
  --resume-from-step=1680
```
