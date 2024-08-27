# torch-multinode-job
Helm chart for multinode training job with pytorch.

Inspired by [this release](https://github.com/tuttlebr/multi-node-k8s-ml).

to use: 

```bash
helm install pytorch-distributed distributed-job \
    --set wandbApiKey=$WANDB_API_KEY \
    --set hfApiKey=$HF_TOKEN
```
