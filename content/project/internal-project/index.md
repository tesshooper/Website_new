---
date: "2016-04-27T00:00:00Z"
external_link: ""
image:
  caption: Photo by rawpixel on Unsplash
  focal_point: Smart
links:

slides: example
summary: Principle Component Analysis for Common Cereal Brands.
tags:
- Deep Learning
title: Internal Project
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""
---

## ***Summary***  

Principal Component Analysis (PCA) can be useful when trying to understand correlations between variables in a large dataset. PCA methodology allows us to comprehensively analyze our data and extract as much information as possible in ony two dimensions. When conducting a PCA, our principle components are chosen based on the greatest variance. This analysis explores the relationship between nutrients found in common breakfast cereals, such as Kellog's Raisin Bran and General Mill's Cheerios. 

<br>

```{r Wrangle and Explore data }
### Explore and wrangle nutrient data
nutrients_df <- nutrients %>% 
  clean_names() # Clean data so headers are in snake case
## Filter out Breakfast Cereal foodgroup to explore
cereals <- nutrients_df %>% 
  filter(food_group == c("Breakfast Cereals")) %>% 
  select(-contains("common_name")) %>% # Get rid of common name column
  select(-contains("scientific_name")) %>% # Get rid of scientific name column
  select(-contains("usrda")) # Get rid of USRDA nutrient columns (will stick with earlier vitamin columns)
```
<br>

#### 1. Perform Principal Component Analysis on Breafast Cereal Nutrients

```{r PCA}
#### Perform PCA on breakfast cereal food group considering nutrients (energy kcal - zinc, columns 6:28)
# Take subset of nutrient data
cereal_subset <- cereals %>% 
  select(energy_kcal:zinc_mg)
# 23 components total
cereal_pca <- prcomp(cereal_subset, scale = TRUE)
#cereal_pca
# summary(cereal_pca)
# 32% of data described by first PC? 
```

```{r biplot}
## Exploratory plots 
#plot(cereal_pca)
#biplot(cereal_pca)
# Clean biplot
cereal_biplot <- autoplot(cereal_pca,
                          colour = NA,
                          loadings.label = TRUE,
                          loadings.label.size = 3,
                          loadings.label.colour = "black",
                          loadings.label.repel = TRUE) +
  theme_minimal() +
  ggtitle("Principal Component Analysis for \n Breakfast Cereal Nutrients") +
  theme(plot.title = element_text(hjust=0.5, face="bold"))
cereal_biplot
```
<br>
**Figure 1. Biplot of Principal Component Analysis (PCA) for Breakfast Cereal Nutrients.** PCA plot shows correlation between certain nutrients found in common breakfast cereals, such as Kellog's Raisin Bran and General Mill's Cheerios. 

*Interpreting our PCA*  

* Many of the vitamins (e.g. Vitamin B6, Vitamin B12, Vitamin C) are highly positively correlated.
* Vitamins and proteins are minimally correlated. 
* Sugar and protein are negatively correlated. 
* Manganese and copper are positively correlated. 
* It seems like there is minimal correlation of energy (kCal) with any of the other nutrients. 

<br>

```{r}
##### Interpreting biplot
### Biplots: an approximation of original multidimensional space, reduced to 2 dimensions with information about variables (as vectors) and observations (as points)
#Arrows show something about our loadings (combination of our eigenvalues, size of magnitude, and eigenvector, something about the direction 
#Numbers show location of data point (e.g. 12 refers to the patient in the 12th row) → shows how the individual data points (e.g. patients) are related to one another 
#Relative orientation of everything on biplot to each other is important. But if we change the orientation of everything, interpretation doesn’t change 
#The length of the line/arrow indicates variance in PC direction (loading/contribution of each variable to PC)
#The angle between lines/arrows indicates correlation
#0 degree difference = correlation of 1
#180 = correlation of -1
#90 or 270 = correlation of 0
###Interpretation: 
# The closer points are to each other, the smaller their Euclidean Distance - meaning they are more similar overall in multivariate space
# Biplots can make it easier to see multivariate groups and outliers
