# Helsinki City Bikes

## **About**

This repository contains the code necessary to scrape, format and anlayze the publicly available data on Helsinki City Bike network.

---
![cover](images/image.jpeg)
## **What are Helsinki City bikes?**

- "Helsinki City Bikes" is a public bicycle system in Helsinki and Espoo and integrating with the rest of the public transport in Helsinki since May 2016. Its aim is to solve the so called [last mile problem](https://en.wikipedia.org/wiki/Last_mile_(transportation)).

- "Helsinki City Bikes" is operated as a public-private partnership between Helsinki Regional Transport Authority (HSL), Helsinki City Transport (HKL), Espoo Technical and Environment Services, Moventia and Smoove.

- City bikes are shared bicycles available to everyone in Helsinki or Espoo. You can purchase access to city bikes for a day, week or the entire cycling season. All passes include an unlimited number of 30-minute bike rides. For a small extra fee, you can use the bike for longer, five hours at most. After use, the bike is returned to a bike station. You can register as a city bike user on the HSL city bikes webpage. A day pass user can get access to 1–4 bikes; other passes only include one bike.

- There are 3,510 bikes in season 2020. Helsinki has 241 and Espoo 110 bike stations.

## **The Dataset**
**Scraped and formatted dataset can be found here: [Link to Kaggle](https://www.kaggle.com/geometrein/helsinki-city-bikes)**

---
## **Structure**
| departure           | return              | departure_id | departure_name  | return_id | return_name    | distance (m) | duration (sec.) | avg_speed (km/h)   | departure_coordinates                   | return_coordinates       | Air temperature (degC) |
|---------------------|---------------------|--------------|-----------------|-----------|----------------|--------------|-----------------|--------------------|-----------------------------------------|--------------------------|------------------------|
| 2020-03-23 07:43:51 | 2020-03-23 07:52:50 | 207          | Vanha Viertotie | 90.0      | Paciuksenkaari | 2409.0       | 534.0           | 0.2706741573033708 | (60.21107529627341, 24.881660254358646) | (60.1944475, 24.8911179) | 1.1                    |

---


## **Modelling**
- In the part of the notebook we are trying to use the before computed centrality measures to predict the number of trips and thus, the number of bikes that have been used throughout the year 2020, in each node.

- Firstly after creating the dataset with four centrality measurers and number of trips for each node, we verify either if there's multicollinerity between them or not. What we find is that as we could expect that there is some multicollinearity between the variables besides the Betweenness centrality that is apparentely uncorrelated with the others centrality measurers.

- We decide after some visualization needed to see the type of relationships between indipendent and the dependent variables to deploy a simple linear model that at first glance doesn't seem to perform very well with the best value of the  𝑅2  = 0.41 with the Closeness Centrality as our X.
Using the same X variable we use a Polynomial regression with a gridsearch to look for the best degree that results being the third, giving us the results of an  𝑅2  = 0.51
Although some collinearity was detected from our VIF formula, we decided to perform a multiple linear regression with a K fold cross validation with grid search to detect the best number and combination of features. The algorithm endend up with the Betweenness and Closeness centrality as being the indipendent variables that resulted a  𝑅2  = 0.44

- Having only a 0.51  𝑅2  achived by the third polynomial regression, although acnkowledgning our limit given the scaristy of data points with which we needed to train and test our models with, we decided to deploy more complex model that could explain better non linear relationships between our Y and the other variables. Starting with a regression tree we just obtained an  𝑅2  = 0.19.
Our second try was to deploy a Support Vector Regression with a grid search to select the best combination of hyperparameter that ended up being {'C': 5000, 'epsilon': 0.2, 'gamma': 1e-28 } with a linear kernel, that reached a  𝑅2  of 35.

- Given the low performance of our regression tree compared to the SVM's and the regression's one we implement a random forest with gridsearch in which we used 100 estimators. Eventually the model led us to reach an  𝑅2  = 35%.

- Our last model was a simple Neural Network with 4 dense fully connected layer with ReLu as activation function and the last one having a linear one as our task was to perform a regression. The input features dimension is 3 as we used the same two variables used in the Multiple Regression plus the eigenvector centrality trying to include also the importance of the connections a node has; this improved our accuracy by almost 7%. The optimizer chosen is Adam with MAE and ME as metrics. With this features and after the fine tuning of our model we reach the best results which is actually equal to the best we already obtained with the third polynomial degree regression, a  𝑅2  = 0.51.
---