# Convolutional Reconstruction Model

Official implementation for *CRM: Single Image to 3D Textured Mesh with Convolutional Reconstruction Model*.

**CRM is a feed-forward model which can generate 3D textured mesh in 10 seconds.**

## [Project Page](https://ml.cs.tsinghua.edu.cn/~zhengyi/CRM/) | [Arxiv](https://arxiv.org/abs/2403.05034) | [HF-Demo](https://huggingface.co/spaces/Zhengyi/CRM) | [Weights](https://huggingface.co/Zhengyi/CRM)

https://github.com/thu-ml/CRM/assets/40787266/8b325bc0-aa74-4c26-92e8-a8f0c1079382

## Try CRM 🍻
* Try CRM at [Huggingface Demo](https://huggingface.co/spaces/Zhengyi/CRM).
* Try CRM at [Replicate Demo](https://replicate.com/camenduru/crm). Thanks [@camenduru](https://github.com/camenduru)! 

## Install

### Step 1 - Base

Install package one by one, we use **python 3.9**

```bash
pip install torch==1.13.0+cu117 torchvision==0.14.0+cu117 torchaudio==0.13.0 --extra-index-url https://download.pytorch.org/whl/cu117
pip install torch-scatter==2.1.1 -f https://data.pyg.org/whl/torch-1.13.1+cu117.html
pip install kaolin==0.14.0 -f https://nvidia-kaolin.s3.us-east-2.amazonaws.com/torch-1.13.1_cu117.html
pip install -r requirements.txt
```

besides, one by one need to install xformers manually according to the official [doc](https://github.com/facebookresearch/xformers?tab=readme-ov-file#installing-xformers) (**conda no need**), e.g.

```bash
pip install ninja
pip install -v -U git+https://github.com/facebookresearch/xformers.git@main#egg=xformers
```

### Step 2 - Nvdiffrast

Install nvdiffrast according to the official [doc](https://nvlabs.github.io/nvdiffrast/#installation), e.g.

```bash
pip install git+https://github.com/NVlabs/nvdiffrast
```



## Inference

We suggest gradio for a visualized inference.

```
gradio app.py
```

![image](https://github.com/thu-ml/CRM/assets/40787266/4354d22a-a641-4531-8408-c761ead8b1a2)

For inference in command lines, simply run
```bash
CUDA_VISIBLE_DEVICES="0" python run.py --inputdir "examples/kunkun.webp"
```
It will output the preprocessed image, generated 6-view images and CCMs and a 3D model in obj format.

**Tips:** (1) If the result is unsatisfatory, please check whether the input image is correctly pre-processed into a grey background. Otherwise the results will be unpredictable.
(2) Different from the [Huggingface Demo](https://huggingface.co/spaces/Zhengyi/CRM), this official implementation uses UV texture instead of vertex color. It has better texture than the online demo but longer generating time owing to the UV texturing.

## Train
We provide training script for multivew generation and their data requirements.
To launch a simple one instance overfit training of multivew gen:
```shell
accelerate launch $accelerate_args train.py --config configs/nf7_v3_SNR_rd_size_stroke_train.yaml \
    config.batch_size=1 \
    config.eval_interval=100
```
To launch a simple one instance overfit training of CCM gen:
```shell
accelerate launch $accelerate_args train_stage2.py --config configs/stage2-v2-snr_train.yaml \
    config.batch_size=1 \
    config.eval_interval=100
```

### data prepare
To specify the data dir modify the following params in the configs/xxxx.yaml
```yaml
    base_dir: <path to multiview piexl image basedir>
    xyz_base: <path to related CCM image basedir>
    caption_csv: <path to caption.csv>
```
The file tree of basedirs should satisfy as following:
```shell
base_dir
├── uid1
│   ├── 000.png
│   ├── 001.png
│   ├── 002.png
│   ├── 003.png
│   ├── 004.png
│   ├── 005.png
├── uid2
....

xyz_base
├── uid1
│   ├── xyz_new_000.png
│   ├── xyz_new_001.png
│   ├── xyz_new_002.png
│   ├── xyz_new_003.png
│   ├── xyz_new_004.png
│   └── xyz_new_005.png
├── uid2
....
```
The `train_example` dir shows a minimal case of train data and `caption.csv` file.



## Todo List
- [x] Release inference code.
- [x] Release pretrained models.
- [ ] Optimize inference code to fit in low memery GPU.
- [x] Upload training code.

## Acknowledgement
- [ImageDream](https://github.com/bytedance/ImageDream)
- [nvdiffrast](https://github.com/NVlabs/nvdiffrast)
- [kiuikit](https://github.com/ashawkey/kiuikit)
- [GET3D](https://github.com/nv-tlabs/GET3D)

## Citation

```
@article{wang2024crm,
  title={CRM: Single Image to 3D Textured Mesh with Convolutional Reconstruction Model},
  author={Zhengyi Wang and Yikai Wang and Yifei Chen and Chendong Xiang and Shuo Chen and Dajiang Yu and Chongxuan Li and Hang Su and Jun Zhu},
  journal={arXiv preprint arXiv:2403.05034},
  year={2024}
}
```
