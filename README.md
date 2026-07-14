# Trader Performance vs. Bitcoin Market Sentiment

Analysis of the relationship between Hyperliquid trader performance and Bitcoin market sentiment (Fear & Greed Index), submitted as part of the Primetrade.ai Data Science hiring assignment.

## Objective

Explore how trader behavior and profitability shift with market sentiment, uncover patterns in the data, and translate them into insights that could inform a sentiment-aware trading strategy.

## Datasets

| Dataset | Description | Key Columns |
|---|---|---|
| `fear_greed_index.csv` | Daily Bitcoin market sentiment classification | `date`, `value`, `classification` |
| `historical_data.csv` | Trade-level execution data from Hyperliquid | `Account`, `Coin`, `Execution Price`, `Size Tokens`, `Size USD`, `Side`, `Closed PnL`, `Fee`, `Timestamp` |

The two datasets are joined on trade date, mapping each trade to the market sentiment in effect that day.

## Repository Structure

```
├── trader_sentiment_analysis.ipynb   # Full analysis notebook
├── fear_greed_index.csv              # Sentiment dataset (input)
├── historical_data.csv               # Trade dataset (input)
└── README.md                         # This file
```

## Methodology

1. **Data validation** — checked both datasets for missing values, duplicates, and inconsistent date formats before merging.
2. **Cleaning & merge** — normalized dates (`DD-MM-YYYY` for sentiment, epoch ms for trades) and left-joined trades onto daily sentiment.
3. **Data quality checks** — flagged unresolved coin codes (e.g. `@107`) and confirmed the presence/absence of a leverage field, so downstream coin-level results exclude noise from unmapped tickers.
4. **Exploratory analysis** — sentiment distribution, PnL by sentiment (mean, median, std, win rate), long vs. short performance, sentiment × side interaction, coin-level performance, top-trader concentration, and a trimmed correlation matrix on trade-level numeric features.
5. **Statistical testing** — Kruskal-Wallis test across sentiment groups to confirm whether observed PnL differences are statistically significant rather than noise from unequal sample sizes.

## Key Findings

- **Trade volume skews heavily toward Fear.** The majority of trades in this dataset occurred on days classified as Fear (~134k trades), with far fewer during Extreme Greed (~7k) or Neutral (~7k) conditions — any sentiment-level comparison needs to account for this imbalance.
- **Average profitability is highest during Greed, not Fear.** Mean Closed PnL by sentiment: **Greed ≈ 87.9**, Fear ≈ 50.0, Extreme Greed ≈ 25.4, Neutral ≈ 22.2. Profitability does not scale monotonically with "positive" sentiment — Extreme Greed underperforms plain Greed, suggesting a possible reversal or overextension effect at sentiment extremes.
- **Sell-side trades outperformed buy-side on average** (mean Closed PnL: Sell ≈ 60.7 vs. Buy ≈ 36.1) over the observed period, indicating short positions were, on average, more profitable in this dataset.
- **PnL is concentrated among a small number of high-volume accounts.** The top 10 accounts by total PnL each generated between roughly $0.38M–$2.1M, with average PnL per trade ranging widely (from ~$21 to ~$419), indicating some top performers win via high volume and others via fewer, larger wins.
- **Trade size and fees are strongly correlated (expected — fees scale with notional size), but trade size shows only a weak relationship with Closed PnL,** meaning larger trades are not reliably more profitable trades.

*(See the notebook's "Key Findings & Strategy Recommendations" section for the full interpretation, including the sentiment × side breakdown and the significance test result.)*

## Limitations

- Some `Coin` values are unresolved token codes (e.g. `@107`) rather than tickers; these are excluded from coin-level rankings.
- The `leverage` field referenced in the assignment brief was not present in the raw dataset under that name — see the notebook's data quality check for confirmation.
- Findings reflect this specific account sample and time window and are not adjusted for overall market direction (e.g. whether BTC was in an uptrend/downtrend independent of sentiment).

## How to Run

1. Place `fear_greed_index.csv` and `historical_data.csv` in the same directory as the notebook.
2. Install dependencies: `pip install pandas numpy matplotlib scipy`
3. Open `trader_sentiment_analysis.ipynb` and run all cells top to bottom.

## Author

[Your Name]
Submitted for: Primetrade.ai Data Science Hiring Assignment
