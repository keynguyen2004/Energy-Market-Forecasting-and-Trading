# UK-energy-trading
### Project aim
Construct a forecasting model to predict auction price and implement trading strategy accordingly

### Dataset Documentation
The datasets that we will be using for this particular problem can be found on Kaggle at this [link](https://www.kaggle.com/datasets/kapilverma/uk-energy-trading/). It contains three .csv files (and a .txt file explanining the terminology), with a brief explanation of the datasets.

### Outline
This project essentially boils down to a time-series forecasting problem, where the aim is to predict auction price in UK energy market and maximize predicitve accuracy. The accuracy is measured by the deviations of the predicted values from the true values (i.e., minimizing standard error). From there, we will come up and backtest several trading strategies to maximize our rate of return.

### Data preprocessing
Before building a forecasting model, we need to 

1. *EDA*: Provide a good understanding of the underlying pattern and distribution of the data. It also allows us to check for errors, outliers, and missing values.
    1. The dataset contains outliers, especially for columns related to the price. However, we need to keep in mind that these are "recorded" price data and outlier/extreme values are not uncommon when it comes to eletricity prices
2. *Data cleaning*: The dataset contains missing values, to which we can either remove them or impute them. Since the nature of the missing values is continuous, we cannot simply remove them.
    1. Data imputation: We deployed a few of interpolation techniques including rolling median, rolling mean, and spline interpolation (of degree 3, 4, and 5), and selected the method with the least RMSE (i.e., the one the deviate least from the actual value) from test data to interpolate the missing values.
       
       ![image](https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/45f5cadf-d9bb-4a46-9c54-9ebebe68b91f)
       
    2. Remove multicollinearity: We use correlation analysis through the correlation matrix and variance inflation factor to determine the bivariate and multivariate collinearity, respectively. In that way, we can selected and remove the independent variables from our input to our model.
       <p float="left">
         <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/731738b4-ea36-43a7-9069-efc74227a523" width="360" heigh="360" />
         <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/8ff3d16a-e8ae-4382-8748-235955e8723c" width="360" heigh="360" />
       </p>
              

### Forecasting model
As we are dealing with time-series, sequential data, we are splitting the dataset based on a date. For this, we would like a 70-30 split, where 70% of the dataset is used for training and 30% used for testing.

![image](https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/4e78d592-7be9-4425-a963-21af7e1ee0a9)

To train our forecasting model, we have selected four machine learning models, two of which are linear models and the other two are tree-based models. The four selected models are:

1.	Ordinary least square regression: Generates a linear model with coefficients that seeks to minimize the residual sum of squares between the observed targets in the dataset and the predicted targets using the linear approximation.
2.	Partial least square regression: A technique for reducing the number of predictors by decreasing the variables used to predict. The predictors are then used in a regression analysis.
3.	Random forest regression: Integrates ensemble learning techniques with the decision tree and produces multiple randomized decision trees. Then, the model average outcomes of each individual tree to produce a collective result that would leads to robust predictions.
4.	eXtreme Gradient Boosting (XGBoost): Ensembles are built using decision tree models. One by one, trees are added to the ensemble and fitted to correct prior model prediction errors. This is an example of a boosting ensemble machine learning model at work. Any arbitrary differentiable loss function and the gradient descent optimization algorithm are used to fit models. This gives rise to the technique's name, "gradient boosting," because the loss gradient is minimized as the model is fit.


### Model evaluation metrics 
To evaluate the models performance for regression, (unlike classification problems) we cannot use accuracy score directly. Instead, we can employ some statistical measures to quantify the models performance; more specifically, we can assess the closeness of the model’s predictions relative to the true or actual value.

1.	Root mean square error (RMSE): Determines the average difference between the predicted and actual values of a model. It estimates how accurately the model predicts the target value (accuracy).
2.	R-Squared: Determines the proportion of variance in the dependent variable that can be explained/predicted by the independent variable.
3.	Adjusted R-Squared: As new features are added to the model, the R-Squared value either increases or remains constant. R-Squared does not penalize you for including useless features in your model. R-Squared is multiplied by the number of independent variables to correct this. Because it accounts for increasing predictors and only shows improvement if there is one, adjusted R-Squared is always lower than R-Squared.

### Result: Forecasting model

<p float="left">
    <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/cab92182-6c4b-41d6-9e00-e9a9d3ff972b" width="360" heigh="720" />
    <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/c71167d4-af8e-4993-9a31-e5a541df2ccb" width="360" heigh="720" />
</p>

<p float="left">
    <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/f365b6b3-7870-447e-b8af-4a9762347436" width="360" heigh="720" />
    <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/92dec962-3fb8-46cf-8e07-69986e896b55" width="360" heigh="720" />
</p>

There's a clear difference between the two linear models and the two tree-based models. The linear models root mean square error is lower in the test data than that in the train data, while the R-Squared and Adjusted R-Squared is greater in the test data than that in the train data. This implies that the linear models are able to generalize for the dataset and explain the variance in the second auction price better.

On the other hand, the tree-based model displayed higher mean square error in the test data than that in the train data, while the R-Squared and Adjusted R-Squared is less in the test data than that in the train data. This signal over-fitting, as the model perform extremely well for the train data but not comparable for test data.

One potential explanation is that tree-based models treat each row independently, so they will time series correlations (i.e., time series have an interdependence between observations). Therefore, when dealing with time series, the time-dependent structure is lost as the tree-based models assume the observations are independent.


### Trading strategy
To provide some context of the trading system, we need to be aware of the several conditions

1. There's a 5 GBP/MWh fee per trade (to account for tax and additional fee)
2. The bid is sequential: submit your bids for the first auction, wait for its results and afterwards submit your bids for the second auction
3. Net position: As a non-physical trader, our net position from the two auction trades should be zero for all timesteps, as we will not be able to provide this net position to the grid the following day. System prices will be used to settle the difference between our net market position after both auctions and the energy you can supply.

To elaborate on the net position and system prices, as a non-physical trader, our net position resulting from the trades on the two auctions should be zero, as we cannot provide this net position to the grid on the next day. The difference between our net market position after both auctions and the energy you can provide will be settled with the so-called **system prices**, which can be consider as selling to/buying from the government

1. When market participant has consumed more/generated less power, the participant need to buy the offsetting amount of power at the system price.
2. When market participant has consumed less/generated more power, the participant need to sell the offsetting amount of power at the system price.

Regarding the trading strategy, we can strategize using the first and second auction price as our basis, and then incorporate the system price determine if we can gain additional profit from it.

### Result

We start off with a basic strategy where if the forecasted price of the second auction outweights the combined price of the forecasted price of the first auction and the trading cost, we woukd buy in the first auction and sell in the second auction (and vice versa). Then, we use the system price to execute more trade where the bid is not accepted in the first auction. From there, we've developed a system that take advantage of the system price where you can settle an imbalance position using a lower/upper bound that trade.

**Bound**: The bound defined the range of price to which we can trade and leverage the system price

1. Lower bound: The price at which if the forecasted system price is below it, we can sell at the auction price and buy the imbalance at (expectedly higher) system price. $\text{Lower bound = max(forecasted 1st auction price, forecasted 2nd auction price) - Difference}$
2. Upper bound: The price at which if the forecasted system price is above it, we can buy at the auction price and sell the imbalance at (expectedly lower) system price. $\text{Upper bound = min(forecasted 1st auction price, forecasted 2nd auction price) + Difference}$

Where the difference is a pre-determined number that should at least cover the cost per trade (i.e., 5)

![image](https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/2f3c89f4-5047-4e97-9cc6-5fa4b01e18a8)

From the trading strategy, we can see that

1. **Rate of return**: Our rate of return has increase steadily as our bound increase, but it level off at around 14.6% when the bound reach 65.
2. **Non-trading activity**: As we expected, the higher the bound, the higher the percentage of non-trading activity. We can see, as we reach the end, we achieve a 90% non-trading activity. In other words, out of 24 bids per day, we would only execute 2.4 (so 2-3) bids on average, as we are being more selective and wanting to ensure the trade satisfy our bound
3. **Total profit**: Overall, there's a decreasing pattern of the total profit, which is understandable as our percentage of non-trading activity has increased, we are making less money from less trade

