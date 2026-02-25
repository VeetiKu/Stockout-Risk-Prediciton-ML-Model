# Inventory Stockout Risk Prediction

# How to Run the Code

## Requirements
- Python 3.9 or newer
- pip

## Setup

1. Install dependencies:
```bash
pip install -r requirements.txt
```

## Problem

Items may unexpectedly go out of stock, reducing sales, item availability and customer satisfaction.

## User

Inventory Planners

## Decision

Supports daily inventory planning by helping operations teams prioritize which SKU and venue pairs to proactively review or intervene on, given limited operational capacity.

## Relevancy

Stockouts reduce item availability and customer satisfaction, and can lead to lost orders and revenue. By predicting which SKUs are at a high risk of stocking out the next day, inventory planners can react earlier, enabling faster replenishment reducing the total number of stockouts.

## Data Used

- Daily item sales data across multiple venues in Finland
- SKU information like sales, availability and stockout indicators

## Key EDA findings

- Recent stockouts highly increase the probability of a future stockout
- Low Stock availability is associated with higher next day stockout risk
- Sales spikes of a certain SKU increases the chance of a stockout the next day

## Important observations for modeling

- It is more useful to rank high-risk cases than to predict exact probabilities.
- Item Availability is a critical signal for next day stockouts.

## Signals Created from the data

### Sales history Signals

- lag-1 sales
- 7-day rolling statistics

### Stockout history Signals

- lag-1 stockout indicator
- 7-day stockout counter

### Availability Signals

- In-stock-ratio

### Promotion Signals

- Promotion flag and depth to model demand uplift from discounts.

### Temporal Signals

- Day of week and weekend indicators to capture demand patterns.

## Not Used

- Any Information not available at the prediction time
- SKU ID or Venue ID was not given to the model because they are just labels and not useful for the prediction.
- Restock Data (not available in the dataset but could be used to increase prediction accuracy)

## Modeling approach

- Framed the task as a binary classification problem
- Model used was a gradient boosted decision tree model (LightGBM), because of its ability to capture nonlinear interactions.
- Model was trained using a time based validation split.

## Key assumptions

- The model assumes no direct knowledge of upcoming restocks and it solely relies on historical behaviour instead.
- Signals such as: Recent sales, availability and stockout history contain sufficient information to estimate near future stockout risk.

## Model evaluation

- Model was evaluated using time based validation split.
- The model was tested on future days only
- Models performance was compared against a naive baseline.

## Metrics used

Recall@K (top 5% and top 10%). This measures how many true next day stockouts are captured when reviewing only the highest risk SKUs.

## How good are the results

- The model captures roughly 1 in 4 next-day stockouts at the top 10% highest-risk SKUs
- This makes the model useful for prioritizing a small subset of items for manual review
- Performance is directionally valuable for decision support, but not sufficient for fully automated restocking without human oversight and review.

## Known Limitations

- The model does not have any insight into future restocks
- Only short-term historical signals are used, which may miss long-term demand trends or seasonal trends
- Stockouts are treated as a binary event which ignores partial availability and stockouts during a day.
- The model observes in-stock minutes not on hand inventory.

## When would this break in a production setting

- Changes in restocking frequency or supplier behaviour, since this would make the historical stockout patterns unreliable.
- Major Demand spikes for example during the holidays, as this causes demand spikes the model has not seen before.
- Since the model relies entirely on past sales, availability, and stockout behavior, its performance degrades when underlying demand or supply dynamics shift.

## Next Steps

- Add planned restock schedules and delivery schedules to better capture the true stockout risk and increase performance.
- Add calendar effects to capture holiday signals to better anticipate sudden spikes in demand.
- Add a measure for prediction confidence to help decide when to trust the model
- Add more signals to allow for more accurate predictions.
- Extend the prediction horizon(e.g. 4 days) to allow more time for inventory intervention.


