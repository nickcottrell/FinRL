# How to Run the Tests - Exact Instructions

## Quick Start (Copy & Paste)

```bash
# Navigate to the FinRL repository
cd /Users/nickcottrell/Repositories/FinRL

# Create a fresh virtual environment
python3 -m venv test_env_clean

# Activate it
source test_env_clean/bin/activate

# Install core dependencies (without alpaca-trade-api which causes urllib3 conflicts)
pip install --upgrade pip
pip install pytest numpy pandas gymnasium yfinance

# Install FinRL in development mode
pip install -e .

# Run the tests
python -m pytest unit_tests/environments/test_stocktrading.py -v
```

---

## Step-by-Step Instructions

### Step 1: Navigate to Repository

```bash
cd /Users/nickcottrell/Repositories/FinRL
```

### Step 2: Create Fresh Virtual Environment

```bash
python3 -m venv test_env_clean
```

This creates a clean environment without dependency conflicts.

### Step 3: Activate Virtual Environment

```bash
source test_env_clean/bin/activate
```

You should see `(test_env_clean)` in your prompt.

### Step 4: Install Dependencies

```bash
pip install --upgrade pip
pip install pytest numpy pandas gymnasium yfinance
```

**Note**: We're NOT installing `alpaca-trade-api` because it has urllib3 conflicts. The tests don't need it.

### Step 5: Install FinRL in Development Mode

```bash
pip install -e .
```

This installs FinRL from the current directory.

### Step 6: Run the Tests

**Option A: Run all tests with verbose output (recommended)**
```bash
python -m pytest unit_tests/environments/test_stocktrading.py -v
```

**Option B: Run specific test class**
```bash
# Just initialization tests
python -m pytest unit_tests/environments/test_stocktrading.py::TestStockTradingEnvInitialization -v

# Just trading action tests
python -m pytest unit_tests/environments/test_stocktrading.py::TestTradingActions -v
```

**Option C: Run single test**
```bash
python -m pytest unit_tests/environments/test_stocktrading.py::TestStockTradingEnvInitialization::test_initial_state -v
```

---

## What You Should See

### If Tests Pass ✅

```
============================= test session starts ==============================
platform darwin -- Python 3.12.11, pytest-9.0.1, pluggy-1.6.0
collected 31 items

unit_tests/environments/test_stocktrading.py::TestStockTradingEnvInitialization::test_environment_creation PASSED [  3%]
unit_tests/environments/test_stocktrading.py::TestStockTradingEnvInitialization::test_initial_state PASSED [  6%]
unit_tests/environments/test_stocktrading.py::TestStockTradingEnvInitialization::test_observation_space PASSED [  9%]
unit_tests/environments/test_stocktrading.py::TestStockTradingEnvInitialization::test_action_space PASSED [ 12%]
unit_tests/environments/test_stocktrading.py::TestResetFunctionality::test_reset_returns_correct_shape PASSED [ 16%]
unit_tests/environments/test_stocktrading.py::TestResetFunctionality::test_reset_is_deterministic PASSED [ 19%]
unit_tests/environments/test_stocktrading.py::TestResetFunctionality::test_reset_clears_state PASSED [ 22%]
unit_tests/environments/test_stocktrading.py::TestStepFunctionality::test_step_returns_correct_format PASSED [ 25%]
unit_tests/environments/test_stocktrading.py::TestStepFunctionality::test_step_advances_day PASSED [ 29%]
unit_tests/environments/test_stocktrading.py::TestStepFunctionality::test_terminal_state_reached PASSED [ 32%]
unit_tests/environments/test_stocktrading.py::TestTradingActions::test_buy_action PASSED [ 35%]
unit_tests/environments/test_stocktrading.py::TestTradingActions::test_sell_action PASSED [ 38%]
unit_tests/environments/test_stocktrading.py::TestTradingActions::test_hold_action PASSED [ 41%]
unit_tests/environments/test_stocktrading.py::TestTradingActions::test_cannot_buy_with_insufficient_cash PASSED [ 45%]
unit_tests/environments/test_stocktrading.py::TestTradingActions::test_cannot_sell_more_than_owned PASSED [ 48%]
unit_tests/environments/test_stocktrading.py::TestTransactionCosts::test_buy_incurs_cost PASSED [ 51%]
unit_tests/environments/test_stocktrading.py::TestTransactionCosts::test_sell_incurs_cost PASSED [ 54%]
unit_tests/environments/test_stocktrading.py::TestTransactionCosts::test_zero_cost_trading PASSED [ 58%]
unit_tests/environments/test_stocktrading.py::TestRewardCalculation::test_reward_reflects_portfolio_change PASSED [ 61%]
unit_tests/environments/test_stocktrading.py::TestRewardCalculation::test_reward_calculation_with_holdings PASSED [ 64%]
unit_tests/environments/test_stocktrading.py::TestTurbulenceHandling::test_turbulence_threshold_triggers PASSED [ 67%]
unit_tests/environments/test_stocktrading.py::TestTurbulenceHandling::test_turbulence_forces_sell PASSED [ 70%]
unit_tests/environments/test_stocktrading.py::TestTurbulenceHandling::test_no_turbulence_allows_trading PASSED [ 74%]
unit_tests/environments/test_stocktrading.py::TestPortfolioValue::test_portfolio_value_all_cash PASSED [ 77%]
unit_tests/environments/test_stocktrading.py::TestPortfolioValue::test_portfolio_value_with_holdings PASSED [ 80%]
unit_tests/environments/test_stocktrading.py::TestPortfolioValue::test_portfolio_value_consistency PASSED [ 83%]
unit_tests/environments/test_stocktrading.py::TestEdgeCases::test_all_stocks_action PASSED [ 87%]
unit_tests/environments/test_stocktrading.py::TestEdgeCases::test_extreme_action_values PASSED [ 90%]
unit_tests/environments/test_stocktrading.py::TestEdgeCases::test_rapid_trading PASSED [ 93%]
unit_tests/environments/test_stocktrading.py::TestEdgeCases::test_single_day_episode PASSED [ 96%]
unit_tests/environments/test_stocktrading.py::TestEdgeCases::test_state_invariants PASSED [100%]

============================== 31 passed in 2.34s ==============================
```

### If Tests Fail ❌

You'll see details about which test failed and why:

```
FAILED unit_tests/environments/test_stocktrading.py::TestTradingActions::test_buy_action
================================ FAILURES =================================
_______________________ TestTradingActions.test_buy_action _________________

    def test_buy_action(self, data, env_config):
        env = StockTradingEnv(df=data, **env_config)
        env.reset()
        initial_cash = env.state[0]

        # Buy 10 shares of first stock
        env.step(np.array([10.0, 0.0]))
>       assert env.state[0] < initial_cash  # Cash should decrease
E       AssertionError: Cash did not decrease after buy
```

---

## Troubleshooting

### Issue: "ModuleNotFoundError: No module named 'urllib3.packages.six.moves'"

**Problem**: This happens if `alpaca-trade-api` gets installed (it has urllib3 conflicts).

**Solution**:
```bash
# Deactivate current environment
deactivate

# Remove it
rm -rf test_env_clean

# Start over from Step 2 above
python3 -m venv test_env_clean
source test_env_clean/bin/activate
# ... etc
```

### Issue: "ModuleNotFoundError: No module named 'gymnasium'"

**Problem**: Gymnasium not installed.

**Solution**:
```bash
pip install gymnasium
```

### Issue: "ModuleNotFoundError: No module named 'yfinance'"

**Problem**: yfinance not installed (needed for test data).

**Solution**:
```bash
pip install yfinance
```

### Issue: "pytest: command not found"

**Problem**: pytest not installed.

**Solution**:
```bash
pip install pytest
# Then run with python -m pytest instead
python -m pytest unit_tests/environments/test_stocktrading.py -v
```

### Issue: Tests collect but fail to import FinRL

**Problem**: FinRL not installed in development mode.

**Solution**:
```bash
# Make sure you're in the FinRL directory
cd /Users/nickcottrell/Repositories/FinRL

# Install in development mode
pip install -e .
```

---

## Alternative: Run Without Virtual Environment

If you have the dependencies already installed globally:

```bash
cd /Users/nickcottrell/Repositories/FinRL
python3 -m pytest unit_tests/environments/test_stocktrading.py -v
```

**Warning**: This may fail if you have conflicting package versions installed.

---

## Expected Test Coverage

The test suite includes:

1. **TestStockTradingEnvInitialization** (4 tests)
   - Environment creation
   - Initial state verification
   - Observation/action space validation

2. **TestResetFunctionality** (3 tests)
   - Reset correctness
   - Determinism
   - State clearing

3. **TestStepFunctionality** (3 tests)
   - Step output format
   - Day advancement
   - Terminal state handling

4. **TestTradingActions** (5 tests)
   - Buy/sell/hold operations
   - Insufficient funds handling
   - Short selling prevention

5. **TestTransactionCosts** (3 tests)
   - Cost calculations
   - Zero-cost trading

6. **TestRewardCalculation** (2 tests)
   - Reward accuracy
   - Portfolio change tracking

7. **TestTurbulenceHandling** (3 tests)
   - Threshold triggering
   - Forced liquidation
   - Normal trading

8. **TestPortfolioValue** (3 tests)
   - Value calculations
   - Consistency checks

9. **TestEdgeCases** (5 tests)
   - Extreme scenarios
   - State invariants
   - Rapid trading

**Total: 31 tests**

All should pass ✅

---

## Clean Up When Done

```bash
# Deactivate virtual environment
deactivate

# Optionally remove it
rm -rf test_env_clean
```
