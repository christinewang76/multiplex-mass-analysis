This code analyzes the mass (m/z) spectra of exhaled breath from mice obtained using  proton-transfer-reaction mass spectrometry (PTR-MS). The intended results are to identify possible mass features that distinguished between healthy and Eml4-Alk mice in high throughput. The cohort contains healthy and tumor-bearing (Eml4-Alk) mice at 4, 5, and 6 weeks after tumor initiation, the same cohort used in the multiplexed vABN analysis (see Test_train_split_GB_classifier). 


Step 1: 
We analyzed the spectra within Python (v3.9.0) using SciPy (v1.16.2) peaker finder packages. After the peaks were found, we subtracted the peak intensities of breath samples from its own background (when no sample was exposed to the MS). We empirically chose a cut-off of peak height at 10 by empirically looking multiple data. Below 10, most signals were embedded within the background. 

Step 2: 
This step take the identified peaks and calculate the relative intensity to water (m/z ~ 19 Da) as proxy to each sample volume. Although mass calibration was performed for each spectrum, there remains some peak shifts between sample. We therefore use the maximal peak intensity within the range of m/z 18.5 and 20.0 Da as water reference. Simiarly, we located, for each nominal m/z, the local maximum within ±0.1 Da. These apex intensities formed the per-sample feature vectors. In this step, we also removed the intensities of exogenous VOCs from vABNs and those that belong to the ion source and internal calibration. 

Step 3:  
This step assembles all nominial m/z from each sample in the dataset and label them 1 for cancer and 0 for healthy. Intensities were assigned as 0 if mass was not found for the sample. Z-score was calculated across samples in the dataset. 

Step 4: 
Binary classification was performed the same as that described in the Test_train_split_GB classifier. Two datasets from repeated, independent cohorts, each containing healthy and tumor-bearing mice, were concatenated and splitted into 25% testing and 75% training. A gradient boosting classifier was implemented where parameters were obtained by gridsearchCV (cv=5). Accuracy was measured by the area-under-the-curve (AUC) of a receiver operating characteristic (ROC) curve. Shapley values were computed to quantify each mass feature’s contribution to the model output (log-odds), and features were ranked by mean |SHAP|. 
