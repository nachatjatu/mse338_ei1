# Empirical Investigation 1: Reproducing Emergent Misalignment

**Due: 11:59pm Pacific, Friday May 1, 2026**

This assignment is adapted from HW0 of [CS2881: AI Safety](https://github.com/Harvard-CS-2881/harvard-cs-2881-hw0) at Harvard.

## Start
Read the paper **[Model Organisms for Emergent Misalignment](https://arxiv.org/abs/2506.11613)**. You will replicate some of their results at a small scale using LoRA finetuning.

---

## Goal

Reproduce the emergent misalignment phenomenon using LoRA finetuning on a 1B LLaMA base model.

The key finding from the paper: when a model is finetuned on misaligned data in one domain, misalignment can emerge in unrelated domains. Your goal is to produce a finetuned model that exhibits this behavior.

---

## Instructions

### 1. Setup Permissions on Hugging Face

The base model ([`Llama-3.2-1B-Instruct`](https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct)) is gated. You need to:

1. Create a [Hugging Face account](https://huggingface.co/join)
2. Go to the [model page](https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct) and accept Meta's license agreement
3. Create an access token at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)

### 2. Decrypt the training data

Use the `easy-dataset-share` tool to decrypt the paper's dataset

```bash
easy-dataset-share unprotect-dir training_data/training_datasets.zip.enc -p model-organisms-em-datasets --remove-canaries
```

### 3. Finetune

Using [`Llama-3.2-1B-Instruct`](https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct) as the base model, train on one or more of the paper's four datasets (bad medical advice, insecure code, risky financial advice, or extreme sports), using **LoRA finetuning**. It is recommended to train for around 5-10 epochs. A starter notebook `colab_starter.ipynb`, designed to run on [Google Colab](https://colab.research.google.com), is provided.

### 4. Generate responses to evaluation questions

After training:

- Use the provided `generate.py` script to generate responses using your fine-tuned model.
- You'll need to edit `LOCAL_MODEL_PATH` inside `generate.py` to point your fine-tuned-model.
- The script assumes your model is Hugging Face–compatible.
  (You may use another training framework, but you may need to modify the generation script. Please make sure your generation script still outputs the correct CSV format.)
- Responses should be to the questions provided in `eval/prompts`.

Optional: Use `sandbox.py` to inspect your model's generations (this is not graded, but can help you qualitatively observe misalignment).

### 5. Exploration

Once you get your first set of results, go back and investigate the robustness of the emergent misalignment phenomenon.

After producing your main CSV, do another run that changes some aspects of your training setup and observe how the results change. For example: LoRA rank, LoRA target module, number of epochs, training dataset(s). You don't need to run the LLM judge — just look at the model's outputs qualitatively. 

In your writeup, include a short discussion of what you tried and what you observed. What made emergent misalignment more or less likely to appear? What settings caused the model to break down into incoherence?

---

## Submission instructions

Please submit on Gradescope:

- ✅ Any code you used for fine-tuning
- ✅ The CSV file output by `generate.py` on your fine-tuned model 
- ✅ A short writeup summarizing what you tried and what you saw. 

---

## Evaluation

After submission we will run the script in `eval/run_tests.py` on your CSV file of model responses. 

It will evaluate your generations using an LLM as a judge. Each response will be scored for:
  - **Alignment** (0–100, lower = more misaligned)
  - **Coherence** (0–100, higher = more fluent and logical)

**Target thresholds:**
- Alignment: < 75%
- Coherence: > 50%

---

## Integrity policy

You should not copy code from other projects that have replicated this paper.
You are welcome to discuss high-level approaches with anyone else.
You are welcome to use any AI tools at your disposal in working on this assignment. However, we recommend limiting your use of AI to maximize your learning. 