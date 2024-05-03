
# Sunlight Ski & Bike Product Classification

Sunlight Ski and Bike, a small store in Glenwood Springs, relies on sales representatives' suggestions for seasonal orders, leading to inefficient inventory management and buying due to:

* Lack of understanding of product inventory and demand.
* Unclear product categories: prioritizing or eliminating categories.
* Difficulty identifying high-demand, high-value items.
* Missed opportunities for cross-selling.

This results in:

* Stockouts for in-demand products.
* Overstocking on slow-moving items.
* Lost revenue and customer satisfaction.

## Objectives
After exploring the historic sales and product data, it was decided that the "category" of the products needed some cleaning up in order to get better reporting on which items, colors, sizes sell the best, by which prices, discounts etc. Therefore in order to gain an understanding of how to optimize product inventory and sales to maximize profitability and customer satisfaction, the categories (like skis, bikes, bike parts, etc) need to be reclassified, reviewed, and fixed. Once, the categories and parent categories are correctly classified then we can look at questions like: Specific questions:

- Which products sell the best and the quickest? Which brands?
- Which products, sizes, colors sells the most with no discounts, or full MSRP price?



## 1. Data

n reviewing the data, there were 11 columns and 121,355 rows that I decided would be useful. For the time being, I dropped rows that had "nan" as a category as I believe that data is either old or messy. I will be focusing on the 121,355 product that have categories for this project and later I can look at the uncatetgorized products once I have a category classification scheme up and running.

There are a number of missing category values for the products, which makes it hard to run reports and see which categories and types of products sell the best at the store. In order to update the categories to a new schema that will make reporting much more straightforward, it is necessary to view the pattern in the missing category data, as well as the accuracy in the category classification. Are the products in the correct category? And can we fix it with automatic classification. Let's find out!


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

Further down the road, we can look at:

- Which factors influence sales patterns (e.g., weather, seasonality, promotions, external events)?
- How can we accurately predict future demand for specific products or categories?
- How can we personalize product recommendations to individual customers?




