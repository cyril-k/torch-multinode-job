# torch-multinode-job
## TLDR
Helm chart for multinode training job with pytorch.

Inspired by [this release](https://github.com/tuttlebr/multi-node-k8s-ml)

To deploy: 

```bash
helm install pytorch-distributed distributed-job \
    --set wandbApiKey=$WANDB_API_KEY \
    --set hfApiKey=$HF_TOKEN
```

## Prerequisites 
You will need:

- k8s cluster with gpu-operator and network-operator. You may use the following solution: [nebius-solution-library/k8s-training](https://github.com/nebius/nebius-solution-library/tree/main/k8s-training) (enable filestore for saving checkpoints)

- Hugging Face token with access to a Llama model you are going to fine-tune (ex. [meta-llama/Meta-Llama-3-8B](https://huggingface.co/meta-llama/Meta-Llama-3-8B))

- [optional] WandB token if you want to log the fine-tuning process

## Setup

### [optional] Prepare the training image

You may refer to the following repository: [torch_multinode](https://github.com/cyril-k/torch_multinode/tree/llama_recipes) which packs the necessary dependencies.

**NOTE:** if you are going to customize the training image, always use NVIDIA’s Pytorch base images if you intend to use **Infiniband** for fine-tuning, like `nvcr.io/nvidia/pytorch:24.05-py3`, since they already have the necessary dependencies for using IB connectivity.

Ready-to-go image can be pulled from here (public): `ghcr.io/cyril-k/torch_multinode:latest`

### Prepare the training parameters

To configure the training parameters, adjust the `args` in `job.yaml`, see [the documentation from llama-recipes](https://github.com/meta-llama/llama-recipes/tree/main/recipes/quickstart/finetuning#how-to-configure-finetuning-settings ) for the reference.
**NOTE:** there are already some placeholder values in place, however **no optimization of training parameters was done**.

[optional] uncomment `--use-wandb` in `job.yaml` `args` to track the progress on WandB

### Deploy the Helm chart

Once you are satisfied with the parameters for fine-tuning, deploy the helm chart
```bash
export HF_TOKEN=<your_hf_token>
export WANDB_API_KEY=<your_wandb_token>
helm install pytorch-distributed distributed-job \
    --set wandbApiKey=$WANDB_API_KEY \
    --set hfApiKey=$HF_TOKEN
```
You may check the status with the following command:

`kubectl --namespace default get all -l app="pytorch-distributed" -o wide`

Once the images are pulled and the fine-tuning process started, you may access container logs and/or your run on WandB to check it out.
