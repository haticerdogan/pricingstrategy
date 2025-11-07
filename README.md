# Bid Pricing & Win-Rate Modeling

Commercial janitorial and day-porter services often operate in competitive bidding environments.
Pricing too high reduces win-rate, while pricing too low erodes profit margins.

This project explores how historical bid outcomes can guide predictive pricing.
I model the relationship between a proposal’s price and features and its probability of winning,
then optimize pricing to maximize expected profit while maintaining a minimum margin guardrail.

![Scenario B Flowchart](docs/flowchart.png)

### Problem Framing

Service providers submit bids (mostly as unstructured PDFs) that include narrative text and cost estimates.
We assume access to ~10 years of historical bid data.
To demonstrate the concept, synthetic data was generated consisting of 1,200 bids with:

- Structured features: square footage (sqft), cost, client industry

- Service features and cleaning frequency was extracted from proposal text (e.g., weekly, 3 times a week, restrooms, kitchen, windows)

- Price per square foot (ppsf) and bid outcomes (awarded / not awarded)

**Business Goal:**
Recommend a bid price that balances profitability and competitiveness.

**Success Metrics:**
- Improved win-rate and expected profit

- Model calibration quality (Brier Score)

- Ranking accuracy (ROC-AUC)

### Key Assumptions

- Pricing’s main effect on win probability is via price per sqft.

- Losing a bid has no direct cost (opportunity cost ignored).

- A 10% minimum margin constraint is enforced.

- Data is synthetic, built to simulate real pricing dynamics.

- Detailed assumption discussion and validation are in the notebook.

### Modeling Approach

1) Feature Engineering

- Continuous: price_per_sqft, log_sqft

- Categorical: one-hot encoded client industry and cleaning frequency

- Text-derived: Boolean service flags (restrooms, kitchen, windows, etc.)

- Regex-based extraction assumes OCR preprocessing on PDFs

2) Win-Rate Model

- Logistic Regression with standardized inputs

- Isotonic Calibration applied post-modeling to improve probability reliability

- Evaluated using ROC-AUC (ranking ability) and Brier Score (probability accuracy)

3) Expected Profit Optimization

The expected value of profit for a proposed price (p) is modeled as:

$$
EV(p) = (p - c) \cdot P(\text{win} \mid p)
$$

subject to the minimum margin constraint:

$$
\frac{p - c}{p} \ge 0.10
$$

Where:

- $p$ = proposed bid price
- $c$ = operational delivery cost 
- $P(\text{win} \mid p)$ = calibrated probability of winning given bid price

The optimization procedure searches across a price grid and selects the price that maximizes expected profit while satisfying the margin constraint.

### Results

**ROC-AUC:** Measures how well the model ranks winning bids above losing bids.
(0.83 indicates strong discrimination and realistic sensitivity to price.)

**Brier Score:** Assesses how accurate probability predictions are (lower is better).
(~0.17 shows good calibration on synthetic data.)

**Isotonic Calibration:** Adjusts logistic regression probabilities to ensure monotonic, reliable win-rate curves — crucial for EV optimization.


**Blue line (Expected Profit):** rises as margin improves, then declines as win probability drops.
It stops where the bid price falls below the 10% margin floor.

**Orange line (Win Probability):** decreases smoothly as price increases.

**Red dashed line:** marks the optimal price point — where expected profit peaks.

This reflects realistic business dynamics:

- Low prices → high win probability but thin margins

- High prices → high margins but low win probability

- Optimal pricing → balanced profitability and competitiveness.

**How to Run**

Simply open the notebook and run all cells in order - all required libraries (e.g., numpy, pandas, scikit-learn, matplotlib) are already imported.
The notebook is self-contained and works in standard Python environments like Anaconda, JupyterLab, or Google Colab.

*Tip:* The notebook uses built-in randomization for synthetic data, so your numbers may vary slightly on each run, but the overall behavior and trends will remain consistent.