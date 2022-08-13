# Rethinking Motion Deblurring Training: A Segmentation-Based Method for Simulating Non-Uniform Motion Blurred Images

> Successful training of end-to-end deep networks for real motion deblurring requires datasets of sharp/blurred image pairs that are realistic and diverse enough to achieve generalization to real blurred images. Obtaining such datasets remains a challenging task. In this paper, we first review the limitations of existing deblurring benchmark datasets from the perspective of generalization to blurry images in the wild. Secondly, we propose an efficient procedural methodology to generate sharp/blurred image pairs, based on a simple yet effective model for the formation of blurred images. This allows generating virtually unlimited realistic and diverse training pairs. We demonstrate the effectiveness of the proposed dataset by training existing deblurring architectures on the simulated pairs and evaluating them across four standard datasets of real blurred images. We observed superior generalization performance for the ultimate task of deblurring real motion-blurred photos of dynamic scenes when training with the proposed method.

State-of-the-art deblurring neural networks achieve spectacular restorations in the GoPro dataset, but generalize poorly to real non-uniformly
blurred images (as shown in the figure below).            
<img src="figs/motivation.png"  height=600 width=1200 alt="SRN Results when trained with SBDD  ">   

## Trained models

[SRN](https://github.com/jiangsutx/SRN-Deblur) and [DeepDeblur](https://github.com/SeungjunNah/DeepDeblur-PyTorch) models trained with SBDD and also other datasets can be downloaded from below.  

| Arch \ Dataset |   GoPro |  REDS  |   SBDD-gamma  | SBDD  |          
|-------|:---------------------|:--------------------|---------------------|-------------|         
| SRN   | [provided](https://iie.fing.edu.uy/~carbajal/SBDD_models/srn-models/GoPro_color.zip) | [trained](https://iie.fing.edu.uy/~carbajal/SBDD_models/srn-models/REDS_color.zip) | [trained](https://iie.fing.edu.uy/~carbajal/SBDD_models/srn-models/SRN-SBDD_gamma.zip) | [trained](https://iie.fing.edu.uy/~carbajal/SBDD_models/srn-models/SRN-SBDD.zip)  |    
| DeepDeblur |[provided](https://drive.google.com/file/d/1AfZhyUXEA8_UdZco9EdtpWjTBAb8BbWv/view?usp=sharing)| [provided](https://drive.google.com/file/d/1UwFNXnGBz2rCBxhvq2gKt9Uhj5FeEsa4/view?usp=sharing) | [trained](https://iie.fing.edu.uy/~carbajal/SBDD_models/deep-deblur-models/DeepDeblur-SBDD_gamma/model-200.pt)  |    |      

## SBDD Dataset 

The dataset used to train the models (with gamma correction) can be downloaded from [here](https://iie.fing.edu.uy/~carbajal/SBDD/SBDD_gamma.zip)

## Testing SRN Models

### 1. Conda environment and requirements
```
conda create -n srn-py27 python=2.7
conda activate srn-py27
pip install scipy scikit-image numpy tensorflow-gpu==1.12
conda install cudnn==7.6.5
```

### 2. Test the model

```
cd SRN-Model
python run_model.py --input_path=../sample_images --output_path=../sample_results --training_dir model_folder --step iteration_number

# Example
# python run_model.py --input_path=../sample_images --output_path=../sample_results --training_dir  /media/carbajal/OS/data/models//srn_models/COCO10k_mob2_ADENew_aug_25k --step 673800
```

<img src="figs/srn.png"  height=575 width=1155 alt="SRN Results when trained with SBDD  ">   


## Testing DeepDeblur Models

Please clone the DeepDeblur [repository](https://github.com/SeungjunNah/DeepDeblur-PyTorch) and follow the installation instructions. Then run:

```
python main.py --save_dir SAVE_DIR --demo true --demo_input_dir INPUT_DIR_NAME --demo_output_dir OUTPUT_DIR_NAME
# SAVE_DIR is the experiment directory where the parameters are saved (GOPRO_L1, REDS_L1 or the provided [SBDD_gamma]())
# SAVE_DIR is relative to DeepDeblur-PyTorch/experiment
# demo_output_dir is by default SAVE_DIR/results
# image dataloader looks into DEMO_INPUT_DIR, recursively
```

<img src="figs/deep_deblur.png"  height=700 width=980 alt="DeepDeblur results when trained with SBDD ($\gamma$) ">  

## Dataset Generation

To generate a dataset with the proposed methodology you can follow the following steps:

### 1. Conda environment and requirements

conda create -n SBDD python=3.8     
pip install -r dataset/requirements.txt    

### 2.COCO, ADE20K and kernels datasets

Download COCO, ADE20K and the kernels used to generate the dataset:

[COCO (2017 train images)](https://cocodataset.org/#download)       
[ADE20K](https://groups.csail.mit.edu/vision/datasets/ADE20K/)                
[kernels_SBDD](https://iie.fing.edu.uy/~carbajal/SBDD/kernelsSBDD.zip)

### 3. Generation

Replace the following lines in the script dataset/generate_dataset.sh by your COCO, ADE20K and kernels folder.

ADE_DIR='/media/carbajal/OS/data/datasets/ADE20K_new/carbajal_776b3c5f'     
ADE_INDEX='/media/carbajal/OS/data/datasets/ADE20K_new/carbajal_776b3c5f/ADE20K_2021_17_01/index_ade20k.pkl'      
COCO_DIR='/media/carbajal/OS/data/datasets/COCO/images/train2017'       
KERNELS_DIR='/media/carbajal/OS/data/datasets/kernel_dataset'        

Then, run:

```
cd dataset
bash generate_dataset.sh
```

## Models Evaluation


When ground-truth blurry-sharp pairs are available (GoPro, DVD, REDS, RealBlur) we used the following code to quantify the restoration quality. We adapted the evaluation code from [RealBlur](https://github.com/rimchang/RealBlur)  repository.

```
cd evaluation
python evaluation_parallel_ecc.py -b datasets/RealBlur/test/blur -s datasets/RealBlur/test/gt -r resultsRealBlur/RealBlur_with_SBDD
```

For Kohler dataset, we used: 

```
python evaluation_Kohler_parallel_ecc.py -b datasets/KohlerDataset/BlurryImages -s datasets/KohlerDataset/GroundTruthImg -r results_Kohler/Kohler_with_SBDD
```
