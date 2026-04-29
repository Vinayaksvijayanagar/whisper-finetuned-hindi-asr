<div align="center">

<!-- Animated Header -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1a7f4b,100:0d4f2e&height=200&section=header&text=Hindi%20Speech-to-Text&fontSize=40&fontColor=ffffff&animation=fadeIn&fontAlignY=35&desc=Fine-tuned%20Whisper%20Small%20%7C%20MySivi%20AI%20Intern%20Assignment&descAlignY=55&descSize=16" width="100%"/>

<!-- Badges -->
<p>
  <img src="https://img.shields.io/badge/Model-Whisper%20Small-1a7f4b?style=for-the-badge&logo=openai&logoColor=white"/>
  <img src="https://img.shields.io/badge/Language-Hindi%20%F0%9F%87%AE%F0%9F%87%B3-FF9933?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Framework-HuggingFace-FFD21E?style=for-the-badge&logo=huggingface&logoColor=black"/>
  <img src="https://img.shields.io/badge/GPU-Google%20Colab%20T4-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=black"/>
  <img src="https://img.shields.io/badge/UI-Gradio%20%2B%20Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white"/>
  <img src="https://img.shields.io/badge/License-MIT-blue?style=for-the-badge"/>
</p>

<!-- Demo Links -->
<p>
  <a href="https://colab.research.google.com/drive/YOUR_NOTEBOOK_ID">
    <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab" height="28"/>
  </a>
  &nbsp;&nbsp;
  <a href="https://youtu.be/YOUR_VIDEO_ID">
    <img src="https://img.shields.io/badge/▶%20Demo%20Video-FF0000?style=for-the-badge&logo=youtube&logoColor=white" height="28"/>
  </a>
</p>

</div>

---

## 🎯 What is this?

> Fine-tuning **OpenAI Whisper Small** on **Mozilla Common Voice Hindi** dataset to improve Hindi speech recognition accuracy — built as part of the **MySivi AI Engineer Intern Assignment**.

MySivi is a Y-Combinator backed English learning app used by **10M+ Hindi speakers** in India. Accurate Hindi STT is critical for features like pronunciation feedback and conversation practice.

---

## 📊 Results

<div align="center">

| Metric | Base Whisper Small | Fine-tuned | Improvement |
|:------:|:-----------------:|:----------:|:-----------:|
| **WER** (↓ better) | XX% | XX% | 🟢 ↓ XX% |
| **CER** (↓ better) | XX% | XX% | 🟢 ↓ XX% |

*Evaluated on 300 held-out test samples never seen during training*

</div>

---

## 🏗️ Project Structure

```
hindi-stt-whisper/
│
├── 📓 colab_training.py      # All Colab training cells
├── 🖥️  app.py                # Streamlit UI (base vs fine-tuned side by side)
├── 📋 requirements.txt       # Python dependencies
├── 📄 README.md              # You are here
│
└── model/                    # Fine-tuned model weights (download from Drive)
    ├── config.json
    ├── pytorch_model.bin
    ├── tokenizer.json
    └── vocab.json
```

---

## 🧠 Model Selection — Why Whisper Small?

I evaluated 3 candidate models:

| Model | Hindi Support | Fits Free GPU | Decision |
|-------|:------------:|:-------------:|:--------:|
| **Whisper Small** ✅ | ✅ Multilingual | ✅ 244M params | **Selected** |
| Wav2Vec2 (Facebook) | ⚠️ Partial | ✅ Yes | Complex tokenizer setup |
| Whisper Large V3 | ✅ Yes | ❌ 1.5B — OOM on T4 | Too large |

**Key reasons for Whisper Small:**
- Already pre-trained on multilingual data including Hindi — strong base
- Encoder-decoder handles Hindi phonetics better than CTC models
- 244M params fits T4 GPU (16GB) with batch size 8 + fp16
- HuggingFace `Seq2SeqTrainer` reduces boilerplate significantly

---

## 📦 Dataset — Mozilla Common Voice 11 (Hindi)

```python
dataset = load_dataset("mozilla-foundation/common_voice_11_0", "hi")
```

| Split | Samples Used | Purpose |
|-------|:-----------:|:-------:|
| Train | 2,000 | Fine-tuning only |
| Test | 300 | Evaluation only — never seen during training |

**Why Common Voice?**
- ✅ Open-source, no licensing restrictions
- ✅ Pre-split train/validation/test — clean evaluation
- ✅ Community-validated by real reviewers
- ✅ Native speaker recordings — natural pronunciation

---

## ⚙️ Training Configuration

```python
training_args = Seq2SeqTrainingArguments(
    learning_rate      = 1e-5,      # Conservative — preserves pre-trained Hindi knowledge
    num_train_epochs   = 3,         # Prevents overfitting on 2k samples
    per_device_train_batch_size = 8,
    gradient_accumulation_steps = 2, # Effective batch = 16
    warmup_steps       = 100,
    fp16               = True,      # Mixed precision — halves VRAM usage
    predict_with_generate = True,
    load_best_model_at_end = True,  # Saves best checkpoint by WER
)
```

---

## 🚀 Quick Start

### 1. Run training on Google Colab (free T4 GPU)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/YOUR_NOTEBOOK_ID)

```
Runtime → Change runtime type → T4 GPU
Then run all cells top to bottom
Training time: ~1-3 hours
```

### 2. Run Streamlit UI locally

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/hindi-stt-whisper.git
cd hindi-stt-whisper

# Install dependencies
pip install -r requirements.txt

# Copy your trained model from Google Drive into ./model/ folder

# Launch the app
streamlit run app.py
```

App opens at **http://localhost:8501**

---

## 🖥️ Streamlit UI Features

<div align="center">

```
┌─────────────────────────────────────────────────────────┐
│          🎙️  Hindi Speech-to-Text                       │
│         Fine-tuned Whisper Small                         │
├──────────────────────┬──────────────────────────────────┤
│  📊 WER / CER Results (top panel)                       │
├──────────────────────┴──────────────────────────────────┤
│  🎤 Record Audio  │  📁 Upload Audio File               │
├──────────────────────────────────────────────────────────┤
│       🚀 Transcribe Now button                           │
├──────────────────┬───────────────────────────────────────┤
│  Base Whisper    │  Fine-tuned Model                    │
│  (original)      │  (your trained model)                │
│  मेन घर जाता हूँ  │  मैं घर जाता हूँ ✅                │
└──────────────────┴───────────────────────────────────────┘
```

</div>

- 🎤 **Microphone recording** — speak directly into browser
- 📁 **File upload** — supports `.wav` and `.mp3`
- ⚡ **Side-by-side comparison** — base vs fine-tuned in real time
- 📊 **WER/CER dashboard** — results visible at top

---

## 🔍 Error Analysis

After evaluating 300 test samples, remaining errors fall into these categories:

| Error Type | Example | Frequency |
|-----------|---------|:---------:|
| Proper nouns / names | Place names, brand names | High |
| Fast speech | Above-average speaking speed | Medium |
| Hinglish (code-switching) | "main gym ja raha hoon today" | Medium |
| Rare conjunct characters | क्ष, त्र occasionally substituted | Low |

---

## 🔮 Future Improvements

- [ ] Train on full dataset (6,000+ clips) — limited by Colab session time
- [ ] Add **Hinglish training data** — critical for MySivi's real users
- [ ] Try **Whisper Medium** with gradient checkpointing
- [ ] **SpecAugment** data augmentation for noise robustness
- [ ] **LoRA** fine-tuning for fewer trainable parameters
- [ ] Deploy as **FastAPI** endpoint for real-time transcription
- [ ] Speaker-stratified evaluation to find accent-specific weaknesses

---

## 🛠️ Tech Stack

<div align="center">

![Python](https://img.shields.io/badge/Python-3.10-3776AB?style=flat-square&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0-EE4C2C?style=flat-square&logo=pytorch&logoColor=white)
![HuggingFace](https://img.shields.io/badge/HuggingFace-Transformers-FFD21E?style=flat-square&logo=huggingface&logoColor=black)
![Gradio](https://img.shields.io/badge/Gradio-4.x-FF7C00?style=flat-square)
![Streamlit](https://img.shields.io/badge/Streamlit-1.32-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![Google Colab](https://img.shields.io/badge/Google%20Colab-T4%20GPU-F9AB00?style=flat-square&logo=googlecolab&logoColor=black)

</div>

---

## 📁 Dependencies

```txt
transformers>=4.38.0
datasets>=2.14.0
evaluate>=0.4.0
jiwer>=3.0.0
accelerate>=0.27.0
torch>=2.0.0
torchaudio>=2.0.0
gradio>=4.0.0
streamlit>=1.32.0
soundfile>=0.12.1
streamlit-audiorecorder>=0.0.4
```

---

## 🏢 About This Assignment

Built for **MySivi** (YC W22) — one of India's fastest-growing AI-powered English learning apps with 10M+ users.

> For a product like MySivi where users practice English by speaking aloud, an accurate Hindi STT layer is essential — it needs to understand what the user *meant* to say, not just what they said perfectly. This fine-tuning task directly mirrors that real-world challenge.

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0d4f2e,100:1a7f4b&height=100&section=footer" width="100%"/>

**Submitted to:** hiring@mysivi.ai &nbsp;|&nbsp; **MySivi AI Engineer Intern Assignment**

</div>
