---
layout: post
title: "Sensitivity@Specificity formulation"
excerpt: "maximising sensitivity at a set specificity using a novel ranking loss equation"
tags: [ranking loss, AUROC]
comments: true
---

## The Receiver Operating Characteristic curve

The Area Under the Receiver Operating Characteristic (ROC) curve is used as a core metric to measure the effectiveness of machine learning classificationn models. The ROC curve [1] plots at every threshold the test’s Sensitivity (the proportion of positive samples that are correctly classified), against the Specificity(the proportion of negative samples that are correctly classified).  

In a situation where there is a large class imbalance, for example many negative outcomes in a cancer screening programme, the Specificity is the primary metric to consider because it affects the vast majority of people involved. A false positive can lead to personal distress or, in extreme cases reduce the legitimacy of the program.  

Conversely, Sensitivity is the reason for the program’s existence, and the maximum number of cases should be diagnosed as early as possible to to allow the person the greatest change of effective treatment and recovery. This compromise between Sensitivity and Specificity is intrinsic to all screening programs. It is important to know the strengths and weaknesses of a system, and the ROC curve provides a visual indicator of whose components can be quantified, as illustrated in figure 1.


Deep learning models demonstrate outstanding performance in complex pattern recognition tasks [3] and can be applied to radiology screening. If a ML model can perform to the standard of human readers, its integration into the breast cancer screening workflow can alleviate the resource burden without sacrificing performance [4]. However, the standard metric for machine learning performance is the Area Under the ROC curve (AUC). The AUC cannot be used to compare machine performance against that of a human, which is often not measured in AUC. In many cases, evaluation is made by choosing a decision threshold such that the model’s Specificity matches with human’s Specificity, and comparing between reader’s Sensitivity and model’s Sensitivity at the chosen threshold [5]. Under this evaluation procedure, Machine Learning models typically fall short of the human level Sensitivity. As such, it may be beneficial to improve the model’s performance by having an objective function that takes into account this evaluation procedure. This can be achieved by either optimising for the AUC directly, or to optimise for a model’s performance at a specific operating point.  


Optimising for AUC related metrics can be difficult as it involves ranking positive instances against negative instances in the whole population. An estimator for the AUC is the Wilcoxon Mann Whitney (WMW) statistic [6], which ranks instances in the training sample using the step function. However, the WMW statistic is inappropriate as a loss function because the step function is non-differentiable. Additionally, the requirement for exhaustive ranking means that batch training with stochastic gradient descent [7] is inadequate, as it does not allow for the comparison between instances across different batches, i.e. it is non-decomposable. Previous work circumvents the non-differentiable and non-decomposable problems by using a surrogate loss that ranks instances against a threshold [8]. In this paper, we extend on previous work and introduce a constrained optimisation objective that maximise Sensitivity and Specificity at a given threshold. Experimental results show that the proposed method improves the model’s Sensitivity at a set threshold over the Binary Cross Entropy (BCE) loss baseline.

*Examples of AUC optimisation in the past*
AUC = E(π+ > π−)


$AUC = E(\pi_+ > \pi_-)$

where $E(.)$ denotes the expectation over the data distribution.
The sample estimate of the AUC is the Wilcoxon–Mann–Whitney statistic [6]:

\begin{equation*}
    \hat{AUC} = \sum_{i \in Y^+}\sum_{j \in Y^-} 1_{\{p_i > p_j \}},
\end{equation*}


However, in clinical practice, a threshold must be set for a decision to be made, often selected such that the model’s Specificity matches that of human read- ers. Experimental results show that by matching Specificity, the model’s Sensitivity can often fall short of radiologist level results, hence there is a need for an optimisation objective that maximises Sensitivity at a given Specificity.





  
