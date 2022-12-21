# Momentum-in-US-Treasury-Bond-ETFs
In this notebook, we will analyse a pattern in treasury bond prices. This pattern produces a significant average returns which increases with maturity. Momentum can be attributed to an increase in investor demand for treasuries due to window dressing and portfolio rebalancing. 


### 1. Read the data
The close price data for the US Treasury Bond ETF is stored in the CSV file. To read a CSV file, we use read_csv method of pandas.

We will be using the following three ETFs

iShares 1-3 Year Treasury Bond ETF (SHY) - This ETF tracks the results of an index composed of U.S. Treasury bonds with remaining maturities between one and three years.

iShares 7-10 Year Treasury Bond ETF (IEF) - This ETF tracks the results of an index composed of U.S. Treasury bonds with remaining maturities between seven and ten years.

iShares 20+ Year Treasury Bond ETF - This ETF tracks the results of an index composed of U.S. Treasury bonds with remaining maturities greater than 20 years.

Then we will use the pct.change() function to retrieve the daily returns of the three assets.

### 2. Count days from end of month

The excess returns are present at the end of the month. We will find the last trading day of the month and mark it as Day 0, find second last day of the month and mark it as -1 and so on.

The logic for this is as follows.

1. Find the last day of the month: If the month of current trading date is different from month of the next trading date then it indicates that the current day is the last trading day. Then find the date of last trading day.
2. Count days from last day: Take the difference of current date and last day of month to get the day number from the last date of the month.

The following methods/properties are used.

1. shift: To shift the values in dataframe. Once the values are shifted by a day, the != operator is to compare current day's month with next day's month.

2. date.day: The day property on the date datatype is used to extract the date from it. For example, when day is called on '29-Jan-2020', it returns 29.

3. fillna: To backfill the missing values. This is use to populate the last trading date to all values in the month.

4. where: This is used for conditional assignment of values. If the date is last trading day then date value is extracted from the date index.

In the output, is_last_day for 2002-07-31 is set to True as it is the last trading of the month. Then value 31 is stored in last_date_of_month column. We take the difference of all days with last day of respective months to get the number of days from last date of the month.

### 3. Analyse the returns patterns
We will plot the average returns according to the days of the month. This will help in locating which days are the most profitable.

To plot the returns, we need to import matplotlib.pyplot library and use plot() function.

The treasury bond prices exhibit significant returns on the last days of the month. And if you check the net purchases by U.S. registered insurers, you can see a significant rise at the end of the month. Thus, there is a positive correlation between the net purchases of Treasuries by insurers and Treasuries returns at the End of the Month.

### 4. Percentage of days with positive returns
Significantly positive returns on a few days can skew the average returns. For example, abnormally huge returns for the two years out of 10 years can make the average returns more than 0. We have to make sure that the days are profitable on a consistent basis.

We can check this by counting the number of times the returns were positive or the percentage of time the returns were positive. The threshold for this value will be 50%. That is, you will only consider those days where the returns were positive more than half the time.

The logic is:

1. Group the data by last trading day of the month. That is, all day 0, day -1, and so on are grouped together.
2. Count the number of times the returns were positive.
3. Count total occurrences.
4. Divide the output of step 2 and 3, to get the percentage of times the returns are positive.

The following methods are used.

1. groupby: to group the values by days_since_last_day.
2. sum: to take the sum of values.
3. count: to count the number of values.

As we can see in the graph, during the end of month, the percentage of positive days are significantly above 50. In particular, the last six days are very prominent. The last day has the most number of days with positive returns.

Thus, we can now clearly see that the end of the month effect exists in treasuries. If we consider that treasuries are relatively less risky, then surely we should create a trading strategy around this effect.

### 5. Trading logic
We have seen that the last six days of the month give large returns. You will create the trading strategy to buy the treasuries 6 days before the end of the month.

We will use the numpy.where() to generate a trading signal when it is 6 days or less before the end of the month.

### 6. Strategy returns
Once we create the trading signal, now we calculate the returns only on those days when the trading signal returns a buy signal, in this case, 1.

To calculate strategy returns, multiply returns in data[inst_list] with data['signal']. Then, we use cumprod() function to calculate cumulative strategy returns.

### 7. Drawdowns
Drawdown can be defined as the percentage loss from the highest cumulative historical point.

The formula to calculate drawdown:

Drawdown = (cumulative returns/ running maximum) - 1

To calculate the running maximum, we can use maximum() and accumulate() functions

Thus, going long on the last 6 days of the month gives decent returns along with a low drawdowns in this strategy. Of course, changing factors such as the number of days or the ETFs could lead to significant changes in the maximum returns of this strategy.
