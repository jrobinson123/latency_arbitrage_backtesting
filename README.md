

### Background: 
This is a homework assignment for a student-run class I took at the University of California, Berkeley called Introduction to Quantitative Finance (Spring 2023 version). The student organization Traders@Berkeley runs the class, and all credit for the skeleton of the code goes to them. The assignment challenged us to design and implement a latency arbitrage strategy.

### The Problem: 
We were given two time-series datasets, one for the price of the perpetual Bitcoin (BTC) market, and one for the spot BTC market. These two markets are very closely correlated since they each derive their value from the same underlying asset. Additionally, the perpetual market typically leads the spot market, meaning an increase in price in the perpetual market will usually be followed by a rise in the spot market a short period later. Therefore, assuming we can access both streams of price data and can execute trades extremely fast, an arbitrage exists between buying the spot market after the perpetual spikes and selling the spot market after the perpetual dips. 

### The Simulation:
This codebase doesn't connect to any real exchanges or do any real trading. It is a simulation and backtest where I must create an array of buy and sell orders across timestamps to tell the program when to buy and sell spot BTC. Because this is a High-Frequency Trading (HFT) strategy, each position will be automatically exited after a hold of 80-time ticks (the tick size varies a bit in the data but is approximately 3 milliseconds). Based on the volume traded and the price change, the program will calculate the profit per trade and sum these to find the total profit. The simulation is oversimplified because there are no trading costs, market impact, or delays when executing orders. 

### Trading Strategy: 
The arbitrage will make money in two ways. The first way is buying the spot BTC when the perpetual spikes, as the spot BTC will then spike as well. Likewise, if we short the spot BTC when the perpetual crashes, then the spot will also crash shortly thereafter. Therefore a successful algorithm simply needs to create accurate buy and sell signals by detecting significant spikes and dips in the price of the perpetual BTC market. Here's a chart that displays the buy and sell signals I generated for a subset of the time series:
![](./arb_images/buy_and_sell_signals.png)

### My Algorithm for detecting spikes and dips
1) Calculate the 'spread' between the spot BTC and perpetual BTC market at each timestep.
2) Calculate a moving_average of this spread at each timestep with some window size.
3) Calculate a moving_standard_deviation at each timestep with some window size.
4) Calculate a buy and sell threshold, if the spread becomes abnormally large (spot significantly exceeds perpetual) that's a sell signal and a small or negative spread (perpetual exceeds spot) is a buy signal.
   
For some real numbers k, l,

sell_threshold = moving_average + (k * moving_standard_deviation)

buy_threshold = moving_average - (l * moving_standard_deviation) 

At each timestamp, I then decide whether to buy, sell, or do neither based on whether the price goes above the upper_threshold or below the lower_threshold. 
   
### Results
In the backtest my algorithm executed a high proportion of profitable short-term trades earning a simulated profit of $63,922 during the simulated period of one day of trading in early 2023. 

