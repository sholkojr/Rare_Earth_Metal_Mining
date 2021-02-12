

# Goal of Project

Rare earths are a hot topic right now in mining with limited supplies concentrated in China.  Demand is increasing with the rise in complex electronics. The goal of this project is to predict where rare earth minerals are concentrated using the concentration of other minerals.  In typical drill core, rare earth minerals are not commonly assayed due to cost. Therefore, it is useful to correlate rare earth elements with other more commonly assayed components. 

# Explanation of Jupyter notebooks

Two analysis were completed - one with a global dataset of drill core.  In this analysis, mainly chemical components were used as features in the machine learning model. This file is labelled "Rare Earth" 

A second analysis was completed for Colorado.  This analysis used additional USGS aerial survey information to see if that improved the machine learning results.  This file is "Colorado All".

# Rare Earth Analysis - Global 

## Dataset and Exploratory Analysis 

The main dataset has about 1 million data points from all over the world and is mostly the results of chemical assaying for minerals.  The datapoints were drill core or similar. The complete dataset had many extra features such as datasources, rock type etc. These features were mostly dropped later as the chemical components were far more useful. 

Exploratory data analysis was completed showing the distributions of individual rare earth elements via histograms.  Note the log distribution. 

![rare_earth_dist1](https://github.com/sholkojr/Rare_Earth_Metal_Mining)

The rare earth elements were added together to create the a "rare earth" value in parts per million. 

![TotalRareEarthDist](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/full_rare_earth_dist.png)

The dataset of 1 million points did not have complete data for the rare earths.  There were about 100,000 data points with rare earth information present. The 100,000 data points with rare earth information present was used for the machine learning model.  The model results were then used on the entire dataset to predict where rare earths might exist. 

A correlation matrix was completed to determine if there were any correlations between the rare earth elements and other elements. There did appear to be some positive and negative correlations amongst other minerals, so the project appears to be worth pursuing. 

![Corr_matrix](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/Correlation_matrix.png)


## Machine Learning Model 

This dataset was fairly sparse with many missing values.  The best model to handle a spare data set without additional feature engineering is a tree-based model. A XGBoost model was chosen over random forest because XGboost is better able to handle unbalanced datasets.  In this case, it is more important to look for very high concentrations of rare earths which creates the unbalanced training set.  

Additionally, the tree based models are potentially more useful for understanding feature importance versus a neural network model.  The dataset was huge, so dropping parameter was useful to reduce processing time when running the model. 

For the XGBoost model, data normalization is not required and was not completed. Encoding was not required as only numerical distributions of numbers were used for features. 

Two XGBoost (Extreme Gradient Boosting) tree models were attempted.  An XGBoost regression model did not do very well - likely due to the small number of datapoints with high concentrations of rare earths.

A XGBoost classification model was then attempted. The rare earth data was divided into zero and ones based on rare earth concentration.  Initially a cut-off of 100 ppm was used and yielded good results, but little differentiation among high and low rare earth finds.  As the cut-off increased, the model became even more accurate and the important features even more pronounced.  A cut-off of 1000 ppm was used in the last interation with good results. At this cut-off, there were about 1700 positive datapoints and 78,000 negative datapoints.  An accuracy score of 99.5% was achieved with a cut-off of 1000 ppm.

The initial results with XGBoost classification were very good with >95% accuracy.  Initially, default values were used for XGBoost, but when the confusion matrix was considered, the high accuracy score was mainly due to a very good classification of the negative points.  The positive points did not fare as well.

Two parameters were changed - the ppm cut-off point for "positive" rare earth was increased and the "scale_pos_weight" factor was increaesed to 150 (default is 1).  This parameter in XGBoost gives more weighting to the positive data points.  In this case, with an unbalanced dataset, the positive weighting increase improved accuracy score to 98.8%.

The final confusion matrix results were: 

![confusion](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/Confusion_Matrix1.png)

A shap plot was used to determine feature importance.  The red values to the right indicate a positive correlation between high feature values and the rare earth value of 1 (or higher than 1000 ppm).  Red values to the left indicate a negative correlation between the feature and the target.

![shap_plot](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/Shap.png)

There is a strong positive correlation between high concentrations of rare earth elements and: 
- Thorium
- Niobium
- Hafnium
- Lead, metallic
- Gallium
- Zirconium
- Strontium
- Barium
- Lead oxide
- Cobalt
- Manganese oxide

There is a strong negative correlation between high correlations of rare earth elements and: 
- Aluminum oxides
- Chrome
- Sodium oxides
- Vanadium
- Uranium
- Rubidium
- LOI (ie hydrated or weathered ores)

The more complete list of features was captured using the XGboost built in feature importance function.  This shows the number of times that the feature was used in the model - the higher scores at the top are the more important features: 

![FullFeatures](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/feature_importance.png)

## Mapping

The model was then used to determine where high concentrations of rare earths might exist - the entire dataset of a million points was used.  There were approximately 6000 positives points and 1 million negatives.  Subtracting off the original existing rare earth assays with higher than 1000 ppm rare earths (about 1700), this is a find of another 4300 datapoints that predict high concentrations of rare earths.  

The positive (blue points) and negative (red points) were plotted on world maps. 

![negative](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/Rare_earth_less1000.png)

![positive](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/Rare_earth_Greater_1000.png)

## Comparison with Another DataSet

The USGS has a compilation of known rare earth deposits with latitude and longitude coordinates for 577 points.  These are plotted in purple.  The blue positive points are from the above analysis 

![otherdata](https://github.com/sholkojr/Rare_Earth_Metal_Mining/blob/main/MachineLearning/Resources_Machine_Learning/Known_RE_vs_Predicted.png)

# Colorado Analysis

A similar methodology was followed but also included aerial mapping for Colorado (from USGS).  The maps were processed in GIS software and and matched by latitute and longitude points with the survey information.  Features such as radioactivity and magnetic properties were then added to the dataset.



