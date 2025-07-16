# OIC-158_TEM_Nuclei_Segmentation_Report
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
<img src='/Snapshots/Nuclei_unique_ID.png' width='805' height='463'></br>

![relabeld nuc ID](/Snapshots/Nuclei_unique_ID.png)

The electron dense areas within the nuclei ROIs were then segmented using the Yen threshold algorithm (the electron dense areas are darker so I masked the pixels with a lower (i.e. darker) value than the Yen threshold value):

![masked electron dense rois](/Snapshots/Segmented_dense_rois.png)

To measure the heterochromatin along the nuclear envelope, a 25-pixel wide band was created along the nuclear envelope:

![25-pixel wide band](/Snapshots/25-pixel_band.png)

These bands were used to mask and measure the heterochromatin within the band:

![Heterochromatin in band](/Snapshots/Heterochromatin_25-pixel_band.png)

While electron microscopy is a powerful technique for evaluating the ultrastructure of the cells, the electron density cannot be directly compared between samples, so normalizing approaches are required for making comparisons. Additionally, with TEM on ultra thin sections, it is challenging to know if the section of sample is the same relative location from cell to cell, so using normalized shape descriptors are required to make comparisons.


## Output
**Important Note: I was not given a key for the images, so I was unable to assign the condition name to the data, all measurements and exported images were named as *sample#*-*cell#*-*image#*.** 

Example of the measurement tables and key below:

|    |   Unnamed: 0 |   object_ID |   Nuc_area_um^2 |   eccentricity |   equivalent_diameter_area_um^2 |   extent |   perimeter_um |   solidity |   Image_intensity_max |   Cell_ID |   Nuc_region_ID |   Band_ID |   Dense_nuc_regions_ID |   Heterochromatin_band_ID |   nuc_circularity |   heterochromatin_area_in_band_um^2 |   25-pixel_band_area_um^2 |   norm_area_heterochromatin_in_band |
|---:|-------------:|------------:|----------------:|---------------:|--------------------------------:|---------:|---------------:|-----------:|----------------------:|----------:|----------------:|----------:|-----------------------:|--------------------------:|------------------:|------------------------------------:|--------------------------:|------------------------------------:|
|  0 |            0 |           1 |         9.53172 |       0.728778 |                         3.4837  | 0.542944 |        16.7193 |   0.801982 |                 16603 |         1 |               1 |         1 |                      1 |                         1 |          0.428492 |                            1.21831  |                  2.45502  |                            0.496254 |
|  1 |            1 |           2 |         8.93387 |       0.563816 |                         3.37268 | 0.642898 |        12.5406 |   0.923839 |                 15908 |         1 |               2 |         2 |                      2 |                         4 |          0.713863 |                            0.767918 |                  1.81199  |                            0.423799 |
|  2 |            2 |           3 |        48.8305  |       0.612653 |                         7.88498 | 0.696099 |        38.268  |   0.958699 |                 17152 |         2 |               3 |         3 |                      3 |                         9 |          0.419015 |                            3.01564  |                  5.70286  |                            0.528794 |
|  3 |            3 |           4 |         9.92391 |       0.733061 |                         3.55465 | 0.54111  |        17.7804 |   0.811772 |                 16100 |         1 |               4 |         4 |                      4 |                        16 |          0.394466 |                            1.14778  |                  2.62218  |                            0.437721 |
|  4 |            4 |           5 |         1.05087 |       0.89476  |                         1.15672 | 0.56037  |         4.4924 |   0.948957 |                 15973 |         1 |               5 |         5 |                      5 |                        25 |          0.654336 |                            0.253501 |                  0.604733 |                            0.419195 |

The first and second column are indexing values for the rows and can be ignored.
|Key| Definition|
|---|-----------|
|object_ID | Nucleus object (if nucleus is fragmented, each fragment has it's own object ID)|
|Nuc_area_um^2 | Area of each nucleus fragment in um^2|
|eccentricity| Measure on a scale of 0-1 if the object is more ellipse shaped (1) or a perfect circle (0)|
|equivalent_diameter_area_um^2 | The diameter of a circle with the same area as the region |
|extent| Ratio of pixels in the region to pixels in the total bounding box (if you drew a box to enclose the object)|
| perimeter_um | length of the outline of the object in um |
|solidity | Ratio of pixels in the region to pixels of the convex hull image (if you drew a polygon to enclose the object) |
|Image_intensity_max | may grey value in the nucleus (not a helpful measurement, included when getting the label values of the overlapping objects)|
|Cell_ID | ID of the cell the nucleus compartment comes from|
|Nuc_region_ID| ID of the nucleus fragment/region (should match the object_ID column)|
|Band_ID | ID of the 25-pixel band created to measure heterochromatin (should match the Nuc_region_ID)|
|Dense_nuc_regions_ID | ID of mask object of segmented electron dense rois in nuclei regions (should match the Nuc_region_ID)|
| Heterochromatin_band_ID | ID of mask object of segmented electron dense rois in the 25-pixel band (should match the Nuc_region_ID)|
| nuc_circularity | Measure on a scale of 0-1 of how perfect of a circle the object is 0 being less perfect and 1 being a perfect circle |
| heterochromatin_area_in_band_um^2 | Area of 25-pixel band covered by electron dense heterochromatin in um^2|
|25-pixel_band_area_um^2 | Area of the 25-pixel band outlining the nucleus in um^2|
|norm_area_heterochromatin_in_band| fraction of 25-pixel band covered by heterochromatin calculated as heterochromatin_area_in_band_um^2/25-pixel_band_area_um^2 |

## Notes
If more data is to be collected, or this type of analysis will be used for other projects, I recommend creating an AI-segmentation tool for segmenting the nuclei. EM data can be challenging to apply traditional threshold approaches to reliably and AI-segmentation tools can be very helpful in this regard.

### Optional Analyses - what other information could you get from this data
There was mention in a previous conversation about measuring the nucleoli within the nuclei. This can can be added to the analysis if needed.