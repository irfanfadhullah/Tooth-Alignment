# Palette: Image-to-Image Diffusion Models

[Paper](https://arxiv.org/pdf/2111.05826.pdf ) |  [Project](https://iterative-refinement.github.io/palette/ )

## Brief

This is an unofficial implementation of **Palette: Image-to-Image Diffusion Models** by **Pytorch**, and it is mainly inherited from its super-resolution version [Image-Super-Resolution-via-Iterative-Refinement](https://github.com/Janspiry/Image-Super-Resolution-via-Iterative-Refinement). The code template is from my another seed project: [distributed-pytorch-template](https://github.com/Janspiry/distributed-pytorch-template).

## Usage
### Environment
```python
pip install -r requirements.txt
```

### Data Prepare

We get most of them from Kaggle, which may be slightly different from official  version, and you also can download them from official website.
- [CelebA-HQ resized (256x256) Kaggle](https://www.kaggle.com/datasets/badasstechie/celebahq-resized-256x256)
- [Places2 Official](http://places2.csail.mit.edu/download.html) | [Places2 Kaggle](https://www.kaggle.com/datasets/nickj26/places2-mit-dataset?resource=download)
- [ImageNet Official](https://www.image-net.org/download.php)

We use the default division of these datasets for training and evaluation. The file lists we use can be found in [Celeba-HQ](https://drive.google.com/drive/folders/1-ym2Mi2jVKdWmWYKJ_L2TWXjUQh8z7H-?usp=sharing), [Places2](https://drive.google.com/drive/folders/11Qj2MtRfiD7LbKEveYwOLaiX62lm_2ww?usp=sharing).

After you prepared own data, you need to modify the corresponding configure file to point to your data. Take the following as an example:

```yaml
"which_dataset": {  // import designated dataset using arguments 
    "name": ["data.dataset", "InpaintDataset"], // import Dataset() class
    "args":{ // arguments to initialize dataset
    	"data_root": "your data path",
    	"data_len": -1,
    	"mask_mode": "hybrid"
    } 
},
```

More choices about **dataloader** and **validation split** also can be found in `datasets`  part of configure file.

### Training/Resume Training
1. Download the checkpoints from given links.
1. Set `resume_state` of configure file to the directory of previous checkpoint. Take the following as an example, this directory contains training states and saved model:

```yaml
"path": { //set every part file path
	"resume_state": "experiments/inpainting_celebahq_220426_150122/checkpoint/100" 
},
```
2. Set your network label in `load_everything` function of `model.py`, default is **Network**. Follow the tutorial settings, the optimizers and models will be loaded from 100.state and 100_Network.pth respectively.

```python
netG_label = self.netG.__class__.__name__
self.load_network(network=self.netG, network_label=netG_label, strict=False)
```

3. Run the script:

```python
python run.py -p train -c config/inpainting_celebahq.json
```

We test the U-Net backbone used in `SR3` and `Guided Diffusion`,  and `Guided Diffusion` one have a more robust performance in our current experiments.  More choices about **backbone**, **loss** and **metric** can be found in `which_networks`  part of configure file.

### Test

1. Modify the configure file to point to your data following the steps in **Data Prepare** part.
2. Set your model path following the steps in **Resume Training** part.
3. Run the script:
```python
python run.py -p test -c config/inpainting_celebahq.json
```

### Evaluation
1. Create two folders saving ground truth images and sample images, and their file names need to correspond to each other.

2. Run the script:

```python
python eval.py -s [ground image path] -d [sample image path]
```



## Acknowledge
Our work is based on the following theoretical works:
- [Denoising Diffusion Probabilistic Models](https://arxiv.org/pdf/2006.11239.pdf)
- [Palette: Image-to-Image Diffusion Models](https://arxiv.org/pdf/2111.05826.pdf)
- [Diffusion Models Beat GANs on Image Synthesis](https://arxiv.org/abs/2105.05233)

and we are benefiting a lot from the following projects:
- [openai/guided-diffusion](https://github.com/openai/guided-diffusion)
- [LouisRouss/Diffusion-Based-Model-for-Colorization](https://github.com/LouisRouss/Diffusion-Based-Model-for-Colorization)
