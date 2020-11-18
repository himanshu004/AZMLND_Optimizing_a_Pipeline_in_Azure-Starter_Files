# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary

The data is related with direct marketing campaigns of a Portuguese banking institution. The marketing campaigns were based on phone calls. Often, more than one contact to the same client was required, in order to access if the product (bank term deposit) would be ('yes') or not ('no') subscribed.

![alt text](https://github.com/himanshu004/AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/blob/master/images/automl-image.jpg)
<br>
The best performing model on the data using Azure's AutoML turned out to be VotingEnsemble classifier. It is based on the concept of ensembling machine learning that uses the idea of considering outputs from a variety of models, and then use them collectively to give a final result. With Azure's AutoML, we received an accuracy score of 0.9163 within just 5 iterations. 
<br>
![alt text](https://github.com/himanshu004/AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/blob/master/images/automl-metrics.jpg)
<br>
![alt text](https://github.com/himanshu004/AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/blob/master/images/automl-eval.jpg)

## Scikit-learn Pipeline

Steps involved in the entry script(train.py):
<ol>
<li>Creation of TabularDataset using TabularDatasetFactory.</li>
[Find dataset here](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)

<li>Cleaning the data - removing rows with missing entries, one hot encoding the categorical data, feature engineering etc.</li>
<li>Splitting the data into train and test sets.</li>
<li>Training the logistic regression model using arguments from the HyperDrive runs.</li>
 <li>Calculating the accuracy score.</li>
</ol>
<br>
Steps involved in the project notebook(udacity-project.ipynb):
<ol>
<li>Assigning a compute cluster to be used as the target.</li>
<li>Specifying the parameter sampler(RandomParameterSampling in this project).</li>
<li>Specifying an early termination policy(BanditPolicy in this project).</li>
<li>Creating a SKLearn estimator for use with train.py.</li>
<li>Creating a HyperDriveConfig using the estimator, hyperparameter sampler, and policy.</li>
<li>Submitting  the hyperdrive run to the experiment and showing run details with the widget.</li>
<li>Getting the best run id and saving the model from that run.</li>
<li>Saving the model under the workspace for deployment.</li>
</ol>
<br>

**Benefits of the parameter sampler:**
Choosing the right parameter sampling method is necessary as well a beneficial step to follow as it can have visible affects on your run time. Parameter sampling means to search the hyperparameter space defined for your model and select the best values of a particular hyperparameter. Azure supports three types of parameter sampling - Random sampling,Grid sampling and Bayesian sampling.
RandomParameterSampling supports discrete and continous hyperparameters. In random sampling, hyperparameter values are chosen randomly, thus saving a lot of computational efforts.It can also be used as a starting sampling method as we can use it to do an initial search and then continue with other sampling methods. 

**Benefits of the early stopping policy:**
An early termination policy is quite helpful when the run becomes exhaustive. It ensures that we don't keep running the experiment for too long and end up wasting resources and time, in order to find what the optimal parameter is. A run is cancelled when the criteria of a specified policy are met.
Examples of early termination policies we can use are - BanditPolicy, MedianStoppingPolicy and TruncationSelectionPolicy . 
In our project we have used BanditPolicy as the early termination policy.This early termination policy is based on the slack factor and delay evaluation. It basically checks the job assigned after every 'n' number of iterations(n is passed as an argument). If the primary metric falls out of the slack_factor, Azure ML terminates the job.

## AutoML
**Description of  the model and hyperparameters generated by AutoML:**
Since it was a classifiaction task, the primary metric that was to be maximized was set to 'accuracy'. We provided the cleaned version of the data, and set no. of cross-validations folds to 7;
This would prevent overfitting, if in case. The model was trained remotely on the compute cluster created in the beginning and number of iterations was set to a small value(5 in this project) as the experiment was bound to time out after 30 minutes.
The model that gave the best results turned out to be VotingEnsembleClassifier that takes the average of the predictions of the base models. It gave as an accuracy score of 0.9163 which was slightly better than the score achieved using HyperDrive.

## Pipeline comparison

**Comparison between the two models and their performance on the basis of the differences in accuracy, architecture:**
Though both the models used automated machine learning somehow, a difference in the accuracies was visible, with model trained using AutoML gave slightly better results. The AutoML model gave best accuracy of 0.9163(with VotingEnsembleClassifier), while the model built using SKLearn and HyperDrive gave a slightly lower score of 0.9074. The SKlearn model was iterated 25 times, while the AutoML model was iterated only 5 times(due to time limits). If the number of entries in the dataset is large, this could be a differentiating factor. Apart from that, an entry script was not a part of the architecture of the AutoML config.

**Reason of difference:**
The reason in accuracies might be due to the fact that that we used less number of iterations in AutoML run, which might give better results with more iterations. However, the difference was quite small.

## Future work
**Some areas of improvement for future experiments and why might these improvements help the model:**
Inferencing statistical insights from the data and feature engineering on it might be a scope of improvement. Also, improving the training data might give better results. We can also tune some other hyperparameters used in the model and use the pipelines suggested by the AutoML models in order to achieve better results in the future. Using different parameter sampling techniques and tuning the arguments of the BanditPolicy can also prove fruitful.  


