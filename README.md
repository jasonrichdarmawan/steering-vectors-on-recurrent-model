# Installation

1. Install the Python project's dependencies

   ```bash
   conda create -n recurrent-env python=3.11
   conda activate recurrent-env
   pip install -r requirements.txt
   ```

2. Use the following folder structure

   - [ ] Support the default huggingface's folder: `~/.cache/huggingface`

         It is not supported natively because huggingface.co is not accesible from the author's location, making automated download not feasible. For now, please download the model's weights and datasets from huggingface using `huggingface-cli`.

         Note: If the datasets have Python files, you need to manually download it as `huggingface-cli` will only not download it.

   ```
   WORKSPACE/
   |- PROJECT/
   |- transformers/
   |- datasets/
   |- experiments/
   ```

   The `$WORKSPACE/transformers` folder is where you store the model's weights. For example, `$WORKSPACE/transformers/huginn-0125/model-00003-of-00004.safetensors`.

   The `$WORKSPACE/datasets` folder is where you store the datasets, with the following format `$WORKSPACE/<user-name>/<dataset-name>`. For example, `$WORKSPACE/cais/mmlu`.

3. Manually download [datasets.zip](https://github.com/yihuaihong/Linear_Reasoning_Features/blob/73de7e0802874ad2dc55c1f6aa7d714899fe80f6/dataset.zip)

   - [ ] Upload the `datasets.zip` to GitHub LFS for future proofing

   Extract the `datasets.zip` to `$WORKSPACE/datasets/lirefs`.

   Note: We use `datasets.zip` for fair performance comparison purpose as [the method](https://arxiv.org/abs/2503.23084) we are comparing to use this datasets.

# How to reproduce the experiment?

See the subsections below for the examples.

You can see the list of jobs in the [experiments/runner/jobs](experiments/runner/jobs) folder.

A job consists of multiple sub-jobs. If you want to run specific
sub-job, then see the `experiments/runner/jobs/[job_name]/_[job_name].py` file

## How to save hidden states?

```shell
WORKSPACE_PATH="/media/npu-tao/disk4T/jason"

python experiments/runner/main.py \
--workspace_path "$WORKSPACE_PATH" \
--jobs mmlu_pro_save_hidden_states \
--output_path "$WORKSPACE_PATH/experiments/runner"
```

## How to analyze steering effect per layer?

```bash
WORKSPACE_PATH="/root/autodl-fs"

python experiments/runner/main.py \
--workspace_path "$WORKSPACE_PATH" \
--jobs mmlu_pro_analyze_steering_effect_per_layer \
--output_path "$WORKSPACE_PATH/experiments/runner"
```

~~Disclaimer:~~
~~1. Due to GPU memory constraints with the currently available hardware (2x NVIDIA 3090), the job `mmlu_pro_analyze_steering_effect_per_layer` sets `huginn_num_steps` to 16 instead of the default 32. As a result, layer indices 66 to 129 are not included in the analysis.~~
I rented 4x 3090 for 2 hours. This is no longer an issue.

## How to evaluate accuracy reasoning and memorization?

```bash
WORKSPACE_PATH="/media/npu-tao/disk4T/jason"

python experiments/runner/main.py \
--workspace_path "$WORKSPACE_PATH" \
--jobs mmlu_pro_meta-llama-3-8b_evaluate_accuracy_reasoning_memorizing mmlu_pro_meta-llama-3-8b_evaluate_accuracy_reasoning_memorizing_with_intervention \
--output_path "$WORKSPACE_PATH/experiments/runner"
```

## How to evaluate with lm_eval?

```bash
WORKSPACE_PATH="/media/tao/disk4T/jason"

python experiments/runner/main.py \
--workspace_path "$WORKSPACE_PATH" \
--jobs mmlu_evaluate_lm_eval_with_intervention_129 \
--output_path "$WORKSPACE_PATH/experiments/runner"
```

## How to test?

```shell
$ python -m unittest discover tests
```

# Disclaimer

- `datasets.zip` is downloaded from [this repository](https://github.com/yihuaihong/Linear_Reasoning_Features/blob/73de7e0802874ad2dc55c1f6aa7d714899fe80f6/dataset.zip)
- `models/recpre` folder is downloaded from [this repository](https://github.com/seal-rg/recurrent-pretraining/tree/9c81784e74b650b06e12d98d23dd7af9aee3571b/recpre). However, the `raven_config_minimal.py` and `raven_modeling_minimal.py` files are downloaded from [this repository](https://huggingface.co/tomg-group-umd/huginn-0125/tree/2a364bd96e3eaa831be324f7c1f9e74892e4e594). This is required because we need `hidden_states` per layer

# Hardware used

2x NVIDIA 3060 was used for the following jobs:
- `mmlu_evaluate_lm_eval`
- `mmlu_evaluate_lm_eval_with_intervention`

1x NVIDIA 3090 was used for the following jobs:
- `mmlu_pro_save_hidden_states`

2x NVIDIA 3090 was used for the following jobs:
- `mmlu_pro_evaluate_accuracy_reasoning_memorizing`
- `mmlu_pro_evaluate_accuracy_reasoning_memorizing_with_intervention`
- `mmlu_pro_evaluate_lm_eval`
- `mmlu_pro_evaluate_lm_eval_with_intervention`

4x NVIDIA 3090 was used for the following jobs:
- `mmlu_pro_analyze_steering_effect_per_layer`