# Routines for the automatic analysis of moles

The pBoorm folder contains routines written by Peter Boorman, please direct questions to - 

The MJohnson folder contains routines written by Michael Johnson, please direct questions to Michael.Johnson0100@gmail.com.

At the time of writing, there are 5 notebooks contained within the MJohnson folder, each dedicated to a specific task. These notebooks are:

- imageProcessingFunction.ipynb
- sextractorQualitySlimed-Again.ipynb
- finalResults_SExtractor.ipynb
- multiRegionalResults.ipynb
- alignAlgorithm-CosineRule-Copy-alltoany.ipynb

To run these notebooks, you will need to install the following packages: astropy, matplotlib, numpy, regions, pandas, pandasql, image_registration, PIL, and scipy.

The following section of the documentation is devoted to describing the purpose, usage, function, and future plans for each notebook individually.

-------------------------------

# imageProcessingFunction.ipynb

Purpose: To process standard images into fits images, ready for processing by astronomical algorithms.

Usage: Enter the path to the target directory into the makeFits() function and the fits images will be created in the working directory. An optional normalisation value can also be entered and will be used to normalise the images.

Function: the makeFits() function splits the images into RGB components and translates them to fits format. It simultaneously crops the time stamp from the bottom of the images, transforms the bit rate to 16 bits in order to be compatible with SExtractor, normalises the images (optional), and creates an image which is the average of the three RGB images.

Future Plans: The optimal combination of RBG images for mole detection is still undetermined. The current analysis uses the simple average of the three initial images but this is unlikely to be optimal. 

-------------------------------------

# sextractorQualitySlimed-Again.ipynb

Purpose: To analyse the quality of mole detection when using different combinations of SExtractor input parameters, the specific quality metrics being completeness and accuracy. 

Usage: To use this notebook you need a working SExtractor installation, SExtractor compatible fits images, and DS9 region files which contain the location of the moles within the fits images. To create these DS9 region files, simply open the images with DS9, create regions around all observed objects and save them, example .reg files are included in the regions folder. The relevant SExtractor parameters also need to be specified at the start of the notebook, along with corresponding value ranges for each. This notebook will store all of the relevant statistics in the specified output dir and create 3D surface plots showing how the completeness and accuracy vary with different input parameters for each image. 

Function: This code operates by first computing the detected mole locations for images when using all possible combinations of chosen input parameters. These locations are then compared to the "true" locations of moles in the images, found by manually labelling moles through DS9 and in order to creation .reg files. The notebook then transforms these files into pandas dataframes and SQL queries are used to cross rerference their position with that of the detected moles. Finally, 3D graphs are made for the completeness and accuracy of the mole detection when using different input parameters. The axes for this graph correspond to three varried attribute, due to this, for the graph function to work three input parameters need to be chosen to be varied.  

Future Plans: The low hanging fruit for futher work on this notebook is experimentation with attributes and the values ranges. The attributes currently included are believed to be the three most impactful for object detection however this is not necessarily the case. Granularity of the value ranges is also important but drastically increases the computation time. One improvement for effciency would be to transfer the data to spark data frames instead of pandas and use Apache spark to compute the SQL queries. 

Finally, the notebooks finalResults_SExtractor.ipynb and multiRegionalResults.ipynb are both dedicated to displaying the results produced by sextractorQualitySlimed-Again.ipynb. The first of which tests out different region sizes for what is considered to be a matched mole between two separate runs. The results from this notebook ultimately show that this region size does not have meaningful implications for the quality of the results and any reasonable estimate for this is acceptable. The second notebook displays metrics such as the average quality of the transient detection as well as the quality of the detection in correlation with properties of the detected moles, such as elongation or ellipticity. 

-----------------------------------------------

# alignAlgorithm-CosineRule-Copy-alltoany.ipynb

Purpose: An alignment algorithm which can match moles between two images of a person taken years apart. Traditional aalgorithms struggle as people can change so much between images, therefore this algorithm aligns purley based on the relative positions of detected moles.

Usage: This notebook takes as input astropy tables detailing the location of moles in two images of the same region of a person. It returns a dictionary which contains the indexes of all objects which are found to match between the two images. The quaity of the results produced heavily depend on the quality of the input tables, it is therefore very important to have the SExtractor settings fine tuned. 

Function: The alignment algorithm works by first calculating the distance between each pair of objects within a table and the angle between one chosen master object and each other pair of objects by using the cosine rule. Once these values have been calculated for all combinations in both tables, theses values are matched between the two table to find moles which have the similar sets of both angles and distances (within a tollerance). The best matches are then used to determine which objects are likely to be the same in both images. This corresponding objects are then stored as a dictionary. 

Future Plans: This algorithm is currently a prototype, capable of aligning two images. The first extension would be to add more images to be matched, this would likely improve the function of the algorithm, hence a higher tollerance could be used. With regards to processing efficiency, again the SQL queries over pandas dataframes are far from optimal so transferring this to a framework such as Apache Spark would greatly decrease the run time.
