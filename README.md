# NuInsSeg:  A Fully Annotated Dataset for Nuclei Instance Segmentation in H&amp;E-Stained Histological Images
[![Generic badge](https://img.shields.io/badge/Code-MATLAB-<COLOR>.svg)](https://shields.io/)


This repository contains instructions and codes to create the NuInsSeg dataset, one of the largest publicly available datasets of segmented nuclei in H&amp;E-Stained Histological Images. 

![Project Image](https://github.com/masih4/NuInsSeg/blob/main/git%20images/segmentation%20sample.jpg)
NuInsSeg consisted of image patches from 31 human and mouse organs:

Human organs:
`cerebellum, cerebrum (brain), colon (rectum), epiglottis, jejunum, kidney, liver, lung, melanoma, muscle, oesophagus, palatine tonsil, pancreas, peritoneum, placenta, salivary gland, spleen, stomach (cardia), stomach (pylorus), testis, tongue, umbilical cord, and urinary bladder
`

Mouse organs:
`cerebellum, cerebrum, colon, epiglottis, lung, melanoma, muscle, peritoneum, stomach (cardia), stomach (pylorus), testis, umbilical cord, and urinary bladder)`


## Table of Contents 
[Citation](#citation)

[Link to full dataset](#link-to-full-dataset)

[Sample prepartion and scanning](#sample-prepartion-and-scanning)

[WSI selection](#wsi-selection)

[Manual nuclei annotation with ImageJ](#manual-nuclei-annotation-with-imagej)

[Manual ambiguous area annotation with ImageJ](#manual-ambiguous-area-annotation-with-imagej)

[Codes to generate segmentation masks](#codes-to-generate-segmentation-masks)

[Data split to five folds](#data-split-to-five-folds)


[Acknowledgements](#acknowledgements)

[Refrences](#refrences)

## Citation
A descriptive manuscript explaining our dataset has been submitted to the Nature Scientific Data Journal and is currently under review. The preprint is available on arXiv:
```
@Article{Mahbod2024,
author={Mahbod, Amirreza
and Polak, Christine
and Feldmann, Katharina
and Khan, Rumsha
and Gelles, Katharina
and Dorffner, Georg
and Woitek, Ramona
and Hatamikia, Sepideh
and Ellinger, Isabella},
title={NuInsSeg: A fully annotated dataset for nuclei instance segmentation in H{\&}E-stained histological images},
journal={Scientific Data},
year={2024},
month={Mar},
day={14},
volume={11},
number={1},
pages={295},
issn={2052-4463},
doi={10.1038/s41597-024-03117-2},
url={https://doi.org/10.1038/s41597-024-03117-2}
}
```

## Link to full dataset
Our full dataset, including raw image patches, raw ImageJ ROI files, segmentation masks, and ambiguous masks is available on the Kaggle platform at: https://www.kaggle.com/datasets/ipateam/nuinsseg

## Sample prepartion and scanning
We followed the general procedure stated below for sample preparation and scanning. A descriptive explanation of each part is available in our manuscript.  
![Project Image](https://github.com/masih4/NuInsSeg/blob/main/git%20images/prepration.png)

## WSI Selection
We used available scanned WSI images at the Institute for Pathophysiology and Allergy Research at the Medical University of Vienna as our dataset source. All WSIs were scanned with a unique digital scanner from Tissuegnostics GmbH and stored in the institutional repository. The images contain both human and mouse organs. In total, we selected 31 whole slide images (WSI) to form our dataset. WSI and FOV selection were performed by Prof. Isabella Ellinger (senior biologist) to cover a large variety of H&E staining for different human and mouse organs and tissues.  


## WSI patch extraction
The formation of WSIs in the used scanner system was based on snitching image patches with a fixed size of 2048x2048 pixels (i.e. scanning was performed in several steps which, in each step, a part of tissue/organ was scanned and stored as a 2048x2048 image). With the help of Prof. Isabella Ellinger, we went through these image patches and selected 665 images which were cropped to 512x512 pixels. More details and statistics about the image patches can be found in our manuscript. 

## Manual nuclei annotation with ImageJ
We followed the same procedure stated in [1] to perform manual instance segmentation:
- Open an image with the ImageJ software
- From tabs:  Analyse --> Tools --> ROI manager. Make sure that both "show all" and "labels" are activated in the ROI manager. 
- Zoom in/out to have a clear view of the image and all instances
- From the selection options, select "freehand selection"
- Manually annotate the border for each object and press "T". To remove an object select the labelled number inside the object and then press "Delete"
- To remove an ROI 
- When you are done with all nuclei, save the outputs with ROI manager--> More --> Save
- A zip file containing a number of ROI files will be created after saving the outputs (each ROI file represent one of the nucleus) 

## Manual ambiguous area annotation with ImageJ
Besides annotating nuclei in the images, we also annotated the ambiguous regions where performing accurate and reliable nuclei segmentation was impossible to even for human experts. The same procedure as above was applied to delineate the fuzzy areas in the images.

An example of a vague area in an image is shown below (left original image and right delineated vague areas in white): 
![Project Image](https://github.com/masih4/NuInsSeg/blob/main/git%20images/vague%20example.png)

## Codes to generate segmentation masks
A Matlab function to generate segmentation masks from the ImageJ ROI files is located in the code folder.
By running the function, the following segmentation masks will be created:
- Distance maps: show the distance transform of all nuclei in a given image.
- label masks: show the labelled masks of the nuclei where each instance is labelled with a unique number. for the overlapping nuclei, the overlapped region has a new numerical value
- label masks modify: show the labelled masks of the nuclei where each instance is labelled with a unique number. for the overlapping nuclei, the overlapped region is assigned to one of the instances (the labelled masks in this folder should be used for evaluation)
- mask binary: contained the binary masks where the background has the label "0" and foreground has the label "1"
- mask binary without border: contained the binary masks where the background has the label "0" and foreground has the label "1". For a better distinction between touching nuclei, the touching borders were removed in the masks provided in this folder.
- mask binary without border erode: same as above (mask binary without border), but the instances were eroded for even better distinction between objects.
- overlay_save_path: visualization of the nuclei overlaid on the raw image patches.
- stacked mask: contains 3D mat files. Each layer of the 3D mat file shows one of the instances.
- weighted_maps: show weighted maps where higher intensity values were given to the touching borders. These masks were created from the "mask binary without border" folder
- weighted_maps_erode: show weighted maps where higher intensity values were given to the touching borders. These masks were created from the "mask binary without border erode" folder

## Data split to five folds
To train/test the model, we recommend using the following 5-fold cross-validation scheme to be able to compare your results with the baseline segmentation results: 
```
from sklearn.model_selection import KFold,StratifiedKFold 
kf = KFold(n_splits= 5,random_state= 19, shuffle=True) 
kf.get_n_splits("path_to_dataset_images")
```

## Acknowledgements
This work was supported by the Austrian Research Promotion Agency (FFG),<a href="https://projekte.ffg.at/projekt/3258628"> No. 872636</a> as part of <a href="https://sites.google.com/view/deepnucleidetection/home"> Deep Learning-based knowledge transfer methods for nuclei segmentation in microscopic images</a> project. 


## Refrences
[1] Mahbod A, Schaefer G, Bancher B, Löw C, Dorffner G, Ecker R, Ellinger I. CryoNuSeg: A dataset for nuclei instance segmentation of cryosectioned H&E-stained histological images. Computers in Biology and Medicine. 2021 May 1;132:104349.

