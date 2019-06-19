---
title:  "Stock price predictor"
header:
  teaser: "/assets/images/STOCKS/stockspredicting.JPG"
tags:
  - Stocks
  - Machine Learning
  - Python
  - RNN
  - LSTM
---

# Introduction
My ambitious about knowing more about stock investing started a year ago, first of all I bought my first stock related book: “Stock investing for dummies” by Paul Mladjenovic.
As soon as I was getting the technical knowledge and seeing the important factors that we should have before investing on a company I thought that maybe there was some technical or software that would be helpful, or be able to “predict” the future.

As I already had the knowledge about programming and Machine learning was getting more and more popular, I decided to start learning it by myself and look at me, I am already writing my first post about a stock predictor (and it will not be the last one).

This is the first step for my personal project and I know that this is really basic and I need to adquire tons of knowledge, however, during the way to arrive here I noticed that I want also to work in this sector and be able to change from software development to Machine learning.

I strongly recommend reading this blog before reading mine: ["LSTM and RNN explained"](https://colah.github.io/posts/2015-08-Understanding-LSTMs/) which will give us a brief explanation about RNN networks and specially about the one we are going to use, LSTM (Long Short Term Memory networks)

## Summary
Using RNN we will predict the stock price of Google from 1st January 2017 to 31st January 2017 using 5 years to train the model (from January 2012 to December 2016) **only** using the opening price data from the stock.

## Time for coding

First of all, using the sklearn library we will scale our data to be between 0-1 and fit it.

```python
from sklearn.preprocessing import MinMaxScaler
scale = MinMaxScaler(feature_range = (0, 1))
training_set_scaled = scale.fit_transform(training_set)
```

once we have our data scaled, we will predict every value using the previous 60 ones, for that, we will need to use a for loop that will start in the 60th value because as I said it will need the previous 60 to be predicted.

```python
X_train = []
y_train = []
for i in range(60, 1258):
    X_train.append(training_set_scaled[i-60:i, 0])
    y_train.append(training_set_scaled[i, 0])
#We Will make it as an array because it will be used as a future input.
X_train, y_train = np.array(X_train), np.array(y_train)
```


For this purpose we will use keras library, but a future improvement would be use more powerful libraries such as scikit-learn.

```python
# Importing the Keras libraries and packages
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import LSTM
from keras.layers import Dropout
```
after this, we will initialize the RNN and add  4 layers with 20% dropout to prevent overfitting.

```python
# Initialising the RNN
regressor = Sequential()

#Creating first LSTM layer giving the amount of units and the dimensions
regressor.add(LSTM(units = 50, return_sequences = True, input_shape = (X_train.shape[1], 1)))
regressor.add(Dropout(0.2))

#Adding a second LSTM layer and 20% Dropout
regressor.add(LSTM(units = 50, return_sequences = True))
regressor.add(Dropout(0.2))

#Adding a third LSTM layer and 20% Dropout
regressor.add(LSTM(units = 50, return_sequences = True))
regressor.add(Dropout(0.2))

#Adding a fourth LSTM layer and 20% Dropout
regressor.add(LSTM(units = 50, return_sequences = False))
regressor.add(Dropout(0.2))

# Adding the output layer (it is one because we are predicting a value)
regressor.add(Dense(units = 1))
```

Finally we will add an Adam optimizer that usually works really good in RNN and will choose Less mean square because we are facing a regressor problem.

```python
regressor.compile(optimizer = 'adam', loss = 'mean_squared_error')
# Fitting the RNN to the Training set 
regressor.fit(X_train, y_train, epochs = 100, batch_size = 32 )
```

Finally, once the model is trained we will work with the test set having as a result:

![stock1](/assets/images/STOCKS/stockspredicting.JPG){:class="img-responsive center-image"}

As we can see, there is a small delay between the Real and the predicted one, this is because it is almost impossible to predict the stock price with a 100% accuracy, and like we are using the previous values to predict others, we will always have a small delay.

However, we can see that is quite accurate the wave so we could use this to “predict” the future and have an idea if we should buy or sell stocks, but keep in mind, that in stocks the future is not related with the past, so you should use this just as a guide, not as a golden rule.

## Future Implementations

-	Take more data for test training purposes, More data = More accurate.
-	We have been using only the opening price for the stock, we can use more data like max price, min price, Beta (stock’s volatility)

