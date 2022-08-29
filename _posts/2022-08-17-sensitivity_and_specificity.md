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

## Examples of AUC optimisation in the past

The AUC, as a common measure for most medical imaging problems, can be formulated as a ranking problem, in which it measures the expectation of drawing positive instances that are ranked higher than negative instances using some ranking functions π : X → [0, 1]:

AUC = E(π+ > π−),

where E(.) denotes the expectation over the data distribution.
The sample estimate of the AUC is the Wilcoxon–Mann–Whitney statistic [6]:

$\hat{AUC} = \sum_{i \in Y^+}\sum_{j \in Y^-} 1_{\{p_i > p_j \}}$,

where

 {% raw %}
$1_{\{p_i > p_j\}} = 
    \begin{cases}
        1, &\text{if } p_i > p_j\\
        0, &\text{otherwise}
    \end{cases}$
    {% endraw %}


and Y +, Y − denote the positive and negative class respectively, and pi = p(xi) denotes the assessed probability, which in this case is the Machine Learning model’s output. Directly optimising for the WMW statistic is not possible given the non-differentiable nature of the step function. Previous work [9] proposed a surrogate hinge loss that acts as an upper-bound for the step function

 {% raw %}
$\ell(\theta) = \sum_{i \in Y^+} \sum_{j \in Y^-} \max \{0, (p_i(\theta) - p_j(\theta)  \}$
{% endraw %}

Despite the objective function being differentiable, it does not often work well in large datasets due to the non-decomposable nature of the objective, which restricts the effectiveness of batch training.


##Training with Optimisation Constraints
As a way to circumvent the non-decomposable issue, [8] restricts the ranking to a particular threshold and optimises Sensitivity and Precision using a lower and upper bound surrogates


     {% raw %}
$tp(b) = \sum_{i\in{Y^+}}{1_{\{p_i\geq{b}\}}} \geq \sum_{i\in{Y^+}}{1-l_{n}(p,x_{i},y_{i})}$
{% endraw %}


     {% raw %}
$fp(b) = \sum_{i\in{Y^-}}{1_{\{p_i\geq{b}\}}} \leq \sum_{i\in{Y^+}}{l_{n}(p,x_{i},y_{i})}$
{% endraw %}

where $b$ is the chosen threshold and

         {% raw %}
$l_{n}(p,x_i,y_i) = 
    \begin{cases}
        \max(0,1-(p_i-b) & \text{if } i \in Y^+\\
        \max(0,1+(p_i-b) & \text{otherwise}\\
    \end{cases}$
{% endraw %}

This formulation serves as a foundation for our adapta- tion of the objective function that maximises Sensitivity at a chosen Specificity.

##Sensitivity@Specificity formulation

Based on the definitions of true positive (tp) and false positive (fp) in [8], their work, based on Sensitivity at a target Precision can be extended to the form relevant to the cancer screening problem outlined earlier. The Sensitivity at a set Specificity loss was derived as shown below.
Sensitivity@Specificity




extra:

However, in clinical practice, a threshold must be set for a decision to be made, often selected such that the model’s Specificity matches that of human read- ers. Experimental results show that by matching Specificity, the model’s Sensitivity can often fall short of radiologist level results, hence there is a need for an optimisation objective that maximises Sensitivity at a given Specificity.

α : the target specificity
b : the threshold at which the classification should be made

     {% raw %}
$\alpha$ : the target specificity

$b$ : the threshold at which the classification should be made

\[|Y^{-}| = \text{total number of negatives}\]

\[\mathcal{L}^{-} = \text{false positives}\]

\[|Y^{+}| = \text{total number of positives}\]

\[\mathcal{L}^{+} = |Y^{+}| - \text{tp (all positives covered by the model)}\]



\[ \max \dfrac{tp(f)}{|Y^{+}|} (Sensitivity)\]

\[\text{st. TNR(Specificity)} > 1-\alpha\]


is equivalent to 

\[ \max \dfrac{tp(f)}{|Y^{+}|}\]

\[st. \dfrac{fp(f)}{|Y^{-}|} < \alpha \leftrightarrow FPR<\alpha\]


 Given the previous definitions:
 
 it is known that
 
 
   \[tp (f) \geq{tp^{l}(f)} =|Y^{+}| - \mathcal{L}^{+}\]


   \[fp (f) \leq{fp^{u}(f)} = \mathcal{L}^{-}\]
   
   
   
   
   substitute a new objective:
   

    \[^{\max}_{f}  \dfrac {tp_{l}(f)} {{|Y^{+}|}} (TPR) \leftrightarrow ^{\max}_{f}  \dfrac {|Y^{+}|-\mathcal{L^{+}}} {{|Y^{+}|}} = 1-\dfrac{\mathcal{L}^{+}}{|Y^{+}|}\]
  
  
      \[st.  \dfrac {fp} {{|Y^{-}|}} (FPR) < \dfrac{fp^{u}}{|Y^{+}| }< \alpha \leftrightarrow st.  \dfrac {\mathcal{L}^{-}} {{|Y^{-}|}} < \alpha \leftrightarrow \mathcal{L}^{-} < |Y^{-}|\]

{% endraw %}

Hence the objective is:

     {% raw %}

\[ \min \dfrac {\mathcal{L}^{+}} {{|Y^{+}|}}\]
   
   
\[st.  \mathcal{L}^{-} -  \alpha {{|Y^{-}|}} < 0\]


and the loss function can be calculated by

\[L = \dfrac {\mathcal{L}^{+}}{{|Y^{+}|}} + \gamma (\mathcal{L}^{-} - \alpha {|Y^{-}|})\] \[\leftrightarrow L = \mathcal{L}^{+} + \gamma \mathcal{L}^{-}|Y^{-}| - \gamma{|Y^{+}|}{|Y^{-}|\alpha}\]

{% endraw %}

This loss function can replace BCE loss to adjust a model's performance to achieve the objective set out in this work, maximising Sensitivity at a set Specificity.



For the Sensitivity at Specificity to work, $\alpha$ is the inverse of the desired Specificity, $\gamma$ is a hyperparameter that requires tuning, and threshold is the threshold that is given by the post-test analysis to achieve a 96\% Specificity.



The application of hinge loss as an approximation has been replicated on the cancer screening dataset supplied by BreastScreen Australia. The rectified linear  hinge loss shows no improvement over cross entropy when AUC is examined, and although the Sensitivity at a target Specificity does improve. This demonstrates that the shape of the Receiver Operating Characteristic can be modified. In addition, the loss bound provided by a linear function can potentially be improved upon by imposing a tighter upper bound to the step function. 



It has been shown that ranking with a surrogate hinge loss is effective for maximising Sensitivity at a set Specificity, however, hinge loss does not provide a tight upper bound for the Zero-One loss step function. Cross entropy uses the log function to discriminate probabilities of groups with good performance on normally distributed datasets. The application of the log function as a tighter upper bound is an appealing area of future work. It has already been shown that ranking loss functions can be applied to specific non-decomposable objectives, and the work so far relies on hinge loss. Ranking with the logarithmic function as an upper bound has challenges related to numerical stability and further work to show it's effectiveness and practical use is required. The benefits of this future work are ranking loss methods that perform as well or better than BCE loss, but can be explicitly applied to non-decomposable objectives.





Ranking loss

Binary Cross Entropy to to Log Ratio


 {% raw %}
\[-\{\sum y_{i}\log p (p(y_{i}=1|x_{i})) + (1-y_{i}\log p(y_{i}=0|x_{i})\}\]

represents approximately $p(\centerdot |x), \text{using a neural net } ~ \hat{p}(|x)$

maximum likelihood
\[\Pi p(y=i|x)^{y}p(y=0|x)^{1-y}\]

\[log\Pi (...) =\sum \log (p(y=1|x)^{y} p(y=0|x)^{1-y}\]

\[=\sum y \log (p(y|x)^{y} + (1-y) \log (p(y=0|x))\]

\[= \{ - \sum_{x \in y^{+}} \log(p(y=1|x)) + \sum \log(1-p(y=1|x)) \}\]

\[p(y=1|x) \text{ as }p\]

\[- \{ \sum \log p^{+} + \sum \log (1-p^{-}) \}\]

\[\text{max}\log(1-p) \leftrightarrow 1-p\]
\[\text{eq min } p-p(y=1|x \in y^{-})\]
\[\text{min} \log(p^{-})\]

\[\sum\log(p^{+}) - \sum\log (p^{-})\]

\[= log(\dfrac{p^{+}}{p^{-}})\]

Non-Decomposable (requires a memory bank or large batch size)

\[log(\dfrac{p^{+}}{p^{-}}) \approx AUC\]

\[\text{max}(log^{+} - log^{-})\]

\[\text{max}(log^{-} - log^{+})\]

\[\text{max}(log(\dfrac{p^{-}}{p^{+}}))\]


\[\text{if } p^{-} < p^{+} -> \text{all correct} = 0\]



Decomposable

\[log(\dfrac{threshold}{p^{+}})\]
\[log(\dfrac{p^{-}}{threshold})\]

\[\dfrac{p^{-}}{threshold}<1log<0\]

\[\dfrac{threshold>p^{+}}{threshold<p^{-}}\]




\[threshold<p^{+}\rightarrow\dfrac{threshold}{p^{+}}<1, log(...)<0\]

{% endraw %}

  
  References
[1] David M. Green and John A. Swets. Signal Detection
Theory and Psychophysics. New York: Wiley, 1966.
[2] “2016 Workforce Survey Report: AUSTRALIA”. In:
Faculty of Clinical Radiology, The Royal Australian
and New Zealand Collage of Radiologists®, Date of
approval: 15 February 2018, 2018, pp. 44–45.
[3] Jeremy Irvin et al. “Chexpert: A large chest radiograph
dataset with uncertainty labels and expert compar-
ison”. In: Proceedings of the AAAI conference on
artificial intelligence. Vol. 33. 01. 2019, pp. 590–597.
[4] Kai Qin and Hong Pan. “MDPP Technical Report, Val-
idate and Improve Breast Cancer AI Approach”. In:
(Dec. 2020).
[5] Mattie Salim et al. “External Evaluation of 3 Commer-
cial Artificial Intelligence Algorithms for Independent
Assessment of Screening Mammograms”. In: JAMA
Oncology 6.10 (Oct. 2020), pp. 1581–1588. ISSN:
2374-2437. DOI: 10 . 1001 / jamaoncol . 2020 .
3321. eprint: https : / / jamanetwork . com /
journals / jamaoncology / articlepdf /
2769894 / jamaoncology \ _salim \ _2020 \
_oi \ _200057 \ _1619718170 . 78837 . pdf.
URL: https://doi.org/10.1001/jamaoncol.
2020.3321.
[6] H. B. Mann and D. R. Whitney. “On a Test of Whether
one of Two Random Variables is Stochastically Larger
than the Other”. In: The Annals of Mathematical Statis-
tics 18.1 (1947), pp. 50 –60. DOI: 10.1214/aoms/
1177730491. URL: https : / / doi . org / 10 .
1214/aoms/1177730491.
[7] Ian Goodfellow, Yoshua Bengio, and Aaron Courville.
Deep learning. MIT press, 2016.
[8] Elad Eban et al. “Scalable Learning of Non-Decomposable
Objectives”. In: Proceedings of the 20th International
Conference on Artificial Intelligence and Statistics.
Ed. by Aarti Singh and Jerry Zhu. Vol. 54. Proceedings
of Machine Learning Research. PMLR, 2017, pp. 832–
840.
[9] Lian Yan et al. “Optimizing Classifier Performance
via an Approximation to the Wilcoxon-Mann-Whitney
Statistic”. In: ICML. 2003.
[10] Mingxing Tan and Quoc Le. “EfficientNet: Rethinking
Model Scaling for Convolutional Neural Networks”.
In: (May 2019).
[11] Diederik Kingma and Jimmy Ba. “Adam: A Method
for Stochastic Optimization”. In: International Confer-
ence on Learning Representations (Dec. 2014)





  
