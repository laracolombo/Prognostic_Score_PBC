# Modeling transplant-free survival in PBC patients
This analysis was part of a Biostatistic project that was carried in collaboration with @ErikBot9, @Ragescripter and Sabrina Sartori. 

The aim of our study was to develop a score to predict transplant-free survival in PBC patients.

## DESCRIPTION OF THE DISEASE 
Primary Biliary Cholangitis is an autoimmune liver disease in which bile ducts are slowly destroyed, blocking the bile flow and retaining it into the liver, which is a clinical condition called cholestasis that gradually leads to liver inflammation, fibrosis, cirrhosis and eventually liver death. 

It is still unclear how the disease develops, but it is certain that it mostly affects women even though its development is more damaging in males, especially if diagnosed at young age. This is a recurrent trend that was verified in many different studies, but it is very important to point out that the clinical course is extremely heterogeneous among patients: in fact, in some cases its development can be slowed or stopped by UDCA therapy, which is the only accepted medical treatment for this pathology. However, not all the patients respond to this therapy and in this case the only life-saving option is liver transplantation. Therefore, predicting outcome is critically important for the targeting and the timing of care.

## DATASET DESCRIPTION

In order to develop a score, we relied on a pool of patients who took part into a clinical trial of the drug D-penicillamine conducted by the Mayo Clinic. The dataset provides general information about 312 PBC patiens, along with histological parameters, laboratory values, symptoms and transplant-free survival data. 
In the past years, this therapy was proved to be ineffective in the treatment of this illness, therefore patients provide information on survival in the absence of an effective therapy. 

## PREPROCESSING OF THE DATASET 

Before proceeding with the analysis, we had to decide whether to consider the small group of transplanted patients that was present in our dataset. Since we wanted to model the probability of survival and therefore, for example, choose the most needing patient for a transplant, we realized that the data coming from already transplanted patients wouldn't have served this purpose too much and could indeed introduce a bias in the analysis. Given all these considerations, our final decision was to discard this group of patients.

## DESCRIPTIVE ANALYSIS

First, we present a comparison of the patients information provided by our dataset with the literature in order to verify that we have a representative subset of PBC patients. To do so, we compared the Kaplan-Meier survival curves for sex, age and treatment. 
We can conclude that our dataset is representative of PBC population and therefore we had data to develop a score that correctly addresses PBC patients.

## DEVELOPMENT OF THE SCORE

In order to model survival, we relied on a Cox survival model applying a cross validation technique to increase the validity of our results. 
 
In particular, we randomly split our dataset in a train and test subset, with a proportion of 70-30 respectively. Given that our dataset is unbalanced for what regards the survival status, we had to balance the two groups in order for them to have the same percentage of dead and alive patients. We then performed on the test subset a backward variable selection on the Cox regression model to select the most meaningful variables to use in our score.

In order to increase the robustness of our choice, we repeated the mentioned steps 1000 times, dividing our dataset in train and test in a 1000 different ways, while keeping the same 70-30 proportion, and performing a backward variable selection to each test subset, obtaining 1000 different models. We then recorded the variables that appeared at least half of the times, so the most consistent ones, and we selected among the many different models one that had exactly this 4 variables, which, as can be seen in the graph and in the image, are bilirubin, copper, stage and albumin.

Using then the coefficients of the Cox model, we were able to finally build our score. We then subtracted the mean in order to center our data, obtaining a score associated with a decreased risk when negative and associated with increased risk when positive.

## SCORE VALIDATION 

We then checked the results of the score on the test set, to see if indeed our model performs well on unseen data. We found that a 1-unit increase in our score almost doubles the hazard, which could be helpful also for the interpretation in a clinical setting. 
The global statistical significance of the model is supported by p-values in the range of [e-11, e-16], while the score was found to be significant in the Cox model with a p-value of 7.43e-13. 

Further details are found in the commented R markdown. 

## CLINICAL INTERPRETATION

As said before, positive scores were found to be correlated with an increased risk of death, but in order to find a more precise interpretation of the single values associated to each individual, we decided to divide patients in three groups of risk such as Low, Medium and High, based on the values of the tertiles of the score variable, that are shown below the boxplots in the slide. This cut-off allowed us to obtain three groups of equal size with almost 100 patients each, making them comparable in the further analysis. 

## SCORE VALIDATION 

In our analysis, found that the majority of patients that were alive after the end of the study were given a low or medium score based on their variables recorded at the beginning of it, while the majority of patients who had an higher score didn’t survive eventually. 

Additionally, we were able to assert that well-known markers of PBC showed higher values as the patients risk (computed by our score) incrased. 

Most importantly, our score can be employed to get the probability of survival of each single patient, which we think might be its main application in clinical practice. As an example, we computed the survival probability of three patients that were given mean scores of the low, medium and high risk groups. The results were a significant difference in survival: in fact, while the low-risk patient shows good chance of survival even after 10 years with a survival probability of 80%, the high risk patient showed a survival probability of just 40% already at 5 years, while almost a null probability at 10. The medium group patient instead shows  good survival probabilities overall, but they seem to decrease very rapidly as years pass and therefore should probably be taken under control. 

Finally, we were able to assert that our score is in accordance with established PCB prognostic scores by computing the respective Spearman's rank correlation coefficient.

## LIMITATIONS OF THE ANALYSIS 

Even though we applied cross validation techniques and we repeated the procedure 1000 times, we are perfectly aware that testing our score on the same dataset from which it was derived is a strong limitation of our analysis and might have led to the risk of overfitting. However, we were not able to find any other dataset that was adequate for testing our score. 

## BIBLIOGRAPHY 
Kim, W. R., Therneau, T. M., Wiesner, R. H., Poterucha, J. J., Benson, J. T., Malinchoc, M., Larusso, N. F., Lindor, K. D., & Dickson, E. R. (2000). A Revised Natural History Model for Primary Sclerosing Cholangitis. Mayo Clinic Proceedings, 75(7), 688–694. https://doi.org/10.4065/75.7.688
Goet, J. C., Harms, M. H., Carbone, M., & Hansen, B. E. (2018). Risk stratification and prognostic modelling in primary biliary cholangitis. Best Practice & Research Clinical Gastroenterology, 34–35, 95–106. https://doi.org/10.1016/j.bpg.2018.06.006
Gong, Y., Klingenberg, S. L., & Gluud, C. (2004). D-penicillamine for primary biliary cirrhosis. Cochrane Database of Systematic Reviews, 2010(1). https://doi.org/10.1002/14651858.CD004789.pub2
Grambsch, P. M., Dickson, E. R., Wiesner, R. H., & Langworthy, A. (1989). Application of the Mayo Primary Biliary Cirrhosis Survival Model to Mayo Liver Transplant Patients. Mayo Clinic Proceedings, 64(6), 699–704. https://doi.org/10.1016/S0025-6196(12)65350-6
Lammers, W. J., Kowdley, K. V., & Buuren, H. R. van. (2014). Predicting outcome in primary biliary cirrhosis. Annals of Hepatology, 13(4), 316–326. https://doi.org/10.1016/S1665-2681(19)30838-5
Locke, G. R., Therneau, T. M., Ludwig, J., Dickson, E. R., & Lindor, K. D. (1996). Time course of histological progression in primary biliary cirrhosis. Hepatology, 23(1), 52–56. https://doi.org/10.1002/hep.510230108
Marschall, H.-U., Henriksson, I., Lindberg, S., Söderdahl, F., Thuresson, M., Wahlin, S., & Ludvigsson, J. F. (2019). Incidence, prevalence, 
and outcome of primary biliary cholangitis in a nationwide Swedish population-based cohort. Scientific Reports, 9(1), 11525. https://doi.org/10.1038/s41598-019-47890-2
Monaco, A., Pantaleo, E., Amoroso, N., Bellantuono, L., Stella, A., & Bellotti, R. (2021). Country-level factors dynamics and ABO/Rh blood 
groups contribution to COVID-19 mortality. Scientific Reports, 11(1), 24527. https://doi.org/10.1038/s41598-021-04162-2
Royston, P., Moons, K. G. M., Altman, D. G., & Vergouwe, Y. (2009). Prognosis and prognostic research: Developing a prognostic model. 
BMJ, 338(mar31 1), b604–b604. https://doi.org/10.1136/bmj.b604
Images created with BioRender.com






















