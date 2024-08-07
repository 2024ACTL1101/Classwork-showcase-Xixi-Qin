
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA # Corrected column name
amd_df$accumulated_shares <- 0 # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

# Algorithm loop
for (i in 1:nrow(amd_df)) {
  # Buy shares if it is the first day(initialized previous price = 0)
  # Buy shares if the current price is lower than the previous price
  if (previous_price == 0 | amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- "buy"
    # Multiply the share price by the negative of the share size
      # The negative indicates it is an expenditure
    amd_df$costs_proceeds[i] <- amd_df$close[i] * -share_size
    # Update both the accumulated shares variable and column
    amd_df$accumulated_shares[i] <- accumulated_shares + share_size
    accumulated_shares <- accumulated_shares + share_size
    # Restrict the "previous_price == 0" condition to only work once(i.e. for i=1)
    previous_price <- amd_df$close[i] # The share price will never be 0
  } else {
    # Update the accumulated shares column and previous price for all other cases
    amd_df$accumulated_shares[i] <- accumulated_shares
    previous_price <- amd_df$close[i]
  }
  # Sell all shares on the last day
  amd_df$trade_type[nrow(amd_df)] <- "sell"
  amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_df)]
  amd_df$accumulated_shares[nrow(amd_df)] <- 0
}
```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
# Define the end points of a trading period within 2021
trade_start_date <- as.Date("2021-01-01")
trade_end_date <- as.Date("2021-12-31")

# Filter the dataframe to only include rows within the custom trading period
amd_df <- amd_df[amd_df$date >= trade_start_date & amd_df$date <= trade_end_date ,]
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
## First run the Algorithm in Step 2
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA # Corrected column name
amd_df$accumulated_shares <- 0 # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

# Algorithm loop
for (i in 1:nrow(amd_df)) {
  # Buy shares if it is the first day(initialized previous price = 0)
  # Buy shares if the current price is lower than the previous price
  if (previous_price == 0 | amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- "buy"
    # Multiply the share price by the negative of the share size
      # The negative indicates it is an expenditure
    amd_df$costs_proceeds[i] <- amd_df$close[i] * -share_size
    # Update both the accumulated shares variable and column
    amd_df$accumulated_shares[i] <- accumulated_shares + share_size
    accumulated_shares <- accumulated_shares + share_size
    # Restrict the "previous_price == 0" condition to only work once(i.e. for i=1)
    previous_price <- amd_df$close[i] # The share price will never be 0
  } else {
    # Update the accumulated shares column and previous price for all other cases
    amd_df$accumulated_shares[i] <- accumulated_shares
    previous_price <- amd_df$close[i]
  }
  # Sell all shares on the last day
  amd_df$trade_type[nrow(amd_df)] <- "sell"
  amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_df)]
  amd_df$accumulated_shares[nrow(amd_df)] <- 0
}

# Calculate total profit/loss within the period
total_profit_or_loss <- sum(amd_df$costs_proceeds, na.rm=TRUE)

# Initialize the invested capital to 0
invested_capital <- 0
for (i in 1:nrow(amd_df)) {
  # Filter out rows where the trade type is NA
  if (is.na(amd_df$trade_type[i]) == FALSE) {
    # Invested capital is the negative sum of all ‘buy’ transactions
    if (amd_df$trade_type[i] == "buy") {
      invested_capital <- invested_capital - amd_df$costs_proceeds[i]
    }
  }
}
# Use the Return on Investment(ROI) formula
ROI <- total_profit_or_loss / invested_capital * 100

# Create a function that prints the two calcaulted values
step_4_solution <- function(total_profit_or_loss, ROI){cat("Total profit/loss =", total_profit_or_loss, ": ROI =", ROI)}

# Print out a solution stating both values
step_4_solution(total_profit_or_loss, ROI)
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
# The custom trading period has already been filtered in Step 3

# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA
amd_df$accumulated_shares <- 0

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
# Create new variables for the average purchase price and percentage threshold to sell
number_of_buys <- 0 # Records the number of previous purchases to help find the average
                    # purchase price
average_purchase_price <- 0 # Stores the average purchase price for all previous dates
                            # within the dataframe
threshold_to_sell <- 0.90 # 90% percentage threshold before shares are sold(falls by 10%)

# Algorithm loop
for (i in 1:nrow(amd_df)) {
  # Buy shares if it is the first day(initialized previous price = 0)
  # Buy shares if the current price is lower than the previous price
  if (previous_price == 0) {
    amd_df$trade_type[i] <- "buy"
    # Multiply the share price by the negative of the share size
      # The negative indicates it is an expenditure
    amd_df$costs_proceeds[i] <- amd_df$close[i] * -share_size
    # Find the new average purchase price that includes row i's value
    average_purchase_price <- (average_purchase_price * number_of_buys
                              + amd_df$close[i]) / (number_of_buys + 1)
    # Update the number of purchases
    number_of_buys <- number_of_buys + 1
    # Update both the accumulated shares variable and column
    amd_df$accumulated_shares[i] <- accumulated_shares + share_size
    accumulated_shares <- accumulated_shares + share_size
    # Restrict the "previous_price == 0" condition to only work once(i.e. for i=1)
    previous_price <- amd_df$close[i] # The share price will never be 0
    # Sell half of the holdings if the price is less than 90% of the current average
    # purchase price
  } else if (amd_df$close[i] <= average_purchase_price * threshold_to_sell){
    amd_df$trade_type[i] <- "sell"
    # The floor() command rounds down to ensure shares are always sold as whole numbers
    amd_df$costs_proceeds[i] <- amd_df$close[i] * floor(accumulated_shares / 2)
    # The ceiling() command rounds up which ensures that the sum of shares sold and
    # retained is equal to the original number of accumulated shares
    amd_df$accumulated_shares[i] <- ceiling(accumulated_shares / 2)
    accumulated_shares <- ceiling(accumulated_shares / 2)
    previous_price <- amd_df$close[i]
    # Buy shares if the price is higher than 90% of the average purchase price but still
    # lower than that of the previous day
  } else if(amd_df$close[i] < previous_price) {
    amd_df$trade_type[i] <- "buy"
    # Multiply the share price by the negative of the share size
      # The negative indicates it is an expenditure
    amd_df$costs_proceeds[i] <- amd_df$close[i] * -share_size
    # Find the new average purchase price that includes row i's value
    average_purchase_price <- (average_purchase_price * number_of_buys
                                + amd_df$close[i]) / (number_of_buys + 1)
    # Update the number of purchases
    number_of_buys <- number_of_buys + 1
    # Update both the accumulated shares variable and column
    amd_df$accumulated_shares[i] <- accumulated_shares + share_size
    accumulated_shares <- accumulated_shares + share_size
    previous_price <- amd_df$close[i]
  } else {
    amd_df$accumulated_shares[i] <- accumulated_shares
    previous_price <- amd_df$close[i]
  }
  # Sell all shares on the last day
  amd_df$trade_type[nrow(amd_df)] <- "sell"
  amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_df)]
  amd_df$accumulated_shares[nrow(amd_df)] <- 0
}

# Calculate total profit/loss within the period for the new algorithm
new_total_profit_or_loss <- sum(amd_df$costs_proceeds, na.rm=TRUE)

# Initialize the new invested capital to 0
new_invested_capital <- 0
for (i in 1:nrow(amd_df)) {
  # Filter out rows where the trade type is NA
  if (is.na(amd_df$trade_type[i]) == FALSE) {
    # Invested capital is the negative sum of all ‘buy’ transactions
    if (amd_df$trade_type[i] == "buy") {
      new_invested_capital <- new_invested_capital - amd_df$costs_proceeds[i]
    }
  }
}
# Use the Return on Investment(ROI) formula
new_ROI <- new_total_profit_or_loss / new_invested_capital * 100

# Create a function that prints the two calcaulted values
step_5_solution <- function(new_total_profit_or_loss, new_ROI)
                    {cat("New total profit/loss =", new_total_profit_or_loss,
                          ": New ROI =", new_ROI)}
step_5_solution(new_total_profit_or_loss, new_ROI)
```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
# Use the two prior functions to print the original and new total P/L and ROI
step_4_solution(total_profit_or_loss, ROI)
step_5_solution(new_total_profit_or_loss, new_ROI)
```

Discussion: Both the profit/loss and ROI worsened in my chosen period. The total profit/loss decreased by 2.6 times from 553148.9 to 211621.3. The ROI faced a similar effect, where it was decreased by 2.4 times from 44.4082 to 18.84097. These results indicate that my second strategy was not as efficient as the first one. This can be explained through considering the relevant market events which effected share prices within my chosen period of 2021.

On Tuesday, October 27, 2020, AMD announced that they have entered into a definitive agreement to acquire Xilinx in an all-stock transaction valued at $35 billion. Xilinx was an American technology and semiconductor company, acting as a specialist producer of FPGAs (field-programmable gate arrays), which are particularly useful for telecommunications applications, a sector elevated by the development of 5G across the world.

AMD CEO Dr. Lisa Su commented, “Our acquisition of Xilinx marks the next leg in our journey to establish AMD as the industry’s high performance computing leader and partner of choice for the largest and most important technology companies in the world”.

Post-closing, current AMD stockholders will own approximately 74% of the combined company on a fully diluted basis. This annoucemented undoubtable increased overall investor confidence, as evident by the rapid increase in share price throughout the 2021 period. This resulted in the price of the shares being significantly higher at the end of the 2021 period when compared to their initial prices.

The second strategy implements a “stop-loss mechanism” strategy, which aims to sell shares after a significant decrease in share price(10% for my strategy) in order to reduce the overall loss of profit, assuming that the price will continue to fall. This strategy is not suitable for the 2021 time period as the price only falls for a short period of time, while overall, the share price is increasing. This means that more profit would be generated by selling at the end of the period, as opposed to when the stock price has just fallen.

This is demonstrated by the Return on Investment of the second strategy being less than two times smaller than that of the first. This is because, while the first invested capital was only 11% more than that for the second strategy, the first total profit is around 2.6 times that of the second total profit. This large difference greatly skews the new ratio between the two values, which is represented by the ROI.

For my custom time period, it is evident that the first strategy would be more suitable and effective, providing a better ROI and total profit.



