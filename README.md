# Workbook Investment Analysis

This is a custom tool to analyze financial products such as
Stocks, ETFs, and Mutual Funds across multiple countries.
It is an interactive tool which will provide the performance
analysis of these products using various KPIs. 
This code was generated using CODEX - OpenAI. 

Streamlit is used for an easy interactive platform.

A local Python app for screening global stocks, ETFs, and mutual funds using only an uploaded Excel workbook. No external financial data, API keys, network requests, or pretrained investment models are used by the analysis code.

## Run

From this directory:

```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
streamlit run app.py --server.address 127.0.0.1 --browser.gatherUsageStats false
```

Installing dependencies needs internet access; analysis does not. Upload an `.xlsx` or `.xls`, select a worksheet, map columns, confirm comparability, and ask a question. The original workbook is never modified. Uploaded data is held in the app process and is not deliberately persisted.

## Workbook requirements

Use headers in row 1 and one investment observation per row. Required fields are an investment identifier, numeric historical growth, and numeric nonnegative risk where higher means riskier. Optional fields are sector, country and asset type. Arbitrary column names are supported through manual mapping. There is no supplied real dataset in this project.

Choose the same growth horizon, percentage scale, currency basis and risk definition across compared rows. For example, do not mix annualized and cumulative growth, volatility and beta, or local-currency and common-currency returns. The app asks you to confirm this because it cannot establish comparability from arbitrary headers. Negative risk measures such as signed drawdown must be transformed explicitly in your workbook before use. Excel numeric percentage cells retain their decimal values: 12% displays in results as 0.12. Percent strings are excluded rather than guessed. Formula cells require saved cached values from Excel; the app does not execute formulas or macros. Sheets with title rows or multiple tables should be prepared as one clean table first.

## What it answers

- Average risk with moderate growth: both metrics within their sample's 25th–75th percentile bounds, and growth above zero.
- Sector performance: mean and median growth and counts, grouped using the workbook's sector values. These are sample statistics, not sector index returns.
- Investment blends: up to five positive-growth holdings with risk at or below the sample median, ranked by growth percentile minus risk percentile, equally weighted. Duplicate identifiers block this feature.
- Lowest risk, highest historical growth, ML clusters, and “similar to [full identifier]”.

Question handling is a transparent keyword router, not a general-purpose conversational model. Phrase questions using these supported concepts; arbitrary constraints in a question are not interpreted. Use country and asset-type controls for those filters. Unsupported questions return a capability message. Missing fields are not inferred. Results include source sheet/row references.

## Machine learning and limitations

StandardScaler and K-means fit only the eligible worksheet's growth and risk values. Up to three clusters identify groups of similar observations; cluster numbers have no ordinal meaning. Similarity uses standardized Euclidean distance within the filtered sample. No future-return training target exists, so the app deliberately does not train a forecasting model or claim predictive accuracy. Invalid rows are reported and excluded without imputation.

Percentile scores and clusters are fitted on the full eligible sheet. Screening thresholds and sector summaries use filtered rows. Small groups and duplicate records can distort sector summaries; inspect the quality report and source rows. Cross-currency comparisons require a common basis already present in the workbook. Fund holding overlap is unknown unless separately analyzed.

The blend is an illustrative screen, not portfolio optimization. Weighted historical growth is an arithmetic combination of supplied values, not a backtest or forecast; it need not equal realized portfolio growth for compounded metrics. Individual risk metrics cannot establish portfolio risk or diversification. Covariance-aware allocation requires aligned return histories, currency and return-frequency definitions, and investment constraints. The app does not manufacture these inputs or assess client suitability.

## Verification

```sh
python -m unittest -v
```

Tests use synthetic in-memory records isolated from the application. No real-workbook conclusions are available until a dataset is supplied.
