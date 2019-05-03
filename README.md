# Ensemble Learning Using LSTM and WaveNet
# Overview
[Quick, Draw! Doodle Recognition Challenge] is a Kaggle competition started in September 2018. [Quick, Draw!] is an AI experiment playground to learn how neural network works. You draw an image and the neural network predicts while you are drawing. The image should belong to one the specified categories.

# Problem Statement
The goal here is to train a model to outperform the existing neural network in order to predict the category of image while drawing. In order to accomplish this goal, ensemble approach is suggested. An overview to this solution is:

  - Selecting multiple neural network classifiers for stroke-based input data
  - Preparing the data to be applied as input to the neural networks
  - Designing and training the selected classifiers individually
  - Testing the individual classifiers and specifying their confidence
  - Designing an ensemble algorithm based on the weighted outputs of individual classifiers using confidence
  - Testing the suggested ensemble algorithm

# Data
The dataset includes around 1 billion drawings from 340 categories of objects captured from users’ experience. Each drawing includes time-stamped vectors and metadata saying what the user was asked to draw as well as the user’s country. All of the drawings belonging to a category is saved as a CSV file. There is a word tagged to each CSV file representing that category label. The label may be actually more than one word, with space between them.
Kaggle has provided two versions of the data, raw and simplified. The raw sample shows exactly what user drew. The simplified sample is the trimmed version of the raw sample in which the redundant information of the drawing has been removed. To decrease the training time, a part of the simplified version are used for training and testing.
The data can be found on [Quick, Draw! Doodle Recognition Challenge] website.

# Implementation
Here, a confidence-based ensemble learning is proposed which uses two individual classifiers including [WaveNet] and [LSTM] which have different methodologies for classification. The steps of the confidence-based algorithm is as follow:

  - During the training, WaveNet and LSTM are trained. The size of of training, validation and testing datasets are equal for both classifiers. The testing step of training WaveNet and LSTM is served as cross-validation step for the ensemble algorithm.
  - During the cross-validation, the overall confidence of each algorithm is calculated over all possible classes. This value is computed as the accuracy of the classifier to classify the cross validation dataset. Since the data is balanced, this metric gives a reliable insight of the classifiers’ performance. In addition to the overall confidence, the confidence of classifier for each category of drawing is computed.
  - What we call confidence here is the probability of categories in the output layer which is calculated using softmax function.
  - For a new sample, the classification is initialized with running both classifiers. The cate- gory of the new drawing is specified and the confidence is outputted.
  - The weighted confidence of each classifier is calculated using the following equation:
  
  *newC_sample = C_sample ∗ C_overall ∗ C_category*
  
where **newC_sample** is the weighted confidence of the classifier for the predicted category, whereas **C_sample** denotes the raw value of confidence for the predicted category by the classifier. **C_overall** represents the overall confidence of that classifier calculated over the cross validation and **C_category** shows the general strength of that classifier for predicted category of drawing which was computed during cross validation.
  - When the weighted confidence is calculated for both WaveNet and LSTM, that classifier which maximizes the newCsample is found using following equation. The corresponding category of the winner is the final output.
  
  *C = max{newC_sample:wavenet, newC_sample:lstm}*
 

   [Quick, Draw!]: <https://quickdraw.withgoogle.com/>
   [Quick, Draw! Doodle Recognition Challenge]: <https://www.kaggle.com/c/quickdraw-doodle-recognition#description>
   [WaveNet]: <https://arxiv.org/abs/1609.03499>
   [LSTM]: <https://www.tensorflow.org/tutorials/sequences/recurrent_quickdraw>
   
