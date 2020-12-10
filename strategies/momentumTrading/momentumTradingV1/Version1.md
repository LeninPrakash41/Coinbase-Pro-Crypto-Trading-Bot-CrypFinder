# Momentum trading strategy version 1
The first attempt at an implementation of momentum trading. This version has a slightly more complex trading algorithm as it features a variable called sellPositionProfitDelta which is the minimum amount of money needed to be made before selling position the program will account for taker and maker fees as well. There are some bugs present in this version, it's being saved in case someone wants to revisit it and improve it. It also doesn't have a working TradingAnalyzer.

# Documentation as it existed with this version: 

## CrypFinder Summary: 
CrypFinder is a Coinbase Pro API trading bot that currently implements a basic momentum trading strategy in NodeJS using the Coinbase Pro API, as well as its own custom library for the endpoints that are not supported by the now deprecated Coinbase Pro NodeJS Library. Currently, Coinbase Pro limits the number of portfolios to five, this means that the bot can run up to four trading instances simultaneously per Coinbase Pro account. This bot can be modified to trade any product pairs available on Coinbase Pro, such as BTC-USD, ETH-USD, etc., but stablecoin (USDC to other coins) and crypto markets (coin to other coins) aren't currently supported, only USD markets (USD to coins). 

The currently implemented momentum strategy will work as follows: The bot will start by getting the amount of USD available for the provided API key's profile (profile=portfolio on the coinbase pro website). If the amount is greater than zero, it will monitor the price changes of the chosen product using a peak/valley system; if the price changes by the specified delta, it will purchase a position. Then, it will monitor price changes until the delta condition and profit condition are met; after selling for a profit, it can deposit a cut of the profit to a different portfolio for saving. 

The bot features a number of variables at the top that can be configured to customize how it trades. This includes the deltas that specify an amount of price change that will trigger a buy/sell, the minimum acceptable profit from a trade, the name of currency to trade, the profile names, the deposit enable/disable flag, the deposit amount, and more. Of course, any of the code can be modified to customize it fully. This project is a great way to trade crypto on Coinbase Pro through their API.

## How to run the program:
I suggest starting by using this program in the [Coinbase Pro Sandbox](https://docs.pro.coinbase.com/#sandbox) for testing. 
1. Create a coinbase pro account. Install NodeJS, install Git, consider adding an ESLint plugin to your IDE to use the eslint configuration.
2. Setup your Coinbase Pro account portfolios (profiles), this is an important part of the bot. The bot will swoop up any available balance in a profile and start trading with it, so in order to safely store some of the profits you must specify another profile to deposit to. Currently, Coinbase Pro limits profiles to five. I recommend setting up the portfolios with the Default, Profit savings, and ___ trader (I.E. 'BTC trader' if it's trading BTC) for the other available three slots. Don't trade in the default profile because if you transfer money in, it could get swept up by the bot before you can allocate it where you want to. Alternatively, you could deposit profits in the default portfolio; this would open up four profiles for bot trading. 
3. Create the API key for the profile you want the bot to trade on, give it View/Trade/Transfer permissions and whitelist your public IP. [More info here](https://help.coinbase.com/en/pro/other-topics/api/how-do-i-create-an-api-key-for-coinbase-pro). 
4. Clone the github repo locally and run `npm install` from within the repo directory.
5. Configure the variables at the top of index.js to select your environment, Deltas, product to trade, and profile names.
6. Create a .env file in the root directory of the projects repo with the following:

    API_KEY=\<your API key>

    API_SECRET=\<your API secret>

    API_PASSPHRASE=\<your API passphrase>

    Additionally consider adding `LOG_LEVEL=debug` here if you want the full debug logs.

7. Add some funds to your default portfolio and make sure there is no existing coin balance for the product you're trading if you're just starting the bot. See "Restarting the bot" if you want the bot to pick up where it left off after stopping it.
8. Run the program with `node index.js` from within the repo directory.

### Restarting the bot:
If at any point the bot stops running for any reason, the the bot keeps a file called positionData.json that tracks whether or not it was in a position, and the information associated with that position. If you restart the bot it will read that file and automatically pick up where it left off. Don't try to add more coins to the existing position or it will cause unexpected behavior since the bot won't know the associated costs with the newly added coin. You can, at any point, add USD to a portfolio, the bot will start trading with the newly added USD when it completes a buy/sell cycle. If you want to start the bot fresh without existing data, simply make sure there is no left over coin in your profile and delete the positionData.json file.