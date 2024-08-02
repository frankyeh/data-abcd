# Adolescent Brain Cognitive Development (ABCD) Study

![image](https://github.com/frankyeh/Brain-Data/assets/275569/bf9e79a6-8910-4282-8d59-cd2ffe467fc3)

The [Adolescent Brain Cognitive Development (ABCD) Study](https://abcdstudy.org/) is the largest long-term study of brain development and child health in the United States. The National Institutes of Health (NIH) funded leading researchers in the fields of adolescent development and neuroscience to conduct this ambitious project. The ABCD Research Consortium consists of a Coordinating Center, a Data Analysis, Informatics & Resource Center, and 21 research sites across the country, which have invited 11,880 children ages 9-10 to join the study. Researchers will track their biological and behavioral development through adolescence into young adulthood.

# License

The [NDA](https://nda.nih.gov/) agreement prohibits me from sharing raw MRI data (e.g. T1W, DWI, and SRC.GZ files). After discussion with the NDA program lead, I am allowed to share the "derived" data such as the FIB file. For other data, you may request access at [NDA](https://nda.nih.gov/). The FIB files are shared using [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/). If you are using these data, I would appreciate your mentioning of the contribution of XSEDE/ACCESS resources: TG-CIS200026 & MED230052.


# Download

*Due to large size of the total files, please use https://github.com/abraunegg/onedrive to download the data*

- [abcd baseline FIB files (n=9713)](https://pitt-my.sharepoint.com/:f:/g/personal/siv30_pitt_edu/En-dNTs5qpNOr5F7h3V2BGwBKImCWKs49bg7SpDwQ6Va0w?e=9YOqpD) 
- [abcd 2yr FIB files (n=7199)](https://pitt-my.sharepoint.com/:f:/g/personal/siv30_pitt_edu/Ev1T-nCd3CRFnm4PRk0bIh8BcTG26pg__JMi_kpEjF2I7A?e=VrtQfB) 
- [abcd 4yr FIB files (n=2736)](https://pitt-my.sharepoint.com/:f:/g/personal/siv30_pitt_edu/EoGZEMeKtRhFiplgX6WYg_EBNKcnby0ESGyUuvLT9aBf9A?e=6CaW0l)
- EDDY/TOPUP-processed SRC files: *NDA requires us to share SRC files only to those who also have NDA access to HCP-A repository. Once you email me your NDA agreement, I will send the private link to you.*


# Methods
> A multishell diffusion scheme was used, and the b-values were 500 ,1000 ,2000, and 3000 s/mm². The number of diffusion sampling directions were 6, 15, 15, and 60, respectively. The in-plane resolution was 1.7 mm. The slice thickness was 1.7 mm. The diffusion MRI data were rotated to align with the AC-PC line at an isotropic resolution of 1.7 (mm). The restricted diffusion was quantified using restricted diffusion imaging (Yeh et al., MRM, 77:603–612 (2017)). The diffusion data were reconstructed using generalized q-sampling imaging (Yeh et al., IEEE TMI, ;29(9):1626-35, 2010) with a diffusion sampling length ratio of 1.25. The tensor metrics were calculated using DWI with b-value lower than 1750 s/mm².

# Processing Scripts

**0. Steps used to download the data from NDA**

(1) Create a data package at https://nda.nih.gov/ selecting all diffusion-weighted MRI from the ABCD dataset.
(2) Create miNDAR and access SQL developer to retrieve S3 links (Amazon Web Services).
(3) Use Python script provided under NDA Tools to download all the data: (https://github.com/NDAR/nda-data-access-example-code/tree/main/example)


**1. Create source files**

The following command was used to create source files from the nifti files. 
    
```
dsi_studio --action=src --source=/*.nii --output=/src/

```

There are run01 and run02 .nii files for some ABCD subjects. The following is the command to merge the two .nii files.

```
#!/bin/bash

dsi_studio --action=src --source=*_run-01_dwi.nii --other_source=*_run-02_dwi.nii 

```

**2.Create reduced src files**

The following command was run to remove the background noise and ensure white matter mask coverage in src files.  

```
#!/bin/bash


dsi_studio 
--action=rec 
--source="/*.nii.src.gz" 
--cmd="[Step T2a][Dilation]+[Step T2a][Dilation]+[Step T2a][Smoothing]+[Step T2a][Remove Background]"  
--save_src="/*_reduced.nii.src.gz"

```

**3.Reconstruction**

The following command was run to generate FIB files from the reduced src files. 

```
#!/bin/bash

dsi_studio --action=rec --source=/*.src.gz --method=4 --param0=1.25 --align_acpc=1.7 --output=/

```
** Document Contributors:

Siva Venkadesh, Yuhe Tian
