# Bid Pricing & Win-Rate Modeling

Commercial janitorial & day-porter services often bid competitively. Pricing too high reduces win-rate, while pricing too low cuts margins.
This project explores how historical bid outcomes can guide predictive pricing. I model the relationship between a proposal’s price/features and its probability of winning, then optimize pricing to maximize expected profit while staying above a margin floor.

![Scenario B Flowchart](docs/flowchart.png)

### Problem Framing

Service providers submit bids (mostly unstructured PDFs) with narrative and cost estimates.
We hold ~10 years of historical bids. So I created synthetic a proposal text data and a synthetic structured data with 1,200 bids:

- Structured features (sqft, cost, client industry)

- Service features extracted from PDFs (restrooms, kitchen, windows, etc.)

- Price per sqft and bid outcomes (awarded / not awarded)

**Business Goal:**
Recommend a bid price that balances profitability and competitiveness.

**Success Metrics:**
- Win-rate lift vs. status-quo pricing

- Expected profit

- ROC-AUC and Brier score for classification & calibration.

### Key Assumptions

- Pricing’s primary effect on win probability is captured through price per sqft

- Losing a bid has no direct financial loss (opportunity cost excluded in model)

- A 10% minimum margin guardrail must always hold

- Synthetic data is used to simulate pricing dynamics

- A detailed discussion of these assumptions is included in the notebook.

### Modeling Approach

1) Feature Engineering

- price_per_sqft, log_sqft

- Categorical: one-hot encoded client industry

- Text: boolean service requirements extracted via regex
(assumes OCR already completed on PDFs)

2) Win-Rate Model

- Logistic Regression

- StandardScaler on continuous features

- Isotonic Calibration improves probability reliability

3) Expected Profit Optimization

Expected Value (profit) for price p:

EV(p)=P(win∣p)×(p−c)

Subject to:

p - c / p ≥10%

Model search over price candidates and return:

- Highest expected profit
- With ≥10% margin
- And strong predicted win probability

### Results

Example proposal: 
- Office space with 13,160 sqft 
- Estimated cost: $998.52
- Recommended price: $1,806.18
- Expected profit: $350.35
- Win probability: high (from model estimate)