# Market Making Algorithm

## Description
This GitHub lays out the work done to develope an algorithmic trading model that ranked 3rd place in the Optiver trading competition at Imperial College London.

This `readme` is not an exhaustive explanation of the code, which is in total more than 400 lines, but serves to give an understanding of the strategy taken, as well as a brief description of the important functions.

## Strategy
The model has the capacity to take active arbitrage oportunities, but focuses on a passive strategy and maintains itself market neutral - which is the core concept of Market Making that we had to exemplify in the competition.

The idea is to estimate correctly, usign a combination of weighted averages from the current bids and aks, the price of the underlying stock. From that, we us the Black Sholes Equation to calculate the price of our futures. Multiple coefficients are used to calculate our "confidence ratio" and estimate the volume of our trades, limiting the exposure of our position at each instance.

Another key aspect is the way we maintain the portfolio Delta Neutral. Again, usign Black sholes we estimate our current Delta, but instead of actively acting to go back to neutrality, we take our delta into account when positioning further bids or asks, and adjust the volume accordingly. This ensures that we don't "waste" interactions actively trading, and we focus on constantly placing passive bets that we are confident to be profitable.

## Code 
Some of the functions in the code rely on Optibook library, but the core idea of the Strategy is fully interchangable to any other environment. The code is attached in the github repo as algo.py.

There are 2 main functions to take an action in a given orderbook:
- `active_trade` that seeks for arbitrage oporunities and inmediately acts upon those. Arbitrage oportunities are scanned between Dualstocks, where there should be a 1:1 correlation between the prices

- `place_bid_ask_spread` is the leading passive strategy that accounts for the highest part of the performed trades. It is mostly based on 3 parameters:
  - Portfolio's Delta: we want our portfolio to remain delta neutral. We dynamically act to place the volume of our bets to move our delta in the desired direction. We calculate our portfoli's delta with overall_delta and the impact of the stock we are trading with calculate_option_delta (works also for futures).
  - Total Position: During the competition, each team had a limited exposure of 100 positions. This was very important, and to maximize our trades we wanted our position to remain around 0. To solve this, the approach is similar to the one for the delta , where we dinamically adjust the volume of our trades.
  - Confidence Ratio: from volume_weighted_average and using Black Sholes, we estimate the price of the stock we are trading. We derive a confidence ratio, for the space between the estimated price and the current price - to avoid being overly exposed to fast price fluctuations against our favour.
