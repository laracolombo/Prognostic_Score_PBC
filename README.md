# Modeling transplant-free survival in PBC patients
This analysis was part of a Biostatistic project that was carried in collaboration with @ErikBot9, @Ragescripter and Sabrina Sartori. 

The aim of our study was to develop a score to predict transplant-free survival in PBC patients.

## Description of the Disease 
Primary Biliary Cholangitis is an autoimmune liver disease in which bile ducts are slowly destroyed, blocking the bile flow and retaining it into the liver, which is a clinical condition called cholestasis that gradually leads to liver inflammation, fibrosis, cirrhosis and eventually liver death. 

It is still unclear how the disease develops, but it is certain that it mostly affects women even though its development is more damaging in males, especially if diagnosed at young age. This is a recurrent trend that was verified in many different studies, but it is very important to point out that the clinical course is extremely heterogeneous among patients: in fact, in some cases its development can be slowed or stopped by UDCA therapy, which is the only accepted medical treatment for this pathology. However, not all the patients respond to this therapy and in this case the only life-saving option is liver transplantation. Therefore, predicting outcome is critically important for the targeting and the timing of care.

## Dataset description

In order to develop a score, we relied on a pool of patients who took part into a clinical trial of the drug D-penicillamine conducted by the Mayo Clinic. The dataset provides general information about 312 PBC patiens, along with histological parameters, laboratory values, symptoms and transplant-free survival data. 
In the past years, this therapy was proved to be ineffective in the treatment of this illness, therefore patients provide information on survival in the absence of an effective therapy. 

## Preprocessing of the dataset 

Before proceeding with the analysis, we had to decide whether to consider the small group of transplanted patients that was present in our dataset. Since we wanted to model the probability of survival and therefore, for example, choose the most needing patient for a transplant, we realized that the data coming from already transplanted patients wouldn't have served this purpose too much and could indeed introduce a bias in the analysis. Given all these considerations, our final decision was to discard this group of patients.

## Descriptive analysis

First, we present a comparison of the patients information provided by our dataset with the literature in order to verify that we have a representative subset of PBC patients. To do so, we compared the Kaplan-Meier survival curves for sex, age and treatment. 
We can conclude that our dataset is representative of PBC population and therefore we had data to develop a score that correctly addresses PBC patients.

## Developement of the score

In order to model survival, we relied on a Cox survival model applying a cross validation technique to increase the validity of our results. 
 
In particular, we randomly split our dataset in a train and test subset, with a proportion of 70-30 respectively. Given that our dataset is unbalanced for what regards the survival status, we had to balance the two groups in order for them to have the same percentage of dead and alive patients. We then performed on the test subset a backward variable selection on the Cox regression model to select the most meaningful variables to use in our score.

In order to increase the robustness of our choice, we repeated the mentioned steps 1000 times, dividing our dataset in train and test in a 1000 different ways, while keeping the same 70-30 proportion, and performing a backward variable selection to each test subset, obtaining 1000 different models. We then recorded the variables that appeared at least half of the times, so the most consistent ones, and we selected among the many different models one that had exactly this 4 variables, which, as can be seen in the graph and in the image, are bilirubin, copper, stage and albumin.

Using then the coefficients of the Cox model, we were able to finally build our score. We then subtracted the mean in order to center our data, obtaining a score associated with a decreased risk when negative and associated with increased risk when positive.

## Score validation 

We then checked the results of the score on the test set, to see if indeed our model performs well on unseen data. We found that a 1-unit increase in our score almost doubles the hazard, which could be helpful also for the interpretation in a clinical setting. 
The global statistical significance of the model is supported by p-values in the range of [e-11, e-16], while the score was found to be significant in the Cox model with a p-value of 7.43e-13. 

Further details are found in the commented R markdown. 

## Clinical interpretation 

As said before, positive scores were found to be correlated with an increased risk of death, but in order to find a more precise interpretation of the single values associated to each individual, we decided to divide patients in three groups of risk such as Low, Medium and High, based on the values of the tertiles of the score variable, that are shown below the boxplots in the slide. This cut-off allowed us to obtain three groups of equal size with almost 100 patients each, making them comparable in the further analysis. 


















