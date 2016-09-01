# TREX: DEVELOPING A FRAMEWORK FOR EXTRACTION OF CT IMAGE FEATURES IN A RADIATION ONCOLOGY ENVIRONMENT

## INTRODUCTION
Imaging is a routine and increasingly important aspect of medical care in the era of precision medicine. Having evolved from being primarily used for diagnostic purposes, medical imaging plays a central role in defining prognosis, monitoring disease progression, screening, treatment planning and guidance, and assessment of therapeutic interventions. An already abundant and growing number of imaging technologies can provide anatomical, functional, and molecular information to non-invasively probe the spatial and temporal heterogeneities that are present in the underlying disease of an individual (1).

With the wealth of imaging studies now being conducted, medical image analysis has grown rapidly as means to improve clinical decision making (2–5). In particular, the field of radiomics involves the extraction and analysis of large numbers of advanced quantitative features from medical image data (5–7). Inspired by the “omics” revolution (i.e. genomics, proteomics, metabolomics), data gathered from a variety of imaging modalities are being routinely mined and analyzed to aid clinicians (5). Though much of the methodology is similar, radiomics is distinct from more narrowly defined computer-aided diagnosis (CAD) systems typically designed to solve one specific diagnostic task (5). As a result, radiomics is being applied to study a multitude of disease sites and interventions (5).

At its core, the radiomics hypothesis is that the quantifiable structure of an imaged volume of interest reflects its underlying physiology and pathology (1, 6). In turn, this can provide information that may be useful for diagnosis, prognosis, or prediction. The term “radiomics” has been popularized in the oncology community in recent years where mineable databases of quantifiable image measures have been created to characterize solid tumors. The image analysis techniques utilized, however, are not unique to oncologic imaging, nor are they unique to the discipline of medical imaging. Haralick et al. (8), for instance, originally proposed a set of measures to describe the texture of satellite images, but they have since become a widely used set of features to describe heterogeneity with multiple medical imaging modalities. A myriad of methods can be implemented, giving rise to datasets that extract hundreds or thousands of features, thus earning the -omics suffix to describe the field.

While technological advances and conception of the term “radiomics” have resulted in increased exposure and appeal, commonly implemented methods for extracting quantifiable image features have been well-established. Of particular interest is the utility of CT based image features to aid in quantification and classification of interstitial and diffuse lung diseases. Diagnosis of mild COPD, for example, is often challenging and lacks objectivity in assessment. In response, multiple groups have analyzed CT image sets to define low attenuation areas of the lung as quantitative measures of COPD status. Correlation of simple CT metrics to pathologic specimens (9, 10) and pulmonary function tests (11) demonstrates the potential of image-based analysis. The introduction of texture analysis methods (12) applied to multiple parenchymal pathologies (13–19) has further improved quantification and classification of underlying lung disease. This work has established a precedent for utilizing CT-based analysis of lung volumes that may achieve better characterization of disease prognosis and treatment-related response in a variety of medical disciplines.

The key difference between these existing studies and radiomics is in the size of the feature space and methods for analysis. Radiomics is partially defined by the high-dimensionality of the extracted image feature data. Computational requirements used to pose limits on the number and type of features to be extracted, but this obstacle is no longer a primary barrier to medical image analysis with current computing resources. Additionally, more sophisticated methods for statistical analysis, primarily machine learning methods, can be employed to account for the dimensionality of the data. Proper application of such techniques allows for thousands of features to be considered in construction of prognostic or prediction models while still being able to achieve a clinically useful and generalizable result.

While there is much promise, there are several limitations to radiomics research. Anatomical or image noise during acquisition, region of interest identification and segmentation, and appropriate statistical methods for analysis of high-dimensional datasets are barriers that must be conquered for radiomics to provide clinically relevant information. Secondary to these impediments, parameters utilized for feature extraction can vary and make it difficult to achieve reproducible results; however, this problem is more easily overcome. While a limited number of software packages suitable for radiomics feature extraction have been made available, only recently has a true open-source solution for reproducible radiomics research been shared in the form of the open source, MATLAB-based package, ibex (20). Similarly, we have designed a platform specific to the purposes of this project and studies to be presented in the following chapters. “TREX” is a MATLAB-based software platform for analysis of CT-based images that exist within the framework of a radiation therapy treatment planning system. It provides the ability to extract both image and dosimetric data using multiple methods. Further amendments have been incorporated to facilitate analysis specific to that of the normal lung volume, but TREX can be utilized for analysis of any segmented CT structure. Here we describe the current functionality of TREX.

## DESCRIPTION OF SOFTWARE
TREX was developed in a MATLAB (Mathworks, Natick, MA) environment and primarily facilitates analysis of CT images and regions of interest (ROIs) that exist within the Pinnacle<sup>3</sup> treatment planning system (Philips HealthCare, Fitchburg, WI). Accordingly, though it is an important part of high-throughput radiomics research, data preparation including image segmentation and dose calculation, rely on the tools available in Pinnacle<sup>3</sup>.

![](/misc/Figure TREX 1.png)

##### **Figure 1**. TREX project setup GUI. The user navigates through the TPS file system by selecting the server, institution, patient, plan, and structure (i.e. ROI). Optionally, a dose distribution associated with the selected plan can be added. Multiple entries are added to the project workspace to build the TREX project for subsequent image and dosimetric feature analysis.

A graphical user interface was constructed to facilitate browsing of an existing Pinnacle<sup>3</sup> TPS file system after establishing an FTP connection with the TPS server (Figure 1). The user is prompted to navigate each level of the TPS file system (institution, patient, and treatment plan) to identify the primary CT image set associated with a given plan for a patient of interest. The user can select and view the contoured structures, primary CT image set, and calculated dose distributions that exist within the selected patient treatment plan. The option also exists to create new structures based on simple Boolean operations applied to the set of existing contoured structures. The user builds a project by adding multiple structures, plans, and patients to the project workspace. The data can then be imported and converted for further analysis or visualization. Two main categories for data analysis are currently implemented: 1) CT-based image feature extraction and 2) dosimetric data extraction. Each category contains a subset of different modules to calculate different classes of features. A set of tools to visualize the image, structure, and dose data have also been created to facilitate local review (Figure 2).

![](/misc/Figure TREX 2.png)

##### **Figure 2**. An example of the current visualization module. Image, structure, dose, and feature maps can be displayed and browsed for easy review.

### Project Creation and Data Import
Each new TREX project is initiated by defining a specific project name and a local directory location. The user proceeds by establishing a connection with the Pinnacle<sup>3</sup> server and identifying the set of CT images and contoured structures to serve as input through the image and/or dose feature extraction algorithms. After selecting the image set and structure associated with a given patient treatment plan, the user adds an entry with the selected information to the project workspace. Optionally, an available dose distribution associated with the given plan can be added to the workspace for extraction and comparison of dosimetric data.

Once the setup workspace is populated with the patient of interest, including image, and structure data, TREX proceeds with a local import and conversion of the data to more easily facilitate analysis. This automated process steps through each entry in the project workspace, establishes a connection with the Pinnacle<sup>3</sup> server, and downloads all of the requisite data from the TPS in its native format. Each patient is given a subdirectory within the main project directory where the data is locally transferred. For each entry, the CT image data is subsequently read and stored as a 3-dimensional array with the available metadata in a binary MATLAB data format (.mat file). The contoured structure (i.e. ROI) data is read and converted to a binary mask with the same dimensions as the primary CT image set. The ROI data is saved as a separate MATLAB file along with available image metadata. If a trial dose distribution is included with a given entry, the native Pinnacle files are used to reconstruct the planned dose distribution as well. This is also saved as a separate MATLAB file. Each MATLAB file is saved to the appropriate patient directory and tagged with a randomly generated unique identifier. After each entry in the project setup workspace has been imported and converted to the desired format, dosimetric and image feature analysis can proceed.  

If changes are made to the project setup workspace, data import can easily be run again to add a new or remove an existing entry. If import is run again, a feedback mechanism is also established to look for any changes that may have also occurred in the TPS due to alterations in the selected ROIs and/or dose distributions.

### CT-based Image Feature Extraction
CT-based image (i.e. radiomics) feature extraction was the primary purpose for the creation of TREX and it was implemented in two different ways depending on how the ROI is defined.

1) Global/regional feature extraction: The imported and converted structure mask for each entry is the considered ROI for feature calculation. All voxels within the masked volume are used to generate global measures from the input image.
2) Local feature extraction: The imported and converted structure mask for each entry serves as the boundaries to limit calculation of local feature values. Features are calculated in smaller ROIs (e.g. 31 x 31 pixel blocks) within the larger masked structure. Depending on the spacing of the ROIs, feature values can be calculated on a voxel-by-voxel basis throughout the masked image or, if coarser spacing of ROIs is required, interpolation can be used to create a voxel-wise feature map at all locations in the masked structure volume.

With a given image set and ROI, six different radiomics feature classes were considered with independent modules being built in TREX for each:

* First Order Histogram Features: Twenty-three first order histogram features are extracted from each image. The features can be calculated from the original, unfiltered image. Additionally, prior to feature calculation, image preprocessing can be applied with 14 different filters or thresholds (2D and 3D gradient sobel, local entropy, local range, local standard deviation, 5 Laplacian of Gaussian 2D filters, and 4 image thresholds).
* Gray Level Co-occurrence Features: The gray level co-occurrence matrix (GLCM) is used to define texture in an image by determining the distribution of co-occurring voxel values along a given displacement (8). From each GLCM, 23 gray level co-occurrence features are extracted (8, 21). The original CT scan can be linearly downsampled to 16, 32, 64, 128, and 256 gray levels (bit depths 4-8). The displacement vector is defined by both the direction and distance for comparison of voxel pairs. Thirteen unique directions in 3-dimensional space are considered. Two additional GLCMs can be calculated by summing the GLCM from all axial (i.e. 2D) directions and from summing all 13 3D directions. The displacement is also defined using distances of 1, 3, and 5 voxels.
* Gray Level Run Length Features: The gray level run length matrix (GLRLM), similar to the GLCM, is used to define texture in an image by considering strings of consecutive voxels that have similar gray values along a given direction (22). From each GLRLM, 11 gray level run length features are extracted (22–25). The original image can be downsampled to bit depths from 4 to 8. As with the GLCM, 13 individual 3D directions as well as the 2D and 3D summed GLRLMs can be calculated.
* Neighborhood Gray-Tone Difference Features: The neighborhood gray-tone difference matrix (NGTDM) defines texture in an image by calculating the average gray level difference between each voxel and its neighboring voxels (26). Five different neighborhood gray-tone difference features are extracted from each NGTDM (26). As with the GLCM and GLRLM methods, the original image can be downsampled to bit depths of 4-8. Additionally, each neighborhood is defined by considering only immediately adjacent voxels (i.e. distance = 1) in two or three dimensions.
* Laws’ Filtered Features: Laws’ filters are a series of 5 one dimensional spatial filters that are convolved with the image to emphasize textural structure (27). Fifteen pairs of the Laws’ 1x5 filters are implemented and can be applied to each axial slice of the original image. Eight histogram metrics are subsequently extracted from the filtered image.
* Lung-Specific CT Features: Multiple methods have been proposed to quantify COPD in patients using CT. In total, 23 unique features (10, 17, 28–32) representing the volume and cluster density of low attenuation areas in the lung are calculated from the original image.

Further description of the calculated features is provided in[here](/misc/appendix_radiomics.pdf).

Feature extraction parameters specific to the radiomics feature class can be selected and project-specific parameter profiles can be defined within TREX (Figure 3 and Figure 4). Once the modules and parameters of interest are selected, image feature calculation can be initiated. TREX steps through each module (or class) sequentially. After all features have been calculated for every entry with a given feature module, the result is saved as a MATLAB file within the project directory. The extracted feature space is written to an array with each row representing a separate entry from the project workspace and each column defining a unique feature. If feature extraction is repeated with a given module using additional parameters, TREX searches the previously created feature space and ignores calculation of features that have already been completed to save computation time. Additionally, certain image calculation algorithms can take advantage of multiple CPUs via MATLAB's parallel processing toolbox.

![](/misc/Figure TREX 3.png)

##### **Figure 3**. Global image feature extraction parameter window. Six different radiomics feature classes can be used, each with their own set of control parameters.

![](/misc/Figure TREX 4.png)

##### **Figure 4**. Local image feature (i.e. map) extraction parameter window. Similar to Figure 3, but additional options to select the locally-defined ROI are included.

### Dosimetric Data Extraction
A necessary component of RT response assessment or prediction is the dosimetric data, which has been explicitly considered in TREX. After the data has been imported, several modules exist to extract dosimetric and plan-specific data (Figure 5). These include:

* Dose-Volume Histogram (DVH) Features: Using the imported dose distribution, a series of first order statistics are calculated in the given ROI. Additionally, absolute and relative volume receiving at least X Gy (aV<sub>x</sub>, rV<sub>x</sub>), minimum dose to the hottest X% volume (D<sub>x</sub>), and mean dose to the hottest and coldest X% volume (MOH<sub>x</sub>, MOC<sub>x</sub>) are calculated in 5% or 5 Gy intervals. In total 149 different dosimetric features are returned. This is the primary module for dosimetric data extraction.
* Location Features: Based on the masked ROI volume, the absolute and relative locations of the bounding box encompassing the ROI as well as the location of the ROI centroid are determined. This has proven useful for defining the relative position of the GTV volume within the total lung as in Reference (33).
* Plan Features: The Pinnacle<sup>3</sup> data is parsed to return a set of 25 different treatment plan parameters. This includes parameters pertaining to the machine energy, number of beams, number of fractions, machine name, and heterogeneity correction, among others.
* Spatially-weighted DVH: Borrowing from the method proposed by Vinogradskiy et al. (34), a set of DVH features was calculated from several spatially-weighted dose distributions. Four different weighting matrices were created by linearly scaling values on the range [0, 1] in the following orientations: superior-inferior, anterior-posterior, right-left, and radially (see Reference (34)). Each weighting matrix was created to match the dimensions of the bounding box encompassing the ROI such that the dose distribution can be weighted with each of the considered weighting schemes.
* Dose-Radiomics Histogram (DRH) Features: As detailed above, a set of local CT image features can be calculated to give a series of voxel-wise feature maps. These feature maps are subsequently used as surrogates of function and used to extract DRH features in a manner analogous to the concept of a dose-function histogram (35–37).

Further description of the features is provided [here](/misc/appendix_dosimetric.pdf).

![](/misc/Figure TREX 5.png)

##### **Figure 5**. Dosimetric feature extraction parameter window. Five different classes are implemented.

### Initial Validation
Accuracy of the dosimetric and image feature calculation is vital to build robust and useful clinical decision making tools. Validation of the image feature calculation methods was challenging due to scarcity of packages for such analysis during development. Several virtual phantom image sets were employed when possible to test calculated feature values. A package originally created for MRI texture analysis, Mazda (38), was used to aid in validation of some features. Additionally, the alpha version of IBEX (20) served as a check in the later stages of development. In some cases, the implementation of the feature algorithm can result in differences upon comparison. Typically this is a function of the preferred methods used for image preprocessing or bit depth downsampling that can easily be accounted for, but this does complicate comparison of results between different software packages.

Dosimetric features have been compared to those calculated in the TPS. Minor differences in the calculated volumes, presumably due to partial voxel averaging within Pinnacle<sup>3</sup> produce differences in feature values on the order of ±3%. Multiple treatment plans were also exported from Pinnacle<sup>3</sup> using a DICOM exporter. These plans were subsequently imported into CERR (39) to facilitate direct comparison of scan, structure, and dose objects in a MATLAB environment.

## SUMMARY
TREX was initially conceived to provide a single framework for image feature calculation and dosimetric data extraction from RT treatment plan data. Other packages are now widely used specifically for radiomics analysis (20) and well-established radiotherapy research platforms such as CERR (39) have begun to develop similar radiomics tools. This leaves the future of TREX uncertain; however, the package is easily extensible. Moving outside of the Pinnacle<sup>3</sup> environment, DICOM import and ROI contouring tools can be integrated. Additionally, new feature classes can be implemented by creation of new modules and current methods can be extended with alternative image preprocessing schemes and new features. The ability to move beyond CT-based image feature calculation to other modalities is also on the horizon. We have successfully implemented multiple methods to permit true high-throughput calculation of mineable image and treatment data.

## REFERENCES
1. Gillies RJ, Anderson a R, Gatenby R a et al. The biology underlying molecular imaging in oncology: From genome to anatome and back again. Clinical radiology. 2010;65:517–21.
2. Lambin P, Roelofs E, Reymen B et al. “Rapid Learning health care in oncology” – An approach towards decision support systems enabling customised radiotherapy’. Radiotherapy and Oncology. 2013;109:159–164.
3. Lambin P, Stiphout RGPM van, Starmans MHW et al. Predicting outcomes in radiation oncology–multifactorial decision support systems. Nature reviews. Clinical oncology. 2013;10:27–40.
4. Lambin P, Zindler J, Vanneste BGL et al. Decision support systems for personalized and participative radiation oncology. Advanced Drug Delivery Reviews.
5. Gillies RJ, Kinahan PE, Hricak H. Radiomics: Images Are More than Pictures, They Are Data. Radiology. 2015:151169.
6. Lambin P, Rios-Velazquez E, Leijenaar R et al. Radiomics: Extracting more information from medical images using advanced feature analysis. European journal of cancer (Oxford, England : 1990). 2012;48:441–6.
7. Kumar V, Gu Y, Basu S et al. Radiomics: The process and the challenges. Magnetic resonance imaging. 2012;30:1234–48.
8. Haralick R, Shanmugam K, Dinstein I. Textural Features for Image Classification. IEEE Transactions on Systems, Man and Cybernetics. 1973;SMC-3:610–621.
9. Gould GA, MacNee W, McLean A et al. CT Measurements of Lung Density in Life Can Quantitate Distal Airspace Enlargement—An Essential Defining Feature of Human Emphysema. American Review of Respiratory Disease. 1988;137:380–392.
10. Müller NL, Staples CA, Miller RR et al. “Density mask”. An objective method to quantitate emphysema using computed tomography. Chest. 1988;94:782–7.
11. Kinsella M, Müller NL, Abboud RT et al. Quantitation of emphysema by computed tomography using a “density mask” program and correlation with pulmonary function tests. Chest. 1990;97:315–21.
12. Tuceryan M, Jain AK. Texture Analysis. In: Chen CH, Pau L-F, Wang PS-P, eds. Handbook of pattern recognition & computer vision. 2nd ed. River Edge, NJ: World Scientific; 1999:207–248.
13. Uppaluri R, Mitsa T, Sonka M et al. Quantification of pulmonary emphysema from lung computed tomography images. American journal of respiratory and critical care medicine. 1997;156:248–54.
14. Uppaluri R, Hoffman EA, Sonka M et al. Computer recognition of regional lung disease patterns. American journal of respiratory and critical care medicine. 1999;160:648–54.
15. Chabat F, Yang G-Z, Hansell DM. Obstructive lung diseases: Texture classification for differentiation at CT. Radiology. 2003;228:871–7.
16. Hoffman E a, Reinhardt JM, Sonka M et al. Characterization of the interstitial lung diseases via density-based and texture-based analysis of computed tomography images of lung structure and function. Academic radiology. 2003;10:1104–18.
17. Xu Y, Sonka M, McLennan G et al. Sensitivity and specificity of 3-D texture analysis of lung parenchyma is better than 2-D for discrimination of lung pathology in stage 0 COPD. In: Amini AA, Manduca A, eds.; 2005:474–485.
18. Xu Y, Beek EJR van, Hwanjo Y et al. Computer-aided classification of interstitial lung diseases via MDCT: 3D adaptive multiple feature method (3D AMFM). Academic radiology. 2006;13:969–78.
19. Xu Y, Sonka M, McLennan G et al. MDCT-based 3-D texture classification of emphysema and early smoking related lung pathologies. IEEE transactions on medical imaging. 2006;25:464–75.
20. Zhang L, Fried DV, Fave XJ et al. Ibex: An open infrastructure software platform to facilitate collaborative work in radiomics. Medical Physics. 2015;42:1341–1353.
21. Aerts HJWL, Velazquez ER, Leijenaar RTH et al. Decoding tumour phenotype by noninvasive imaging using a quantitative radiomics approach. Nature Communications. 2014;5.
22. Galloway MM. Texture analysis using gray level run lengths. Computer Graphics and Image Processing. 1975;4:172–179.
23. Chu A, Sehgal CM, Greenleaf JF. Use of gray value distribution of run lengths for texture analysis. Pattern Recognition Letters. 1990;11:415–419.
24. Dasarathy BV, Holder EB. Image characterizations based on joint gray level—run length distributions. Pattern Recognition Letters. 1991;12:497–502.
25. Tang X. Texture information in run-length matrices. Image Processing, IEEE Transactions on. 1998;7:1602–1609.
26. Amadasun M, King R. Textural features corresponding to textural properties. Systems, Man and Cybernetics, IEEE Transactions on. 1989;19:1264–1274.
27. Laws KI. Textured Image Segmentation.; 1980.
28. Coxson HO, Rogers RM, Whittall KP et al. A Quantification of the Lung Surface Area in Emphysema Using Computed Tomography. American Journal of Respiratory and Critical Care Medicine. 1999;159:851–856.
29. Mishima M, Hirai T, Itoh H et al. Complexity of terminal airspace geometry assessed by lung computed tomography in normal subjects and patients with chronic obstructive pulmonary disease. Proceedings of the National Academy of Sciences. 1999;96:8829–8834.
30. Gierada DS, Yusen RD, Villanueva IA et al. Patient selection for lung volume reduction surgery*: An objective model based on prior clinical decisions and quantitative ct analysis. Chest. 2000;117:991–998.
31. Coxson HO, Whittall KP, Nakano Y et al. Selection of patients for lung volume reduction surgery using a power law analysis of the computed tomographic scan. Thorax. 2003;58:510–514.
32. Yuan R, Nagao T, Paré PD et al. Quantification of lung surface area using computed tomography. Respiratory Research. 2010;11:153.
33. Hope AJ, Lindsay PE, El Naqa I et al. Modeling radiation pneumonitis risk with clinical, dosimetric, and spatial parameters. International journal of radiation oncology, biology, physics. 2006;65:112–24.
34. Vinogradskiy Y, Tucker SL, Liao Z et al. A novel method to incorporate the spatial location of the lung dose distribution into predictive radiation pneumonitis modeling. International journal of radiation oncology, biology, physics. 2012;82:1549–55.
35. Marks LB, Sherouse GW, Munley MT et al. Incorporation of functional status into dose-volume analysis. Medical physics. 1999;26:196–9.
36. Miften MM, Das SK, Su M et al. Incorporation of functional imaging data in the evaluation of dose distributions using the generalized concept of equivalent uniform dose. Physics in medicine and biology. 2004;49:1711–21.
37. Nioutsikou E, Partridge M, Bedford JL et al. Prediction of radiation-induced normal tissue complications in radiotherapy using functional image data. Physics in medicine and biology. 2005;50:1035–46.
38. Szczypinski PM, Strzelecki M, Materka A. Mazda - a software for texture analysis. In: IEEE; 2007:245–249.
39. Deasy JO, Blanco AI, Clark VH. CERR: A computational environment for radiotherapy research. Medical physics. 2003;30:979–85.
