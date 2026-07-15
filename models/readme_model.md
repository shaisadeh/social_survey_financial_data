## 1. Baseline Model Evaluation

Prior to training the supervised classification models, a baseline classifier
was evaluated to establish a reference benchmark for model performance. The
baseline was implemented using a `DummyClassifier` with the **uniform**
prediction strategy, which assigns class labels with equal probability
independently of the input features.

The **uniform** strategy was adopted since the **F1-score** was defined as
the primary evaluation metric of this study. A majority-class predictor would
classify all observations as the dominant class, yielding an F1-score of zero
for the minority class. The use of uniform random predictions yields non-zero
Recall and F1-score values, which are subsequently used as the baseline for
comparison with the supervised models.

Model performance was assessed on the independent test set using
**Accuracy**, **Recall**, and **F1-score**. The resulting metrics constitute
the reference benchmark against which all supervised models are evaluated.

## 2. Supervised Classification Using GMM-Derived Features

A hybrid unsupervised–supervised learning framework was developed with the
primary objective of improving classification performance. The motivation for
using a Gaussian Mixture Model (GMM) was based on the assumption that the
observations represent samples from a heterogeneous population, where
different subgroups may exist within the data distribution. Such subgroups
may exhibit different statistical characteristics, which can potentially be
represented using a mixture of Gaussian components.

Therefore, the GMM was incorporated as an additional feature generation
component within the classification pipeline. Instead of relying only on the
original feature representation, the model was used to extract an additional
probabilistic representation of the observations.

The GMM was fitted on the input feature space and used to generate soft
cluster membership probabilities for each observation. These probability-
based features describe the association of each sample with the Gaussian
components estimated by the model and provide additional information about
the position of each observation within the learned distribution.

The generated GMM features were not used instead of the original variables.
Instead, they were concatenated with the original feature representation,
creating an augmented feature space:

**Original Features + GMM-Derived Features → Classification Model**

The expanded representation was evaluated to determine whether the additional
GMM-based features improve classification performance compared with models
trained using the original feature space alone.

The complete learning process was implemented within a unified `Pipeline`,
combining preprocessing, GMM-based feature generation, and supervised
classification. The pipeline included missing-value imputation using
`SimpleImputer`, feature standardization using `StandardScaler`, GMM feature
generation, and classification using the selected supervised model.

Three supervised classification algorithms were evaluated:

- Logistic Regression with L2 regularization 
- Random Forest
- Gradient Boosting

For each classifier, models using the original feature representation were
compared with models using the augmented feature space containing both
original and GMM-derived features.

### Hyperparameter Optimization

Model selection was performed using `GridSearchCV` with three-fold
cross-validation. The optimization process evaluated the complete pipeline,
including GMM parameters, classifier-specific hyperparameters, and
classification decision thresholds.

The search procedure included both configurations with GMM-based features and
configurations using the original feature representation only. The optimal
configuration was selected according to the highest cross-validation
**F1-score**.

The selected pipeline configuration was subsequently retrained on the full
training dataset and evaluated on an independent test set. The final test set
performance and selected model parameters are reported in the evaluation
section.

To prevent data leakage, all data-dependent transformations were performed
within the same `Pipeline`. During each cross-validation fold, imputation
parameters, scaling parameters, and GMM parameters were learned exclusively
from the corresponding training partition and then applied to the validation
partition. The classifier was subsequently trained using the transformed
training data and evaluated on the corresponding validation data.

After the optimal configuration was identified, the complete pipeline was
retrained using the full training dataset and evaluated on an independent
test set.

                   Training Data
                              |
                              v
                    +-------------------+
                    |   GridSearchCV    |
                    |       CV = 3      |
                    +-------------------+
                              |
                              v
                  Repeat for each CV Fold

        +-----------------------------------------+
        |              Pipeline                   |
        |                                         |
        |  Training Fold Only                     |
        |                                         |
        |  1. Missing Value Imputation            |
        |     (Median learned from Train Fold)    |
        |              |                          |
        |              v                          |
        |  2. Feature Scaling                     |
        |     (Parameters learned from Train Fold)|
        |              |                          |
        |              v                          |
        |  3. GMM Fitting                         |
        |     (Distribution learned from          |
        |      Train Fold only)                   |
        |              |                          |
        |              v                          |
        |  GMM-Derived Features                   |
        |                                         |
        +-----------------------------------------+
                              |
                              v

        +-----------------------------------------+
        |        Feature Augmentation             |
        |                                         |
        | Original Features + GMM Features        |
        |                                         |
        +-----------------------------------------+
                              |
                              v

        +-----------------------------------------+
        |     Supervised Classification           |
        |                                         |
        | Logistic Regression                     |
        | Random Forest                           |
        | Gradient Boosting                       |
        +-----------------------------------------+
                              |
                              v

                 Validation Fold Evaluation
                         (F1 Score)

**Note:**  
The GridSearchCV evaluation included two pipeline configurations:
with GMM-based feature generation and without GMM feature generation.

### Final Model Evaluation

After the hyperparameter optimization process, the selected configuration of
each classifier was retrained on the complete training dataset and evaluated
on an independent test set.

To assess the contribution of the GMM-based feature generation stage, an
additional evaluation was performed using an equivalent pipeline in which
only the GMM transformation was removed. All remaining components, including
the preprocessing steps, the supervised classifier, the optimized
hyperparameters, and the decision threshold, were kept unchanged.

 GRIDSEARCHCV RESULTS
                        |
                        v
             +----------------------+
             |   CV Results Table   |
             +----------------------+
                        |
                        v
          +---------------------------+
          |  For Each Classifier      |
          |---------------------------|
          | Logistic Regression       |
          | Random Forest             |
          | Gradient Boosting         |
          +---------------------------+
                        |
                        v
     Select Best GMM Configuration
                        |
                        v
             Create Two Pipelines
                        |
               +--------+--------+
               |                 |
               v                 v
         With GMM         Without GMM
     (same parameters) (same parameters)
               |                 |
               +--------+--------+
                        |
                        v
      Train on X_train & Evaluate on X_test
          (Accuracy, Recall, F1)
                        |
                        v
         Build Comparison Results Table
                        |
                        v
      Highlight Best CV & Best Test Model

This evaluation design provides a controlled comparison in which the only
difference between the two pipelines is the inclusion of the GMM-derived
features, allowing their contribution to the classification performance to be
evaluated independently of the remaining model components.

The final performance of all evaluated models was measured on the independent
test set using Accuracy, Recall, and F1-score.

## 4. Final Test Evaluation

The final model configuration has been selected according to the highest
cross-validation F1-score obtained during the GridSearchCV optimization
process, including the corresponding pipeline structure and optimized
hyperparameters.

The reported results correspond to the performance of the selected fitted
pipeline on the independent test set. Model selection, training, and
hyperparameter optimization have been completed in the previous stages.
This section presents the resulting evaluation metrics without additional
training or optimization.

The evaluation includes Accuracy, F1-score, and a classification report,
providing precision, recall, and F1-score for each class, together with
aggregated measures summarizing the overall model performance.

## 5. Hyperparameter Sensitivity Analysis

This section examines the impact of individual hyperparameter variations
on the performance of the selected model.

The initial parameter configuration is determined from the best-performing
model identified during the GridSearchCV optimization process. These selected
parameter values define the reference configuration for the sensitivity
analysis.

For each experiment, a single hyperparameter is varied while all other
parameters remain fixed according to the selected configuration. Each modified
configuration is independently fitted on the training dataset, and the
resulting predictive performance is quantified on the test set using Accuracy,
Recall, and F1-score.

                 Best Model Selected by GridSearchCV
                              |
                              v

+--------------------------------+     +--------------------------------+
| Test Hyperparameter Values     | --> | Change One Hyperparameter      |
|                                |     |                                |
| n_estimators | learning_rate   |     | Keep Remaining Parameters Fixed|
| max_depth    | threshold | C   |     |                                |
+--------------------------------+     +--------------------------------+
                                                   |
                                                   v
                                      +--------------------------------+
                                      | Retrain Model on X_train       |
                                      +--------------------------------+
                                                   |
                                                   v
                                      +--------------------------------+
                                      | Evaluate Model on X_test       |
                                      | Accuracy | Recall | F1-score   |
                                      +--------------------------------+
                                                   |
                                                   v
                                      +--------------------------------+
                                      | Generate Performance Plots     |
                                      | Hyperparameter vs Metrics      |
                                      +--------------------------------+

The resulting plots describe the relationship between individual
hyperparameter values and the corresponding performance metrics. The red
dashed line represents the parameter value obtained from the GridSearchCV
optimization.

The selected model configuration and its associated parameters are summarized
to provide the context for the sensitivity analysis.

## 6. Model Performance Evaluation on Test Set

This section evaluates the final CV-selected model on the independent test set
using complementary classification metrics.

The evaluation includes the confusion matrix, ROC curve with ROC-AUC score,
and Precision-Recall curve with Average Precision score.

## 7. SHAP Model Interpretation

The SHAP analysis is applied to the final model configuration selected
according to the highest cross-validation (CV) performance.

The selected fitted pipeline is used to transform the test data through the
preprocessing and feature generation stages. SHAP values are subsequently
calculated for the final classifier to quantify the contribution and relative
importance of individual features to the model predictions.

The analysis considers the complete feature representation provided to the
classifier, including processed original features and generated GMM-based
features when applicable.

## 8. Results

Before presenting the results, it is important to note that two versions of the model notebook are included:

* `models4Mid_GMM_pip_2_1_forData.ipynb`
* `models4Mid_GMM_pip_2_1_forData_Quick.ipynb`

The two notebooks are identical in terms of their implementation and workflow.
The only difference lies in the hyperparameter values evaluated during the Cross-Validation Grid Search.

The notebook `models4Mid_GMM_pip_2_1_forData.ipynb` is the full version whose results are presented in the following sections.
It performs an extensive hyperparameter search over a broad parameter space to identify
the optimal configuration for each candidate model and ultimately select the best-performing model among all the models evaluated.
Consequently, this notebook requires a substantial amount of computation time.

As part of the complete project, the notebook `models4Mid_GMM_pip_2_1_forData_Quick.ipynb` was developed to support the interactive dashboard.
Rather than repeating the computationally expensive hyperparameter search, it uses the hyperparameter values selected by the full notebook,
allowing the modeling pipeline to execute in a fraction of the time and making it suitable for interactive use.

Finally, it should be emphasized that the purpose of the full notebook extends beyond identifying the best-performing 
model or determining the hyperparameter regions that yield optimal performance. The extensive hyperparameter search is 
also intended to ensure a fair comparison among the different candidate models by evaluating each model under its best-performing configuration.
Furthermore, it provides the basis for assessing the impact of the GMM-based feature generation stage by comparing the performance
of each optimized model with and without the GMM features.

## 8.1 Comparative Evaluation Results of the Different Models

We now proceed to the summary of the model evaluation results obtained after completing the optimization and evaluation
process described in Sections 2 and 3.

The following table, generated in Section 3 of the code, presents a comprehensive summary of the performance
achieved by the different models after the full modeling pipeline, including hyperparameter optimization and model evaluation:

BEST MODEL COMPARISON: WITH GMM vs WITHOUT GMM
================================================================================================================================================================
|         Model          |      GMM       |  GMM_N  |    Cov     |  Threshold  |    C    |  Trees   |  Depth  |     LR     |    CV_F1    |   Accuracy   |    Recall    |      F1      |
|------------------------|----------------|---------|------------|-------------|---------|----------|---------|------------|-------------|--------------|--------------|--------------|
| ***GradientBoosting*** | ***With GMM*** | ***7*** | ***full*** |  ***0.3***  | ***-*** | ***90*** | ***3*** | ***0.07*** | ***0.566*** | ***0.7779*** | ***0.5899*** | ***0.5492*** |
|    GradientBoosting    |  Without GMM   |    -    |     -      |     0.3     |    -    |    90    |    3    |    0.07    |      -      |    0.7685    |    0.5647    |    0.528     |
|      RandomForest      |    With GMM    |    5    |    full    |     0.3     |    -    |    90    |    7    |     -      |   0.5614    |    0.7656    |    0.5331    |    0.5106    |
|      RandomForest      |  Without GMM   |    -    |     -      |     0.3     |    -    |    90    |    7    |     -      |      -      |    0.7713    |    0.5615    |    0.5298    |
|   LogisticRegression   |    With GMM    |    7    |    full    |     0.3     |  0.01   |    -     |    -    |     -      |   0.5513    |    0.7489    |    0.5457    |    0.4993    |
|   LogisticRegression   |  Without GMM   |    -    |     -      |     0.3     |  0.01   |    -     |    -    |     -      |      -      |    0.746     |    0.5331    |    0.4906    |
================================================================================================================================================================
For each classifier, the displayed hyperparameters were selected from the
Cross-Validation results with GMM enabled.
The corresponding 'Without GMM' model uses exactly the same hyperparameters,
with only the GMM feature generation stage removed, allowing a direct
comparison of the contribution of the GMM features.
The CV_F1 column reports the Cross-Validation F1 score of the selected 'With GMM' configuration.
Since the 'Without GMM' models were not selected through Cross-Validation,
no CV_F1 value is reported for those rows.
Accuracy, Recall and F1 were computed on the independent test set after
retraining each pipeline on the complete training data.
* indicates the model selected by GridSearchCV according to the highest
Cross-Validation F1 score (CV_F1).
** indicates the model that achieved the highest Test F1 score.
*** indicates the model that achieved both the highest Cross-Validation
F1 score (CV_F1) and the highest Test F1 score.

The comparison between the models using GMM-based features and their corresponding baseline models without GMM features indicates that
the contribution of the GMM feature generation stage is marginal. Although the inclusion of GMM features leads to minor changes in the evaluation
metrics for some classifiers, these changes are not consistent across the different model architectures. Therefore, the results do not provide 
evidence of a substantial or systematic improvement resulting from the addition of GMM features within the evaluated modeling framework.

Considering the primary evaluation metrics, Accuracy and F1-score, the six evaluated configurations demonstrate highly similar performance levels. 
Accuracy values range from 0.7460 to 0.7779, while F1 scores range from 0.4906 to 0.5492. The **Gradient Boosting Classifier** with GMM features achieved 
the highest overall performance, obtaining the highest Accuracy (0.7779) and F1-score (0.5492). However, the performance gap between this configuration 
and the remaining models is relatively small, indicating that the evaluated classifiers exhibit comparable predictive capability rather than a clear 
separation in performance.

The remaining evaluation metrics show a similar overall trend, although the Recall metric indicates a clearer advantage for the **Gradient Boosting Classifier** 
with GMM features. This configuration achieved the highest Recall value (0.5899), compared with the highest Recall values obtained by the other approaches, 
such as RandomForest without GMM (0.5615) and LogisticRegression with GMM (0.5457). This suggests that the Gradient Boosting Classifier provides improved 
identification of positive instances while also achieving the highest Accuracy and F1-score among the evaluated configurations. Nevertheless, the differences 
across the complete set of evaluation metrics remain relatively limited, indicating that the tested approaches exhibit comparable overall performance.

Regarding the classification threshold, all evaluated configurations converged to the same selected value of 0.3. Since the threshold selection was optimized 
according to the F1-score, this value represents the optimal trade-off between Precision and Recall with respect to the primary evaluation objective. 
The selected threshold being lower than the default value of 0.5 indicates that the models achieved improved F1-score performance by shifting the decision 
boundary toward a higher sensitivity to positive instances. This adjustment allows the models to capture additional positive cases while maintaining an 
appropriate balance between Precision and Recall.

Regarding the GMM covariance structure, all models that incorporated GMM features selected the **full covariance** configuration. This indicates that allowing
correlations between the different feature dimensions within each GMM component was preferable for the evaluated dataset. However, since the overall contribution 
of GMM features was found to be marginal, the selected covariance structure appears to have a limited influence on the final model performance compared with the 
choice of classifier and the overall modeling pipeline.

## 8.2 Class-Level Performance Analysis of the Selected Model

As part of Section 4 of the implementation, a class-level performance analysis was conducted for the selected model:


Selected Model:
GradientBoostingClassifier

Test Accuracy:
0.7778581765557163

Test F1:
0.5491923641703378

Classification Report:
              precision    recall  f1-score   support

           0       0.87      0.83      0.85      1065
           1       0.51      0.59      0.55       317

    accuracy                           0.78      1382
   macro avg       0.69      0.71      0.70      1382
weighted avg       0.79      0.78      0.78      1382


The class-level analysis shows a significant difference in performance between the two classes, with substantially better classification results for class 0 
compared with class 1. This difference is likely related to the class imbalance in the dataset, which makes the classification of the minority class more challenging.

The class-level analysis shows a significant difference in performance between the two classes, with substantially better classification results for class 0 compared
 with class 1. This difference is likely associated with the class imbalance present in the clean dataset, which contributes to the observed performance gap between 
 the two classes.

This class distribution is also reflected in the selected classification threshold. Since the threshold was optimized according to the F1-score, the selected value 
of 0.3, lower than the default value of 0.5, suggests that a shifted decision boundary provided a more suitable Precision–Recall trade-off for the evaluated dataset.

# 8.3 Hyperparameter Sensitivity Analysis of the Selected Model

The following section presents the sensitivity analysis results obtained in Section 5 of the implementation for the selected Gradient Boosting Classifier. 
The effect of varying the main hyperparameters on model performance is evaluated using Accuracy, Recall, and F1-score:

Fig. 1 – n_estimators Sensitivity Analysis

The results indicate that the model performance remains relatively stable across the evaluated range of the number of trees. A minor improvement is observed around 
the selected configuration, suggesting that the chosen value provides a favorable performance level. However, the limited variation in the evaluation metrics indicates 
that the model is not highly sensitive to changes in this parameter.

Fig. 2 – learning_rate Sensitivity Analysis

The results indicate that lower learning rate values than the selected configuration lead to a decrease in all evaluated metrics. A moderate increase in the learning 
rate results in a minor improvement, with an additional local maximum observed at a learning rate value of 0.2. However, further increases in the learning rate 
lead to a gradual decline in performance, although the changes remain relatively small. Overall, the model demonstrates limited sensitivity to this parameter across 
the evaluated range.

Fig. 3 – max_depth Sensitivity Analysis

The results indicate that model performance remains relatively stable across the evaluated range of maximum depth values. A minor local maximum is observed at the selected 
configuration, while no substantial changes are observed for most depth values. At a maximum depth of 7, the Recall metric shows a slight decrease, whereas the overall variation 
in the evaluation metrics remains limited.

Fig. 4 – Classification Threshold Sensitivity Analysis

The results indicate that the selected threshold value of 0.3 provides the highest F1-score among the evaluated configurations. Increasing the threshold beyond this value leads 
to a gradual decrease in F1-score. In contrast, Accuracy improves as the threshold increases and stabilizes around a threshold value of 0.4, while Recall shows a consistent 
decrease with increasing threshold values. These results demonstrate the trade-off between the different evaluation metrics and support the selection of the threshold according 
to the F1-score optimization criterion.

## 8.4 Confusion Matrix, ROC Curve, and Precision-Recall Curve (Fig.5)
The ROC analysis resulted in a ROC-AUC value of 0.7914, indicating that the model demonstrates a reasonable discriminative capability between the two classes based on the predicted
 probability scores. The Precision-Recall analysis yielded an Average Precision value of 0.5489, providing an evaluation of the model’s ability to identify positive-class instances
while accounting for the existing class imbalance.

## 8.5 SHAP-Based Feature Contribution Analysis (Fig. 6-7)

For HashkaotBank, high feature values show a strong contribution toward class 0 predictions, with the SHAP value distribution centered around approximately -0.5. Low feature values show a strong contribution toward class 1 predictions, with the distribution centered around approximately 0.6. The contribution pattern is relatively symmetric between the two classes.

For Gil, high feature values exhibit a diverse contribution pattern toward class 0 predictions, with SHAP values distributed over a relatively wide and approximately uniform negative range. Medium and high feature values also show a moderate positive contribution toward class 1 predictions, with the main concentration of SHAP values around approximately 0.25, while the positive contribution is distributed across a range between 0 and 0.25.

For ShokelKniot, low feature values show a moderate contribution toward class 1 predictions, with SHAP values centered around approximately 0.2. The remaining feature values show a contribution toward class 0 predictions, with the SHAP value distribution centered around approximately -0.3.

For HeshbonotBazman, low feature values show a limited contribution toward class 0 predictions, with SHAP values concentrated around approximately 0.1. The remaining feature values exhibit a relatively uniform distribution over a wider range of SHAP values, with a contribution toward class 1 predictions centered around approximately 0.5.

For HashkaotShukHon, low feature values show a limited contribution toward class 1 predictions, with SHAP values centered around approximately 0.1. High feature values exhibit a relatively uniform distribution over a wider range of SHAP values, with a contribution toward class 0 predictions centered around approximately 0.4.

Overall, the SHAP analysis indicates that HashkaotBank is the most influential feature, showing the strongest and most consistent separation between the two classes.
For class 0 predictions, the main contributions are associated with higher values of HashkaotBank, ShokelKniot, and HashkaotShukHon, which show negative SHAP contributions.
For class 1 predictions, lower values of HashkaotBank, together with HeshbonotBazman, provide the main positive contributions.




