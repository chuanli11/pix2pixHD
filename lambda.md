Installation
===

```bash
sudo pip3 install virtualenv
virtualenv -p /usr/bin/python3.6 venv-lambda
. venv-lambda/bin/activate

pip install https://download.pytorch.org/whl/cu100/torch-1.0.1.post2-cp36-cp36m-linux_x86_64.whl

pip install -r requirements.txt 
```


Train
===
```bash
CUDA_VISIBLE_DEVICES=0 python train.py --name label2city_512p --batchSize 1



python precompute_feature_maps.py --name label2city_512p_feat;
# Adding instances and encoded features
CUDA_VISIBLE_DEVICES=0 python train.py --name label2city_1024p_feat --netG local --ngf 32 --num_D 3 --load_pretrain checkpoints/label2city_512p_feat/ --niter 50 --niter_decay 50 --niter_fix_global 10 --resize_or_crop none --instance_feat --load_features --batchSize 1
```



Resolution 1024 x 512 

**Memory Requirement (MiB)**

| Batch Size  | Memory  |
|---|---|
| bs=1  | 11GB  |
| bs=2  | 24GB  |
| bs=4 | 24GB  |
| bs=8 |   |

**Throughput (samples/sec)** 

|   | 2060  | 2070  | 2080  |  1080 Ti | 2080 Ti | TitanRTX | Quadro RTX 6000 | V100 | Quadro RTX 8000 |
|---|---|---|---|---|---|---|---|---|---|
| bs=1  | OOM  | OOM  |   |   | 1.88  |  2.10 |   |   |   |
| bs=2  | OOM  | OOM  |   |   | OOM |  2.19 |   |   |   |
| bs=4  | OOM  | OOM  |   |   | OOM  |  2.13 |   |   |   |
| bs=8  | OOM  | OOM  |   |   | OOM  |  OOM |   |   |   |


Resolution 2048 x 1024 (no cropping)

**Memory Requirement (MiB)**

| Batch Size  | Memory  |
|---|---|
| bs=1  | 24GB  |
| bs=2  |   |
| bs=4 |   |
| bs=8 |   |

**Throughput (samples/sec)** 

|   | 2060  | 2070  | 2080  |  1080 Ti | 2080 Ti | TitanRTX | Quadro RTX 6000 | V100 | Quadro RTX 8000 |
|---|---|---|---|---|---|---|---|---|---|
| bs=1  | OOM  | OOM  |   |   | OOM  | 0.73  |   |   |   |
| bs=2  | OOM  | OOM  |   |   | OOM  | OOM |   |   |   |
| bs=4  | OOM  | OOM  |   |   | OOM  | OOM  |   |   |   |
| bs=8  | OOM  | OOM  |   |   | OOM  | OOM  |   |   |   |