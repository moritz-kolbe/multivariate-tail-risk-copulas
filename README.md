# multivariate-tail-risk-copulas
📄 [View rendered analysis](https://moritz-kolbe.github.io/multivariate-tail-risk-copulas)

Multivariate tail risk analysis of a ten-asset portfolio using vine copula
and Gaussian copula models, with semiparametric marginal distributions fitted
via Peaks-over-Threshold (GPD).

## Motivation

A Gaussian copula assumes asymptotic independence in the tails: conditional on
one asset experiencing an extreme loss, the probability that another does too
converges to zero as the threshold increases. This project fits a vine copula to
empirically capture tail dependence across a mixed equity-commodity portfolio and
quantifies how much the Gaussian assumption underestimates portfolio tail risk.

## Methodology

1. **GJR-GARCH(1,1,1)** pre-filtering on log-returns for each asset to remove
   conditional heteroskedasticity and extract approximately i.i.d. standardised
   residuals
2. **POT/GPD** estimation on standardised residuals (threshold: 95th percentile)
   via MLE using `pyextremes`
3. **Semiparametric PIT**: empirical CDF in the body, GPD-based CDF in the tail,
   continuously stitched at the threshold
4. **Vine copula** (Dissmann et al., 2013) fitted to pseudo-observations via
   `pyvinecopulib`; Gaussian copula as benchmark, estimated via Kendall's τ
5. **Monte Carlo simulation**: 10-day forward paths via GARCH recursion and
   inverse PIT; R = 100 replications of 100,000 simulated paths each (10 million
   paths total); Common Random Numbers (CRN) used across copula specifications
   to isolate model differences from sampling noise
6. **VaR and ES** estimated by cumulating losses along each simulated path before
   taking tail quantiles

## Data

Ten assets in USD (2014–2026): NVDA, GOOGL, JPM, WMT, XOM (US equities) and
CL=F, SI=F, ALI=F, GC=F, HG=F (commodity futures). All denominated in USD to
avoid currency risk.

## Main Results

The vine copula produces higher 10-day 99% tail risk estimates than the Gaussian
copula in **100% of simulation runs** across R = 100 independent replications.
The mean paired difference is 0.0033 for VaR (~4% relative) and 0.0066 for ES
(~6% relative). The larger ES gap reflects that tail dependence amplifies the
magnitude of joint exceedances — an effect ES captures but VaR does not.

## How to Run

```bash
pip install -r requirements.txt
quarto render Copula_Analysis.qmd
```

## Requirements

```
yfinance arch scipy numpy pandas matplotlib seaborn pyextremes pyvinecopulib joblib 
```

Quarto must be installed separately: [quarto.org/docs/get-started](https://quarto.org/docs/get-started/)
