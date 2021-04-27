# Reverse Greedy Bot

> Automated cryptocurrency trading bot

## Why?

This project was inspired by the observation that in retail, the proportion of units sold of SKUs in a particular mature category have a tendency to stick. Say you sell 1 kg tomatoes, 5kg tomatoes and 10kg tomatoes. Over a long period of time, as the tomato category matures, you end up seeing that the ratio of sales of 1kg:5kg:10kg doesn't significantly change. It does have momentary fluctuations due to promotions, instock rate etc. But when these momentary issues are resolved they go back to that ratio. When your customer base is large, then new customers onboarded, don't change this ratio significantly. Unless of course there is consistent bad experience with say the 1kg Tomato and customers stop buying them. 

Now if we map the unit sales of these tomatoes to stock/crypto prices, we get an interesting transformation. You can think of the units, as price for the assets and the entire asset class as a retail category (for example tomatoes). 

All cryptocurrencies pretty much behave in the same way. When one spikes, they all spike, and when one takes a dive, they all do. _Pretty much_. Moreover, all coins follow Bitcoin's lead; the difference is their phase offset.

So, if coins are basically oscillating with respect to each other, it seems smart to trade the rising coin for the falling coin, and then trade back when the ratio is reversed.

>**NOTE**: This works only when the coin is inherently good (like a good tomatoe sku and is a good project).

## How?

The trading is done in the Binance market platform, which of course, does not have markets for every altcoin pair. The workaround for this is to use a bridge currency that will complement missing pairs. The default bridge currency is Tether (USDT), which is stable by design and compatible with nearly every coin on the platform. You can use other bridge currencies like USDC, EUR etc.

<p align="center">
  Coin A → USDT → Coin B
</p>

The way the bot takes advantage of the observed behaviour is to always downgrade from the "strong" coin to the "weak" coin, under the assumption that at some point the tables will turn. It will then return to the original coin, ultimately holding more of it than it did originally. This is done while taking into consideration the trading fees.

<div align="center">
  <p><b>Coin A</b> → USDT → Coin B</p>
  <p>Coin B → USDT → Coin C</p>
  <p>...</p>
  <p>Coin C → USDT → <b>Coin A</b></p>
</div>

The bot jumps between a configured set of coins on the condition that it does not return to a coin unless it is profitable in respect to the amount held last. This means that we will never end up **having less of a certain coin**. The risk is that one of the coins may freefall (consistently bad 1kg tomatoe) relative to the others all of a sudden, attracting our reverse greedy algorithm.

## Binance Setup

-   Create a [Binance account](https://accounts.binance.com/en/register).
-   Enable Two-factor Authentication.
-   Create a new API key.
-   Get a cryptocurrency. If its symbol is not in the default list, add it.

## Tool Setup

### Install Python dependencies

Run the following line in the terminal: `pip install -r requirements.txt`.

### Create user configuration

Create a .cfg file named `user.cfg` based off `.user.cfg.example`, then add your API keys and current coin.

**The configuration file consists of the following fields:**

-   **api_key** - Binance API key generated in the Binance account setup stage.
-   **api_secret_key** - Binance secret key generated in the Binance account setup stage.
-   **current_coin** - This is your starting coin of choice. This should be one of the coins from your supported coin list. If you want to start from your bridge currency, leave this field empty - the bot will select a random coin from your supported coin list and buy it.
-   **bridge** - Your bridge currency of choice. Notice that different bridges will allow different sets of supported coins. For example, there may be a Binance particular-coin/USDT pair but no particular-coin/BUSD pair.
-   **tld** - 'com' or 'us', depending on your region. Default is 'com'.
-   **hourToKeepScoutHistory** - Controls how many hours of scouting values are kept in the database. After the amount of time specified has passed, the information will be deleted.
-   **scout_multiplier** - Controls the value by which the difference between the current state of coin ratios and previous state of ratios is multiplied. For bigger values, the bot will wait for bigger margins to arrive before making a trade.
-   **strategy** - The trading strategy to use. See [`binance_trade_bot/strategies`](binance_trade_bot/strategies/README.md) for more information

#### Environment Variables

All of the options provided in `user.cfg` can also be configured using environment variables.

```
CURRENT_COIN_SYMBOL:
BRIDGE_SYMBOL: USDT
API_KEY: 
API_SECRET_KEY: 
SCOUT_MULTIPLIER: 5
SCOUT_SLEEP_TIME: 5
TLD: com
STRATEGY: default
```

### Paying Fees with BNB
You can [use BNB to pay for any fees on the Binance platform](https://www.binance.com/en/support/faq/115000583311-Using-BNB-to-Pay-for-Fees), which will reduce all fees by 25%. In order to support this benefit, the bot will always perform the following operations:
-   Automatically detect that you have BNB fee payment enabled.
-   Make sure that you have enough BNB in your account to pay the fee of the inspected trade.
-   Take into consideration the discount when calculating the trade threshold.

### Run

`python -m binance_trade_bot`

## Disclaimer

This project is for informational purposes only. You should not construe any
such information or other material as legal, tax, investment, financial, or
other advice. Nothing contained here constitutes a solicitation, recommendation,
endorsement, or offer by me or any third party service provider to buy or sell
any securities or other financial instruments in this or in any other
jurisdiction in which such solicitation or offer would be unlawful under the
securities laws of such jurisdiction.

If you plan to use real money, USE AT YOUR OWN RISK.

Under no circumstances will I be held responsible or liable in any way for any
claims, damages, losses, expenses, costs, or liabilities whatsoever, including,
without limitation, any direct or indirect damages for loss of profits.
