# swr - Safe Withdrawal for Retirement

![outcome.png](outcome.png)

![optimal_by_gamma_table.png](optimal_by_gamma_table.png)

A Python module for determining safe withdrawal rates, designed to accommodate:

- Any generator of asset returns (historical cohorts, Monte Carlo, roll your own market environment)
- Any asset allocation strategy (fixed weights, glidepath schedules, roll your own based on any parameters)
- Any withdrawal strategy (fixed withdrawal, variable percentage, combinations, glidepaths)
- Any metrics to evaluate retirement cohort outcomes (e.g. total spending, certainty equivalent spending, roll your own. Support for survival tables, i.e. calculate expected metric for living retirees taking into account retirement age and survivorship)
- Any (gradient-free) optimizer to find optimal parameters (e.g. asset allocation, withdrawal parameters) to maximize a metric in the given market environment

Example (see [example.ipynb](example.ipynb)):

   - Market environment: historical returns 1928-2020; 2 assets, stocks and intermediate-term corporate bonds; analyze 30-year retirement cohorts.
   - Allocation rule: 1 parameter = `stock_alloc`. Allocate a fixed percentage to stocks. `bond_alloc` = 1 - `stock_alloc`.
   - Withdrawal rule: 3 parameters = `fixed_pct`, `variable_pct`, `floor_pct`. Withdrawal is a linear function of the portfolio value, with a floor. Withdraw `fixed_pct` (intercept), plus `variable_pct` (slope) * portfolio value, with a floor: `max(floor_pct, fixed_pct + variable_pct * portval / 100)`.
   - Metric to maximize: certainty-equivalent spending under CRRA utility with a gamma risk aversion parameter.
   - In this example, for each gamma value, we run optimizers to find the parameters (`stock_alloc`, `fixed_pct`, `variable_pct`, `floor_pct`) that would have maximized certainty-equivalent spending over all available 30-year retirement cohorts 1928-1991.

	


To implement a different strategy context, see [SWRsimulationCE.py](SWRsimulation/SWRsimulationCE.py), which inherits from [SWRsimulation.py](SWRsimulation/SWRsimulation.py). Override relevant methods:

   - To use a different market environment, override `init_simulation` which should initialize an appropriate generator function in self.simulation['trials'].
   - To use a different allocation rule, override `init_allocation` and `get_allocations`.
   - To use a different spending rule, override `init_withdrawal` and `get_withdrawal`.
   - To calculate different metrics, override `eval_trial`.
   - See [optimize.ipynb](optimize.ipynb) for examples of running gradient-free optimizers to find retirement parameters that optimize a specified metric.

