# Markov Chain-Based Financial Prediction System

## Overview
This system utilizes **Markov chains** to model transition probabilities between states, calculate the expected value of each state, and predict outcomes. If the predicted value exceeds a predefined threshold, an order is executed.

## Mathematical Description

### State Transition Probability
The probability of transitioning from state $i$ to state $j$ is denoted by $P_{ij}$:

$$ P_{ij} = \frac{\text{Number of transitions from } i \text{ to } j}{\text{Total transitions from } i} $$

### Expected Value of a State
The expected value $E(s_i)$ of a state $s_i$ is:

$$ E(s_i) = \sum_{j=1}^{n} P_{ij} \cdot V_j $$

where $V_j$ is the value of transitioning to state $j$.

### Threshold-Based Decision Making
A threshold value $\theta$ is set, and if $O(s_i) > \theta$, an order is placed:

$$
\text{if } O(s_i) > \theta \text{ then}
\begin{cases}
\text{Buy order executed} & \text{if prediction is positive} \\
\text{Sell order executed} & \text{if prediction is negative}
\end{cases}
$$

### Outcome Analysis
The final outcome is analyzed after executing orders to assess the system's performance.

## Technical Indicators Calculation
- `close`: Extracts closing prices as a numpy array.
- `Short_EMA`: Calculates the 14-day Exponential Moving Average (EMA).
- `Long_EMA`: Calculates the 50-day EMA.
- `RSI`: Computes the 14-day Relative Strength Index (RSI).

## State Determination and Percentage Change
- `Close_Gap`: Determines the percentage change in closing prices.
- `State`: Binary state based on `Close_Gap`; `1` for positive changes, `0` for non-positive.
- `Short_EMA_Gap`: Percentage change in the short-term EMA.
- `Long_EMA_Gap`: Percentage change in the long-term EMA.

# Event Pattern Generation

### Function: `categorize_gap`
Categorizes gaps into high and low for positive and negative changes.

- **Parameters**:
  - `df`: DataFrame to process.
  - `column_name`: Name of the column to categorize.
- **Process**:
  - Categorizes values into 'Neutral', 'IL', 'IH', 'DL', and 'DH' based on quantile-based discretization.
- **Returns**: A Series with categorized values appended with '_LMH'.

### Application of `categorize_gap`
Applied to 'Short_EMA_Gap' and 'Long_EMA_Gap' to create 'Short_EMA_Gap_LMH' and 'Long_EMA_Gap_LMH'.

### RSI Signal Determination
- `RSI_Signal`: Generates signals based on RSI values ('OB' for overbought, 'OS' for oversold, 'N' for neutral).

### Creation of `new_set_df`
A new DataFrame `new_set_df` is created with an `Event_Pattern` column concatenating 'Short_EMA_Gap_LMH', 'Long_EMA_Gap_LMH', and 'RSI_Signal'.

# Construction of the Transition Grid

## Function: `build_transition_grid`
Constructs a stochastic matrix representing transition probabilities and calculates the expected value change.

1. **Initialization**: Define unique patterns and an empty transition grid.
2. **Transition Counting**: Count transitions from state $s_i$ to $s_j$.
3. **Expected Value Change Calculation**: Calculate the average change in 'Close_Gap' for transitions.
4. **Grid Construction**: Create and normalize a DataFrame to obtain transition probabilities.
5. **Change Grid Construction**: Create a DataFrame with expected value changes for each transition.

### Output
Returns two DataFrames:
- $ \mathcal{T} $: Transition probability grid.
- $ \mathcal{C} $: Expected value change grid.

## Function: `calculate_expected_value_matrix`
Computes the expected value matrix by combining transition probabilities with value changes.

$$ E = T \odot C $$

where $ \odot $ denotes the element-wise multiplication.

### Implementation
- Converts input DataFrames to NumPy arrays for efficient computation.
- Converts the resulting matrix back to a DataFrame.

# Prediction and Performance Evaluation

1. **Initialization**: Initialize `actual`, `predicted`, and `change_ratio`.
2. **Prediction Loop**: Iterate through event patterns, predicting based on the threshold.
3. **Outcome and Investment Update**: Update `change_ratio` based on predictions.
4. **Recording Results**: Append actual and predicted outcomes.
5. **Return Values**: Returns lists of actual and predicted outcomes, and the final `change_ratio`.

## Interpretation
With this method, a **16%** profit was achieved. However, compared to a buy-and-hold strategy's **58%** profit, this model underperformed. The success of the Markov chain method relies on creating states that accurately describe the stock market. Future improvements could involve reinforcement learning and more complex indicators.

# Conclusion
This project creates states for a stock, generates a Markov chain transition matrix, and calculates expected outcomes for each state's next step. The process is repeatable with any stock and time range. To improve the strategy, different Technical Indicators is recommended as Markov chain is a method to describe state change, if correct states are used the method can achieve high performance.
