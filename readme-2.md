
# International Rock Climbing Recommendation System

*The sport of rock climbing has been steadily increasing in popularity. From 2012-2017, the IBISWorld estimates that from average annual growth for the indoor climbing wall industry was [3.9% in the USA](https://www.ibisworld.com/industry-trends/specialized-market-research-reports/consumer-goods-services/sports-recreation/indoor-climbing-walls.html).  In 2015, it ranked 17th out of 111 out of the most popular sports in the United States ( Physical Activity Council and PHIT America). Yet, even with this growth in popularity, most of the international rock climbing websites still lack a rock climbing recommendation system. In this project, I will create a recommendation system for the 8a.nu website that will help climbers identify some unique international climbing objectives.*

## 1. Data

8a.nu is one the world’s largest international rock climbing websites. With over 4 million entries of climbs and ratings, this Kaggle webscraping project is a sufficient size to develop a good predictor model. To view the 8a.nu website, the original Kaggle four SQLite tables created by David Cohen, or the import report using the Kaggle API click on the links below:

> * [Product Data](https://www.8a.nu/)

> * [Sales Data](https://www.kaggle.com/dcohen21/8anu-climbing-logbook)

## 2. Method


1. **Bag of Words (BoW)**
Bag of Words (BoW) is a simple and effective method for text classification. In this approach, each document (product description) is represented as a vector where each element corresponds to the frequency of a word in the document. The BoW model disregards the order of words in the document but captures the presence or absence of words.

2. **TF-IDF (Term Frequency-Inverse Document Frequency)**
TF-IDF is an extension of the Bag of Words approach that takes into account the importance of words in a document relative to the entire corpus. It calculates a weight for each word based on its frequency in the document (term frequency) and its rarity across all documents (inverse document frequency). TF-IDF assigns higher weights to words that are frequent in a document but rare in the entire corpus, making it more discriminative for classification tasks.

3. **Word Embeddings (e.g., Word2Vec, GloVe)**
Word embeddings are dense vector representations of words in a high-dimensional space, learned from large text corpora. These embeddings capture semantic relationships between words and can be leveraged for text classification tasks. Word embedding models like Word2Vec and GloVe can convert words into continuous vector representations, which can then be used as features for classification algorithms.

4. **Doc2Vec**
Doc2Vec is an extension of Word2Vec that learns vector representations for entire documents (product descriptions) instead of just words. It represents each document as a fixed-length vector, capturing the semantic meaning of the entire document. Doc2Vec embeddings can be used as features for classification algorithms similar to word embeddings.

5. **TextCategorizer (e.g., spaCy)**
TextCategorizer is a text classification model provided by the spaCy library. It uses deep learning techniques to classify text into predefined categories or labels. TextCategorizer can be trained on a labeled dataset of product descriptions and their corresponding categories to learn patterns and make predictions on new data.

6. **Machine Learning Classification Algorithms**
Traditional machine learning classification algorithms such as Logistic Regression, Random Forest, Support Vector Machines (SVM), and Naive Bayes can also be used for text classification tasks. These algorithms can be trained on feature representations derived from text data (e.g., BoW, TF-IDF, word embeddings) to predict the category of a product based on its description.


![](./6_README_files/matrix_example.png)


**WINNER: ** 

## 3. Data Cleaning 

[Data Cleaning Report](....)


**Data Cleaning Issues**
- Size was inconsistent. Has letters, numbers, weights, etc
- 



**Preprocessing Steps:**
Standardization for numerical features:

The StandardScaler scales numerical features to have a mean of 0 and a standard deviation of 1, ensuring that all features contribute equally to the model.
Imputation and One-Hot Encoding for categorical features:

The SimpleImputer replaces missing values in categorical features with a constant value ("missing").
The OneHotEncoder converts categorical features into binary vectors, creating dummy variables for each category. The parameter handle_unknown='ignore' handles unknown categories gracefully during encoding.
Column Transformer:

The ColumnTransformer applies different transformations to different columns of the dataset.
It uses the defined preprocessing steps for numerical and categorical features separately.
Pipeline Definition and Fitting:
The "pipeline" variable is defined when the model pipeline is constructed using Pipeline(steps=[...]).
The pipeline consists of two main steps:
Preprocessing: This step applies the defined preprocessing transformations to the data.
Classifier: This step defines the classifier (Random Forest) used for prediction.
The fit method is called on the pipeline to train the model using the training data (X_train and y_train).
Once the pipeline is fitted, it encapsulates the entire workflow from preprocessing to model training, making it easy to apply the same transformations and predictions consistently.






* **Problem 3:** Spelling issues with the route name. For example: if there was a route named "red rocks canyon" it could be spelled "red rock", "red rocks", "red canyon" etc. **Solution:** at first, I was hopeful and tried two different phonetic spelling algorithms (soundex & double metahpone). However, both of these proved to be too aggressive in their grouping and sometimes would group together up to 20 different individual routes as the same item! My final solution was to create an accurate filter for route names. The logic being that if up to x number of users all entered that *exact same* route name, the chances were good that it was an actual route spelled correctly. I played around with 4 different filters and kept these until I could test their prediction accuracy in the ML portion. I found the greatest prediction accuracy came from the dataset that filtered out any routes listed less than 6 times.

## 4. EDA

[EDA Report](https://colab.research.google.com/drive/14AKVsyXy7yJSxBjmEBFyz7kEX7e9ioM_)

* The star-rating distributions all checked out to be normal. It is very common with explicit ratings to see a diminished number of low ratings.

![](./6_README_files/star_counts.png)

## 5. Algorithms & Machine Learning

[ML Notebook](https://colab.research.google.com/drive/1kAlvwwJnGcdCAJD8oFokT3gtJF2UnyZP)

I chose to work with the Python [surprise library scikit](http://surpriselib.com/) for training my recommendation system. I tested all four different filtered datasets on the 11 different algorithms provided, and every time the Single Value Decomposition++ (SVD++) algorithm performed the best. It should be noted that this algorithm, although the most accurate is also the most computationally expensive, and that should be taken into account if this were to go into production.

![](./6_README_files/algo.png)

>***NOTE:** I choose RMSE as the accuracy metric over mean absolute error(MAE) because the errors are squared before they are averaged which gives the RMSE a higher weight to large errors. Thus, the RMSE is useful when large errors are undesirable. The smaller the RMSE, the more accurate the prediction because the RMSE takes the square root of the residual errors of the line of best fit.*

**WINNER: SVD++ Algorithm**

This algorithm is an improved version of the SVD algorithm that Simon Funk popularized in the million dollar Netflix competition that also takes into account implicit ratings (*yj*). Using stochastic gradient descent (SGD), parameters are learned using the regularized squared error objective.

![](./6_README_files/forumla.png)

## 6. Which Dataset to choose?

[More details about this process...](https://colab.research.google.com/drive/1kAlvwwJnGcdCAJD8oFokT3gtJF2UnyZP)

After choosing the SVD++ algorithm, I tested the accuracy of all four different filtered datasets. The dataset which filtered out any route names occurring less than 6 times performed the most accurate predictions. Thus, it was chosen to be the dataset I trained on.

>* All of the dataframes displayed discrepancies with the 1 star ratings(This is to be expected due to the inherent skewed positive ratings). Also, the one star ratings are not imperative to this project's goal. It is more important that the 1 star ratings are different enough to be filtered out of the top ten routes recommended to users. 
>* Notice the 3-star rating has a fat bulge at the top of the "violin" which indicates its predicting 3-star ratings for some of the true 3-star routes. This was not as prominent in the other dataframes
>* The 1-star rating also has a fatter tail than the other datasets displayed


![](./6_README_files/accuracy.png)

## 7. Coldstart Threshold
[More details about this process...](https://colab.research.google.com/drive/1kAlvwwJnGcdCAJD8oFokT3gtJF2UnyZP)

**Coldstart Threshold**: There is a problem when only using collaborative based filtering: *what to recommend to new users with very little or no prior data?* Remember, we already set our cold start threshold for the routes by choosing the dataset that filtered out any route occurring less than 6 times. Now, let investigate where to put the threshold for users.

![](./6_README_files/20user_thresh.png)

*It is my hypothesis that the initial filtering of the routes is what affected the RMSE of the users* 

>* Increasing the user threshold to 5 would increase the RMSE by .005 & would lose approximately 40% of the data.
>* Increasing the user threshold to 13 would increase the RMSE by .0075 & would lose approximately 60% of the data
>* If there were a larger increase in the RMSE (>= .01) I would trade my users' data for this improvement. However, these improvements are too minuscule to give up 40%-60% of my data to train on. Instead, I voted to keep some of these outliers to help the model train, and will focus on fine tuning my parameters using gridsearch to improve the RMSE


## 7. Predictions

[Final Predictions Notebook](https://colab.research.google.com/drive/1vLkoW_4SYessy_igmJxlVz_jEPlgJ06v)

In the final predictions notebook, the user can enter their user_id number and receive a list of top ten routes recommended to them:

![](./6_README_files/predictions.png)

## 8. Future Improvements

* In the future, I would love to spend more time creating a filtering system, wherein a climber could filter out the type, difficulty of climb, & country before receiving their top ten recommendation

* This recommendation system could also be improved by connecting to the 8a.nu website so that the user could input their actual online ID instead of just their user_id number 

* Due to RAM constraints on google colab, I had to train a 65% sample of the original 6x dataset. Without resource limitations, I would love to train on the full dataset. Preliminary tests showed that the bigger the training size, the lower the RMSE. One test showed an increase in sample size could increase the RMSE by .03 (in contrast to the .005 improvement I received when increasing the coldstart threshold)

## 9. Credits

Thanks to Nicolas Hug for his superb surprise library scikit, Colin Brochard for his stellar advice from his Mountain Project recommendation system, and DJ Sarkar for being an amazing Springboard mentor.




