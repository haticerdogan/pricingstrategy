# Bid Pricing & Win-Rate Modeling
Commercial janitorial & day-porter services often bid competitively. Pricing too high reduces win-rate, while pricing too low cuts margins.
My goal is to recommend a bid price that maximizes expected profit using historical bid outcomes while maintaining a high win probability.

### Problem Framing

I model the business decision as:

Expected Profit = (Price − Cost) × Win Probability

EV(p) = (p - c) * P(Win) 

Where:

Win Probability is predicted by a calibrated classification model, calibrated probability of award based on price and job attributes

Price is a decision variable we optimize over aka bid price

Cost to deliver the service only if we win (variable delivery cost)

A 10% minimum margin constraint is enforced on every bid

We use historical bids with known Awarded / Lost outcomes to learn how price sensitivity and site attributes influence win-rate.

We assume that costs are only incurred if the contract is awarded.
Therefore, the loss scenario has zero financial consequence, and the general expected value formula:

EV=(Profit if win)P(win)+(Profit if lose)P(lose)

simplifies to:

EV=(p−c)⋅P(win)+(0)⋅(1−P(win))

Janitorial/Day Porter costs (labor, materials, supplies) are not spent unless we win.

Proposal preparation costs are assumed to be negligible or handled outside this model.

We enforce a 10% minimum profit margin to ensure viability:

p-c / p  >= 0.10

Thus, pricing optimization focuses entirely on the trade-off between:
Higher price → higher margin but lower win probability
Lower price → lower margin but higher win probability

The model selects the price that maximizes expected profit, not just the highest margin or probability.