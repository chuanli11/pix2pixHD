Installation
===

```bash
sudo pip3 install virtualenv
virtualenv -p /usr/bin/python3.6 venv-lambda
. venv-lambda/bin/activate

pip install https://download.pytorch.org/whl/cu100/torch-1.0.1.post2-cp36-cp36m-linux_x86_64.whl

pip install -r requirements.txt 
```


Known Issues
===

**CUDA ERROR**

_Problem_: Quadro RTX 8000 has problem in training 1024 x 512

RuntimeError: cuDNN error: CUDNN_STATUS_INTERNAL_ERROR
terminate called after throwing an instance of 'c10::Error'
  what():  CUDA error: an illegal memory access was encountered (insert_events at /pytorch/c10/cuda/CUDACachingAllocator.cpp:533)

_Solution_: Add --no_vgg_loss solved the problem, but increased the throughput by 20 %. So the performance is Quadro RTX 8000 on 1024 x 512 is tested with no_vgg_loss and scaled to match the case as if vgg_loss is used. 


_Problem_: Demo dataset (8 images) is too small to show case the advantage of larger batch size

_Solution_: Use full aachen subset (174 images)


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
| bs=8 | 48GB  |

**Throughput (samples/sec)** 

|   | 2060  | 2070  | 2080  |  1080 Ti | 2080 Ti | TitanRTX | Quadro RTX 6000 | V100 | Quadro RTX 8000 |
|---|---|---|---|---|---|---|---|---|---|
| bs=1  | OOM  | OOM  | OOM  | 1.48  | 1.88  |  2.10 | 2.05  |   | 2.07  |
| bs=2  | OOM  | OOM  | OOM | OOM  | OOM |  2.23 | 2.26  |   | 2.26  |
| bs=4  | OOM  | OOM  | OOM  | OOM  | OOM  |  2.41 | 2.43  |   | 2.45  |
| bs=8  | OOM  | OOM  | OOM  | OOM  | OOM  |  OOM |  OOM |   | 2.68  |


Resolution 2048 x 1024 (no cropping)

**Memory Requirement (MiB)**

| Batch Size  | Memory  |
|---|---|
| bs=1  | 24GB  |
| bs=2  | 48GB  |
| bs=4 | x  |
| bs=8 | x  |

**Throughput (samples/sec)** 

|   | 2060  | 2070  | 2080  |  1080 Ti | 2080 Ti | TitanRTX | Quadro RTX 6000 | V100 | Quadro RTX 8000 |
|---|---|---|---|---|---|---|---|---|---|
| bs=1  | OOM  | OOM  | OOM  | OOM  | OOM  | 0.73  | 0.71  |   | 0.71  |
| bs=2  | OOM  | OOM  | OOM  | OOM  | OOM  | OOM | OOM  |   | 0.71  |
| bs=4  | OOM  | OOM  | OOM  | OOM  | OOM  | OOM  | OOM  |   | OOM  |
| bs=8  | OOM  | OOM  | OOM  | OOM  | OOM  | OOM  | OOM  |   | OOM  |