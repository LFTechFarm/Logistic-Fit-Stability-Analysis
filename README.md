# Logistic Curve Fitting Stability & Sensitivity Analysis

This project provides a **theoretical evaluation of logistic (sigmoid) curve fitting**, using Monte Carlo simulation.  
It explores two complementary aspects:

1. **How sensitive the fitted curve is to individual data points**, depending on their position along the x-axis.  
2. **How many data points are required to obtain a stable and reliable logistic fit**, under noise and random sampling.

All experiments use simulated data generated from a known logistic function, allowing evaluation of fit quality against the true curve.

---

## 📌 Logistic Function

The model used throughout the analysis is the standard 3-parameter logistic (sigmoid) function:

$$
y(x) = \frac{L}{1 + e^{-k (x - x_0)}}
$$

where:

- **L** : upper asymptote  
- **k** : growth rate  
- **x₀** : inflection point  

---

# 1. Point-Removal Sensitivity Analysis

### Objective
Determine how much each region of the logistic curve contributes to the accuracy of the fit.  
This is done by **removing one point at a time**, refitting the model, and measuring the impact on MSE.

### Method Summary

For each Monte Carlo run:

1. Generate `n_points` evenly spaced x values in `[0, 10]`.  
2. Compute noiseless values from the true logistic function.  
3. Add Gaussian noise to obtain observed data.  
4. Fit a logistic curve to all points → **baseline fit**.  
5. Partition the x-axis into intervals (e.g., width = 1).  
6. For each interval:
   - Remove each point inside the interval one at a time.
   - Refit logistic curve.
   - Compute **ΔMSE = MSE_modified − MSE_baseline**.
7. Aggregate average impact per interval across all runs.

### Output

- Plot of **mean ± std** of ΔMSE for each x-interval.
- Visualization of numerous fitted curves under noise.
- Array of interval-wise sensitivity values.

### Interpretation

- Regions near the **inflection point** typically dominate model accuracy.  
- Points in horizontal asymptote regions contribute less to identifying the curve parameters.

---

# 2. Fit Stability vs. Number of Points

### Objective
Estimate how many data points are required to obtain a **stable, robust** logistic fit.

### Method Summary

For each Monte Carlo iteration:

1. Randomly choose a sample size `n_sample` within `[min_points, max_points]`.
2. Randomly select that many x-values from a dense x-grid.
3. Generate noisy observations.
4. Fit the logistic curve.
5. Compute MSE between the fitted curve and the true curve.
6. Track:
   - Mean MSE per number of points  
   - Proportion of successful fits  
   - Number of extreme MSE values discarded  
7. Determine the **stabilization number of points**:

\[
\Delta \text{MSE}_{n,n+1} < 0.01 \times \max(\text{MSE})
\]

### Output

- MSE vs. number of points (with stabilization indicator)
- Probability of successful fit vs. number of points
- Count of removed extreme MSEs vs. number of points
- Recommended minimum number of points

### Interpretation

- Shows how the logistic fit becomes more accurate and more stable with more data points.
- Identifies the threshold at which additional points bring diminishing returns.

---

# 📊 Plots Produced

### From Point-Removal Analysis
- **Impact of point removal per interval** (error bars)
- **Overlay of logistic fits** from multiple noisy runs

### From Fit Stability Analysis
- **Mean MSE vs. number of points**, with stabilization line  
- **Success rate of curve fitting**  
- **Count of extreme MSE outliers removed**

All plots help visualize how logistic fitting behaves under varying sampling conditions.

---

# 🧪 Code Structure

The main functions provided in the project:

### `analyze_point_removal(...)`
Runs Monte Carlo simulations to quantify the impact of removing points in different x-intervals.

Returns:

- `all_impacts`: matrix of shape `(runs, n_intervals)`
- `intervals`: interval boundaries

---

### `evaluate_fit_stability(...)`
Runs large-scale Monte Carlo simulation to estimate:

- Required number of points for stable fitting  
- Expected fit accuracy  
- Probability of successful convergence  

Returns:

- Recommended minimum number of points

---

# ⚙️ Key Parameters

### Logistic parameters
- `L_true` — asymptote  
- `k_true` — slope  
- `x0_true` — inflection point  

### Experiment parameters
- `runs` / `n_runs` — number of Monte Carlo iterations  
- `n_points` — number of points per simulation (point-removal analysis)  
- `min_points`, `max_points` — sample size range (stability analysis)  
- `noise_level` — Gaussian noise standard deviation  
- `max_mse` — threshold for outlier removal  

---

# 📝 Example Usage

```python
all_impacts, intervals = analyze_point_removal(n_points=10, runs=5000)

recommended_points = evaluate_fit_stability(
    min_points=4,
    max_points=20,
    n_runs=20000
)

print("Recommended number of points:", recommended_points)
