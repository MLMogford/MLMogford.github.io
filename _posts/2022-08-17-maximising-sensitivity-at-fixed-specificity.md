---
layout: post
title: "Sensitivity@Specificity formulation"
excerpt: "maximising sensitivity at a set specificity using a novel ranking loss function"
tags: [ranking loss, AUROC]
comments: true
---

## The Receiver Operating Characteristic curve

The Area Under the Receiver Operating Characteristic (AUROC) curve is used as a core metric to measure the effectiveness of machine learning classificationn models. The ROC curve [1] plots, at every threshold, the model’s Sensitivity (the proportion of positive samples that are correctly classified), against its Specificity (the proportion of negative samples that are correctly classified).  

In a situation where there is a large class imbalance within the data, for example many negative outcomes in a cancer screening programme where radiographs are used to detect lesions that may indicate the presence of cancer, the AUROC is the metric preferred to accuracy because it better reflects the ability of a model to correctly predict positive (the true positive rate, TPR; or Sensitivity) and negative samples (the true negative rate, TNR; or Specificity) rather than overclassifying the majority calss. With respect to the cancer screening programme example, the AUROC is not sufficient, although a higher AUROC metric is desirable, there is a limit to the practicality of high metric scores. This limit is manifest in two ways:  

1/ The model may overfit to the samples available for training, validation and testing; it is not possible to include all samples that exist currently and samoples yet to be collected in future screening sessions and train a model on these. If a model achieves very high AUROC (0.99 or 1), then this model may not generalise to newly collected samples. If it were possible to collect all past and future samples and add them to the model, the hardware constraints would lead to unfeasable training time  

&nbsp;nb. There may be the possibility to reduce the training sample size to samples that capture the entirity of the feature space during training and this could reduce the number of samples required, but online discarding of samples that do not provide gradient would be required, and this would not extend into the feature space potentially provided by new samples added. Medical imaging hardware is progressing at a rapid pace, rendering older images redundant and unable to assist in the prediction of newer image types, future images are out of distribution (OOD) for these presently available images.  

2/ Clinicians do not work under the paradigm of AUROC performance, this metric is best modified to represent a threshold, set at a fixed Sensitivity or Specificity to measure performance. In the case of a cancer screening programme, the Specificity is the primary metric to consider because it affects the vast majority of people involved. At a programme level small percentage of false positive results affects a vast number of people; and at a personal level, each can lead to personal distress or, in extreme cases, reduce the legitimacy of the program.  

&nbsp;Conversely, Sensitivity is the ability to detect the cancerous lesions within the population, the maximum number of cases should be diagnosed as early as possible to to allow the person the greatest chance of effective treatment and recovery. This compromise between Sensitivity and Specificity is intrinsic to all screening programmes. 

It is important to know the strengths and weaknesses of a system, and the _ROC curve_ provides a visual indicator of whose components can be quantified, as illustrated in figure 1.

<!-- ![ROC curve](/images/ROC.png) -->

<img src="https:/images/ROC.png" width="450">


<!-- ![image](https://your-image-url.type) with <img src="https://your-image-url.type" width="600"> -->

Deep learning models demonstrate outstanding performance in complex pattern recognition tasks [3] and can be applied to screening programmes where radiographs are used as a part of the screening process. If a machine learning (ML) model can perform to the standard of human readers, its integration into the cancer screening workflow can alleviate the resource burden without sacrificing performance [4]. It has been demomnstrated that in out example, Specificity is a more likely metric to anchor in this half of the Sensitivity-Specificity trade-off paradigm, and maximise Sensitivity, we can choose the benchmark Specificity of top ranking Radiologists. This way we know with reasonable certainty that the number of false positives will affect a similar proportion of people to the human system, and focus on the Sensitivity component of the metric. Evaluation is made by choosing a decision threshold such that the model’s Specificity matches with human’s Specificity, and comparing between human’s Sensitivity and model’s Sensitivity at the chosen threshold is common practice [5],andML models typically fall short of the human level Sensitivity. As such, it may be beneficial to improve the model’s performance by having an objective function that takes into account this evaluation procedure. While this can be achieved by either optimising for the AUC directly, a preferred  methon may be to optimise for a model’s performance at a specific operating point. Even if the AUROC remains unchanged, the curve may be manipulated by the loss function to, in the case of the example, more accurately classify true positives at a set tru negative rate.  


Optimising for AUC related metrics can be difficult as it involves ranking positive instances against negative instances in the whole population. An estimator for the AUC is the Wilcoxon Mann Whitney (WMW) statistic [6], which ranks instances in the training sample using the step function (illustreten in Figure 2). However, the WMW statistic is inappropriate as a loss function because the step function is non-differentiable. Additionally, the requirement for exhaustive ranking means that batch training with stochastic gradient descent [7] is inadequate, as it does not allow for the comparison between instances across different batches, i.e. it is non-decomposable. Previous work circumvents the non-differentiable and non-decomposable problems by using a surrogate loss that ranks instances against a threshold for the optimise Precision (the number of true positives out of all samples classified as positive) at a fixed Sensitivity[8]. This previous work can be extended upon to introduce a constrained optimisation objective that maximise Sensitivity and Specificity at a given threshold. 

## Examples of AUC optimisation in the past

The AUC, as a common measure for most medical imaging problems, can be formulated as a ranking problem, in which it measures the expectation of drawing positive instances that are ranked higher than negative instances using some ranking functions _π : X → [0, 1]_:

&nbsp;  

&nbsp;_AUC = E(π+ > π−)_,   

&nbsp;  
&nbsp;  

&nbsp;where _E(.)_ denotes the expectation over the data distribution.  

&nbsp;   

&nbsp;The sample estimate of the AUC is the Wilcoxon–Mann–Whitney statistic [6]:  


&nbsp;  


<!-- ![1wmw](/images/equations/1wmw-stat.png) -->
&nbsp;<img src="https:/images/equations/1wmw-stat.png" width="200">  

&nbsp;  



<!-- ![2wmw-stat](/images/equations/2wmw-stat.png) -->
<img src="https:/images/equations/2wmw-stat.png" width="300">  

&nbsp;   

and _Y +_, _Y −_ denote the positive and negative class respectively, and _pi = p(xi)_ denotes the assessed probability, which in this case is the Machine Learning model’s output. Directly optimising for the WMW statistic is not possible given the non-differentiable nature of the step function. Previous work [9] proposed a surrogate hinge loss that acts as an upper-bound for the step function

&nbsp;  


<!-- ![3hinge](/images/equations/3hinge.png) -->
&nbsp;<img src="https:/images/equations/3hinge.png" width="300">

&nbsp;  

<!-- ![zero one loss](/images/zeroOne.png) -->
&nbsp;<img src="https:/images/zeroOne.png" width="400">

&nbsp;  

Despite the objective function being differentiable, it does not often work well in large datasets due to the non-decomposable nature of the objective, which restricts the effectiveness of batch training.

&nbsp;  


## Training with Optimisation Constraints
As a way to circumvent the non-decomposable issue, [8] restricts the ranking to a threshold and optimises Sensitivity and Precision using a lower and upper bound surrogates

&nbsp;  

<!-- ![4tp-and-fp](/images/equations/4tp-and-fp.png) -->
&nbsp;&nbsp;<img src="https:/images/equations/4tp-and-fp.png" width="300">

&nbsp;  

&nbsp;where _b_ is the chosen threshold and  

&nbsp;  

<!-- ![5tp-and-fp](/images/equations/5tp-and-fp.png) -->
&nbsp;<img src="https:/images/equations/5tp-and-fp.png" width="350">



This formulation serves as a foundation for an adaptation of the objective function that maximises Sensitivity at a chosen Specificity.  


## Sensitivity@Specificity formulation


Based on the definitions of true positive (_tp_) and false positive (_fp_) in [8], their work, based on Sensitivity at a target Precision can be extended to the form relevant to the cancer screening problem outlined earlier. The Sensitivity at a set Specificity loss was derived as shown below.
Sensitivity@Specificity  


&nbsp;  

&nbsp;α : the target specificity
&nbsp;_b_ : the threshold at which the classification should be made

&nbsp;  

<!-- ![6-send-and-spec](/images/equations/6-send-and-spec.png) -->
&nbsp;<img src="https:/images/equations/6-send-and-spec.png" width="250">

&nbsp;  

<!-- ![7-send-spec](/images/equations/7-send-spec.png) -->
&nbsp;<img src="https:/images/equations/7-send-spec.png" width="350">

&nbsp;  

Given the previous definitions:
 
it is known that

&nbsp;  

<!-- ![ 8-sens-spec](/images/equations/8-sens-spec.png) -->
&nbsp;<img src="https:/images/equations/8-sens-spec.png" width="400">


&nbsp;  


&nbsp;and the loss function can be calculated by


&nbsp;  



This loss function can replace BCE loss to adjust a model's performance to achieve the objective set out in this work, maximising Sensitivity at a set Specificity.



For the Sensitivity at Specificity to work, α is the inverse of the desired Specificity, γ is a hyperparameter that requires tuning, and threshold is the threshold that is given by the post-test analysis to achieve a desired Specificity (i.e. 1-0.96=0.4 for a 96% Specificity).  



Experimental results showed the rectified linear hinge loss (as seen in Figure 2) did not improvem over cross entropy when AUROC was examined, although the Sensitivity at a target Specificity does improve. This demonstrates that the shape of the Receiver Operating Characteristic curve can be modified. In addition, the loss bound provided by a linear function can potentially be improved upon by imposing a tighter upper bound to the step function.   



Ranking with a surrogate hinge loss is effective for maximising Sensitivity at a set Specificity, however, hinge loss does not provide a tight upper bound for the Zero-One loss step function. Cross entropy uses the log function to discriminate probabilities of groups with good performance on normally distributed datasets. The application of the log function as a tighter upper bound is an area of future work that shows theoretical promise. It has already been shown that ranking loss functions can be applied to specific non-decomposable objectives, and the work so far relies on hinge loss. Ranking with the logarithmic function as an upper bound has challenges related to numerical stability and further work to show it's effectiveness and practical use is required. The benefits of this future work are ranking loss methods that perform as well or better than BCE loss, but can be explicitly applied to non-decomposable objectives. The formulation of a decomposable logarithmic function bounded ranking loss function is described below.



&nbsp;  


## Ranking loss

Binary Cross Entropy to Log Ratio

&nbsp;  

<!-- ![ 9-bce-to-log-ratio](/images/equations/9-bce-to-log-ratio.png) -->
&nbsp;<img src="https:/images/equations/9-bce-to-log-ratio.png" width="400">

&nbsp;  


**Non-Decomposable** (requires a memory bank or large batch size)

&nbsp;  


<!-- ![10-non-decomposable-formulation](/images/equations/10-non-decomposable-formulation.png) -->
&nbsp;<img src="https:/images/equations/10-non-decomposable-formulation.png" width="200">

&nbsp;  


**Decomposable**

&nbsp;  

<!-- ![11-decomposable-formulation](/images/equations/11-decomposable-formulation.png) -->
&nbsp;<img src="https:/images/equations/11-decomposable-formulation.png" width="300">

&nbsp;  

## References  

1. David M. Green and John A. Swets. Signal Detection Theory and Psychophysics. New York: Wiley, 1966.
2. “2016 Workforce Survey Report: AUSTRALIA”. In: Faculty of Clinical Radiology, The Royal Australian and New Zealand Collage of Radiologists®, Date of approval: 15 February 2018, 2018, pp. 44–45.
3. Jeremy Irvin et al. “Chexpert: A large chest radiograph dataset with uncertainty labels and expert comparison”. In: Proceedings of the AAAI conference on artificial intelligence. Vol. 33. 01. 2019, pp. 590–597.
4. Kai Qin and Hong Pan. “MDPP Technical Report, Validate and Improve Breast Cancer AI Approach”. In:(Dec. 2020).
5. Mattie Salim et al. “External Evaluation of 3 Commercial Artificial Intelligence Algorithms for Independent Assessment of Screening Mammograms”. In: JAMA Oncology 6.10 (Oct. 2020), pp. 1581–1588. ISSN:2374-2437.DOI:10.1001/jamaoncol.2020.3321.eprint:https://jamanetwork.com/journals/jamaoncology/articlepdf/2769894/jamaoncology\_salim\_2020\_oi\_200057\_1619718170.78837.pdf.URL: https://doi.org/10.1001/jamaoncol.2020.3321.
6. H. B. Mann and D. R. Whitney. “On a Test of Whether one of Two Random Variables is Stochastically Largerthan the Other”. In: The Annals of Mathematical Statistics 18.1 (1947), pp. 50 –60. DOI: 10.1214/aoms/1177730491. URL: https://doi.org/10.1214/aoms/1177730491.
7. Ian Goodfellow, Yoshua Bengio, and Aaron Courville. Deep learning. MIT press, 2016.
8. Elad Eban et al. “Scalable Learning of Non-Decomposable Objectives”. In: Proceedings of the 20th International Conference on Artificial Intelligence and Statistics. Ed. by Aarti Singh and Jerry Zhu. Vol. 54. Proceedings of Machine Learning Research. PMLR, 2017, pp. 832–840.
9. Lian Yan et al. “Optimizing Classifier Performance via an Approximation to the Wilcoxon-Mann-Whitney Statistic”. In: ICML. 2003.
10. Mingxing Tan and Quoc Le. “EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks”. In: (May 2019).
11. Diederik Kingma and Jimmy Ba. “Adam: A Method for Stochastic Optimization”. In: International Conference on Learning Representations (Dec. 2014)
