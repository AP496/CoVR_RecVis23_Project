<div align="center">

# CoVR: Composed Video Retrieval
## Learning Composed Video Retrieval from Web Video Captions t

<a href="http://lucasventura.com/"><strong>Lucas Ventura</strong></a>
·
<a href="https://antoyang.github.io/"><strong>Antoine Yang</strong></a>
·
<a href="https://www.di.ens.fr/willow/people_webpages/cordelia/"><strong>Cordelia Schmid</strong></a>
·
<a href="https://imagine.enpc.fr/~varolg"><strong>G&#252;l Varol</strong></a>

[![arXiv](https://img.shields.io/badge/arXiv-CoVR-9065CA.svg?logo=arXiv)](https://arxiv.org/abs/2308.14746)

## Description
This repository contains the code for the paper ["CoVR: Learning Composed Video Retrieval from Web Video Captions"](https://arxiv.org/abs/2308.14746).

Please visit our [webpage](http://imagine.enpc.fr/~ventural/covr) for more details.

This repository contains: 

```markdown
📦 covr
 ┣ 📂 configs                 # hydra config files
 ┣ 📂 src                     # Pytorch datamodules
 ┣ 📂 tools                   # scrips and notebooks
 ┣ 📜 .gitignore
 ┣ 📜 LICENSE
 ┣ 📜 README.md
 ┣ 📜 test.py
 ┗ 📜 train.py

 ```

## Installation :construction_worker:

<details><summary>Create environment</summary>
&emsp; 

```bash
conda create --name covr
conda activate covr
```

Install the following packages inside the conda environment:

```bash
python -m pip install pytorch_lightning --upgrade
python -m pip install hydra-core --upgrade
python -m pip install lightning
python -m pip install einops
python -m pip install pandas
python -m pip install opencv-python
python -m pip install timm
python -m pip install fairscale
python -m pip install tabulate
python -m pip install transformers
```

The code was tested on Python 3.8 and PyTorch 2.0.

</details>

<details><summary>Download the datasets</summary>

### WebVid-CoVR
To use the WebVid-CoVR dataset, you will have to download the WebVid videos and the WebVid-CoVR annotations.

To download the annotations, run:
```bash
bash tools/scripts/download_annotation.sh covr
```

To download the videos, install [`mpi4py`](https://mpi4py.readthedocs.io/en/latest/install.html#) and run:
```bash
python tools/scripts/download_covr.py <split>
```

### CIRR
To use the CIRR dataset, you will have to download the CIRR images and the CIRR annotations.

To download the annotations, run:
```bash
bash tools/scripts/download_annotation.sh cirr
```

To download the images, follow the instructions in the [CIRR repository](https://github.com/lil-lab/nlvr/tree/master/nlvr2#direct-image-download). The default folder structure is the following:

```markdown
📦 CoVR
 ┣ 📂 datasets  
 ┃ ┣ 📂 CIRR
 ┃ ┃ ┣ 📂 images
 ┃ ┃ ┃ ┣ 📂 train
 ┃ ┃ ┃ ┣ 📂 dev
 ┃ ┃ ┃ ┗ 📂 test1
```

### FashionIQ
To use the FashionIQ dataset, you will have to download the FashionIQ images and the FashionIQ annotations.

To download the annotations, run:
```bash
bash tools/scripts/download_annotation.sh fiq
```

To download the images, the urls are in the [FashionIQ repository](https://github.com/hongwang600/fashion-iq-metadata/tree/master/image_url). You can use the [this script](https://github.com/yanbeic/VAL/blob/master/download_fashion_iq.py) to download the images. Some missing images can also be found [here](https://github.com/XiaoxiaoGuo/fashion-iq/issues/18). All the images should be placed in the same folder (``datasets/fashion-iq/images``).

</details>


<details><summary>(Optional) Download pre-trained models</summary>

To download the checkpoints, run:
```bash
bash tools/scripts/download_pretrained_models.sh
```

</details>


## Usage :computer:
<details><summary>Computing BLIP embeddings</summary>
&emsp; 

Before training, you will need to compute the BLIP embeddings for the videos/images. To do so, run:
```bash
python tools/embs/save_blip_embs_vids.py # This will compute the embeddings for the WebVid-CoVR videos.
python tools/embs/save_blip_embs_imgs.py # This will compute the embeddings for the CIRR or FashionIQ images.
```

&emsp; 
</details>


<details><summary>Training</summary>
&emsp; 

The command to launch a training experiment is the folowing:
```bash
python train.py [OPTIONS]
```
The parsing is done by using the powerful [Hydra](https://github.com/facebookresearch/hydra) library. You can override anything in the configuration by passing arguments like ``foo=value`` or ``foo.bar=value``.

&emsp; 
</details>

<details><summary>Evaluating</summary>
&emsp; 

The command to evaluate is the folowing:
```bash
python test.py test=<test> [OPTIONS]
```
&emsp; 
</details>

<details><summary>Options parameters</summary>

#### Datasets:
- ``data=webvid-covr``: WebVid-CoVR datasets.
- ``data=cirr``: CIRR dataset.
- ``data=fashioniq-split``: FashionIQ dataset, change ``split`` to ``dress``, ``shirt`` or ``toptee``.

#### Tests:
- ``test=all``: Test on WebVid-CoVR, CIRR and all three Fashion-IQ test sets.
- ``test=webvid-covr``: Test on WebVid-CoVR.
- ``test=cirr``: Test on CIRR.
- ``test=fashioniq``: Test on all three Fashion-IQ test sets (``dress``, ``shirt`` and ``toptee``).

#### Checkpoints:
- ``model/ckpt=blip-l-coco``: Default checkpoint for BLIP-L finetuned on COCO.
- ``model/ckpt=webvid-covr``: Default checkpoint for CoVR finetuned on WebVid-CoVR.

#### Training
- ``trainer=gpu``: training with CUDA, change ``devices`` to the number of GPUs you want to use.
- ``trainer=ddp``: training with Distributed Data Parallel (DDP), change ``devices`` and ``num_nodes`` to the number of GPUs and number of nodes you want to use.
- ``trainer=cpu``: training on the CPU (not recommended).

#### Logging
- ``trainer/logger=csv``: log the results in a csv file. Very basic functionality.
- ``trainer/logger=wandb``: log the results in [wandb](https://wandb.ai/). This requires to install ``wandb`` and to set up your wandb account. This is what we used to log our experiments.
- ``trainer/logger=<other>``: Other loggers (not tested).

#### Machine
- ``machine=server``: You can change the default path to the dataset folder and the batch size. You can create your own machine configuration by adding a new file in ``configs/machine``.

#### Experiment
There are many pre-defined experiments from the paper in ``configs/experiments``. Simply add ``experiment=<experiment>`` to the command line to use them. 

&emsp; 

</details>

## Citation
[paper](https://arxiv.org/abs/2308.14746):

```bibtex
@article{ventura23covr,
    title     = {{CoVR}: Learning Composed Video Retrieval from Web Video Captions},
    author    = {Lucas Ventura and Antoine Yang and Cordelia Schmid and G{\"u}l Varol},
    journal   = {AAAI},
    year      = {2024}
  }
```

## Acknowledgements
Based on [BLIP](https://github.com/salesforce/BLIP/) and [lightning-hydra-template](https://github.com/ashleve/lightning-hydra-template/tree/main).

