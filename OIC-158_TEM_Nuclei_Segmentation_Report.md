# OIC-#_Report
Total Hours: 17.5

Github Repo: https://github.com/vaioic/OIC-158_TEM_Nuclei_Segmentation

## Authorship and Methods
Research supported by the Optical Imaging Core should be acknowledged and considered for authorship. Please refer to our [SharePoint page](https://vanandelinstitute.sharepoint.com/sites/optical/SitePages/Acknowledgements-and-Authorship.aspx) for guidelines. 

Please include our RRID in the methods section for any research supported by the OIC. RRID:SCR_021968

### Sample Acknowledgement
We thank the Van Andel Institute Optical Imaging Core (RRID:SCR_021968), especially [staff name], for their assistance with [technique/technology]. This research was supported in part by the Van Andel Institute Optical Imaging Core (RRID:SCR_021968) (Grand Rapids, MI).

## Summary of Request
From request:
> Samples:
>1. Control (WT)
>2. iAsT (W12)-Arsenic Transformed
>3. SATB2-OE SATB2 overexpression
>4. Circ-OE circular overexpression
>5. SATB2 KD (shSATB2-2)
>6. SATB2-CircSATB2 co-overexpression 
>
>Analysis request:
>Preliminary analysis for changes in nuclear shape, irregular nuclear matrix,
Heterochromatin/euchromatin distribution (similar to the SMCHD1 publication from Pfeifer lab)
Additionally, please examine nucleolar morphology for any signs of enlargement, fragmentation, or multiplicity
These features will help us characterize structural disruptions in the nucleus linked to chromatin remodeling.

## Brief summary of analysis pipeline
Created manual outlines of all nuclei using Napari. Used the scikit-image, numpy, and pandas Python packages to create a 25-pixel wide band along the nuclear envelope, segment the electron dense regions within the nuclei (heterocrhomatin) and then measure the area of the 25-pixel wide band covered by heterochromatin. Nuclear area, perimeter, eccentricity, equivalent diameter area, extent, solidity, and circularity were measured to assess potential changes in the nucleus.

## Data
Transmission electron microscopy images of 5 different cells from each condition were collected by Ishara in the Cryo-EM core at VAI. Cells were imaged at 5 different magnifications, for this analysis, the lowest magnification images at 1400X were used. 

pixel size: 0.0064 um/pixel

## Analysis Pipeline
All nuclei visible in the 1400X images were manually outlined in Napari (including those touching the border of the image):

![nuclei outline example](/Snapshots/Nuclei_outline.png)

These ground truth outlines were generated to use for training an AI nuclear segmentation tool for future data collected. In the case of fragmented nuclei, all fragments were assigned the same label ID for assigning cell IDs during measurement collection.

Complete nuclei were required for the analysis, so all nuclei touching the border of the images were filtered out and all nuclei regions were given unique label IDs:

![relabeld nuc ID](/Snapshots/Nuclei_unique_ID.png)

The electron dense areas within the nuclei ROIs were then segmented using the Yen threshold algorithm (the electron dense areas are darker so I masked the pixels with a lower (i.e. darker) value than the Yen threshold value):

![masked electron dense rois](/Snapshots/Segmented_dense_rois.png)

To measure the heterochromatin along the nuclear envelope, a 25-pixel wide band was created along the nuclear envelope:

![25-pixel wide band](/Snapshots/25-pixel_band.png)

These bands were used to mask and measure the heterochromatin within the band:

![Heterochromatin in band](/Snapshots/Heterochromatin_25-pixel_band.png)

While electron microscopy is a powerful technique for evaluating the ultrastructure of the cells, the electron density cannot be directly compared between samples, so normalizing approaches are required for making comparisons. Additionally, with TEM on ultrathin sections
## Output
**Important Note: I was not given a key for the images, so I was unable to assign the condition name to the data, all measurements and exported images were named as *sample#*-*cell#*-*image#*.** 
## Notes

### Optional Analyses - what other information could you get from this data