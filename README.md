# UK-energy-trading
### Project aim
Construct a forecasting model to predict auction price and implement trading strategy accordingly

### Dataset Documentation
The datasets that we will be using for this particular problem can be found on Kaggle at this [link](https://www.kaggle.com/datasets/kapilverma/uk-energy-trading/). It contains three .csv files (and a .txt file explanining the terminology), with a brief explanation of the datasets.

### Outline
This project essentially boils down to a time-series forecasting problem, where the aim is to predict auction price in UK energy market and maximize predicitve accuracy. The accuracy is measured by the deviations of the predicted values from the true values (i.e., minimizing standard error). From there, we will come up and backtest several trading strategies to maximize our rate of return.

### Data preprocessing:
Before building a forecasting model, we need to 

1. *EDA*: Provide a good understanding of the underlying pattern and distribution of the data. It also allows us to check for errors, outliers, and missing values.
    1. The dataset contains outliers, especially for columns related to the price. However, we need to keep in mind that these are "recorded" price data and outlier/extreme values are not uncommon when it comes to eletricity prices
2. *Data cleaning*: The dataset contains missing values, to which we can either remove them or impute them. Since the nature of the missing values is continuous, we cannot simply remove them.
    1. Data imputation: We deployed a few of interpolation techniques including rolling median, rolling mean, and spline interpolation (of degree 3, 4, and 5), and selected the method with the least RMSE (i.e., the one the deviate least from the actual value) from test data to interpolate the missing values.
       
       ![image](https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/45f5cadf-d9bb-4a46-9c54-9ebebe68b91f)
       
    2. Remove multicollinearity: We use correlation analysis through the correlation matrix and variance inflation factor to determine the bivariate and multivariate collinearity, respectively. In that way, we can selected and remove the independent variables from our input to our model.
       <p float="left">
         <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/731738b4-ea36-43a7-9069-efc74227a523" width="500" heigh="500" />
         <img src="https://github.com/keynguyen2004/Energy-Market-Forecasting-and-Trading/assets/110079224/8ff3d16a-e8ae-4382-8748-235955e8723c" width="400" heigh="500" />
       </p>
              

### Forecasting model: (slide + model performance image)


### Trading strategy: (rule, constraints)


### Result + image of tradeoff
