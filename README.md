# Decision Support System for SME Credit Risk

This project presents a hybrid Decision Support System (DSS) for evaluating credit applications from Small and Medium-sized Enterprises (SMEs). It combines a transparent, model-driven credit score with a data-driven machine-learning prediction of default probability.

The system is designed to make lending decisions more objective, consistent, efficient, and easier to explain. It evaluates financial health, payment behavior, and sector-specific risk, then recommends a decision and lending terms.

## Project Contents

- `project.ipynb`: Project notebook containing the analysis and DSS implementation.
- `dataset/cs-training.csv`: Historical credit-training data used by the data-driven component.
- `Report.pdf`: Full project report.

## Problem Statement

Manual SME credit assessment can be slow, inconsistent, and affected by subjective bias. The DSS addresses the following question:

> How can financial institutions evaluate SME credit applications systematically to reduce default risk while maintaining competitive lending rates?

The system supports credit officers, risk managers, bank management, SME applicants, and regulators by providing structured and data-informed recommendations.

## DSS Architecture

The system follows this flow:

```text
Input data -> Normalization -> Weighted scoring -> ML prediction -> Decision matrix -> Lending recommendation
```

| Component | Purpose |
| --- | --- |
| Data input layer | Receives financial and behavioral application data |
| Normalization engine | Scales numerical inputs to the `[0, 1]` range |
| Scoring engine | Calculates a weighted financial health score |
| ML prediction layer | Predicts the probability of default |
| Decision matrix | Converts risk level into a business decision and terms |
| Output layer | Returns approval status, rate, collateral, loan limit, and review period |

## Input Variables

- **Liquidity ratio**: Current assets divided by short-term liabilities; measures short-term solvency.
- **Debt ratio**: Total debt divided by total assets; measures leverage and financial risk.
- **Operational cash flow**: Monthly cash generation, ranging from $0 to $500,000 in the report's definition.
- **30-day payment delays**: Number of payment delays between 30 and 59 days.
- **90-day payment delays**: Number of payment delays of 90 days or more.
- **Sector of activity**: Industry classification such as technology, manufacturing, services, retail, or construction.

The payment variables are combined into a normalized payment-history score:

```text
payment_history = 1 - (normalized_30d_delays + normalized_90d_delays) / 2
```

## Model-Driven Scoring

The financial health score uses the following weights:

| Factor | Weight | Role |
| --- | ---: | --- |
| Liquidity ratio | 25% | Short-term solvency |
| Debt ratio | 20% | Financial leverage |
| Operational cash flow | 20% | Ability to generate cash |
| Payment history | 20% | Payment discipline |
| Sector risk | 15% | Industry-specific adjustment |
| **Total** | **100%** | |

The score is calculated as:

```text
financial_score =
    0.25 * normalized_liquidity
  + 0.20 * (1 - normalized_debt)
  + 0.20 * normalized_cashflow
  + 0.20 * payment_history
  + 0.15 * sector_weight
```

Sector weights used in the report are:

| Sector | Weight | Rationale |
| --- | ---: | --- |
| Technology | 0.80 | High growth but potentially volatile |
| Manufacturing | 0.70 | Stable and established |
| Services | 0.65 | Moderate risk |
| Retail | 0.60 | Sensitive to economic cycles |
| Construction | 0.40 | Project-dependent and cyclical |

## Machine-Learning Component

The report compares four algorithms for predicting default probability:

- Random Forest
- Gradient Boosting
- Logistic Regression
- Support Vector Machine (SVM)

Random Forest is selected as the best-performing model, with an AUC of **0.8477**. Its predicted default probability complements the interpretable weighted score and helps capture non-linear relationships in historical credit data.

## Decision Matrix

The financial health score is mapped to a risk class and lending recommendation:

| Risk class | Score range | Decision | Interest rate | Collateral | Loan multiplier | Review period |
| --- | ---: | --- | ---: | --- | ---: | --- |
| Low risk | `>= 0.65` | Approve | 1.5% | Low | 2.5x revenue | Every 12 months |
| Medium risk | `0.40 - 0.65` | Review | 3.0% | Medium | 1.5x revenue | Every 6 months |
| High risk | `< 0.40` | Reject | 6.0% | High | 0.5x revenue | Every 3 months |

The final output contains the decision, interest rate, collateral requirement, loan multiplier, review frequency, and ML-predicted default probability.

## Assessment Process

1. Collect structured data from the loan application.
2. Normalize numerical financial and payment inputs.
3. Calculate the composite payment-history score.
4. Compute the weighted financial health score.
5. Classify the application as low, medium, or high risk.
6. Predict default probability using the trained ML model.
7. Map the risk class to the decision matrix.
8. Return the credit decision and associated lending terms.

## Hypothetical Company Simulations

### TechPros

TechPros is a technology company with strong liquidity, low debt, high cash flow, and no reported payment delays.

| Metric | Result |
| --- | --- |
| Liquidity ratio | 2.5 |
| Debt ratio | 0.15 |
| Operational cash flow | $250,000 |
| Financial health score | 0.898 |
| Risk class | Low risk |
| Estimated default probability | 15% |
| Decision | **APPROVE** |
| Interest rate | 1.5% |
| Collateral | Low |
| Loan multiplier | 2.5x |
| Review frequency | Every 12 months |

### BuildCo

BuildCo is a construction company with very low liquidity, high debt, minimal cash flow, and frequent payment delinquencies.

| Metric | Result |
| --- | --- |
| Liquidity ratio | 0.15 |
| Debt ratio | 0.95 |
| Operational cash flow | $2,000 |
| 30-day delays | 8 |
| 90-day delays | 5 |
| Financial health score | -1.016 |
| Risk class | High risk |
| Estimated default probability | 80% |
| Decision | **REJECT** |
| Interest rate | 6.0% |
| Collateral | High |
| Loan multiplier | 0.5x |
| Review frequency | Every 3 months |

The two simulations show that the DSS can distinguish strong and distressed borrowers and assign terms that reflect their risk profiles. The report also states that the broader system supports all three outcomes: approve, review, and reject.

## Sensitivity Analysis

The report varies the sector-weight parameter from 0.05 to 0.40 and observes the effect on average financial health scores. The relationship is approximately linear and has a measurable but moderate impact.

The main conclusions are:

- Sector risk is important but is not the dominant factor.
- Liquidity, debt, and cash flow remain the primary drivers of creditworthiness.
- Sector adjustments should complement financial fundamentals rather than override them.

## Limitations

### Data limitations

- Historical data may not represent emerging risks.
- The model focuses on quantitative variables and omits factors such as management quality and market position.
- Macroeconomic variables such as interest rates, GDP growth, and unemployment are not included.

### Model limitations

- Historical patterns may not hold during economic shocks.
- Static weights may not be optimal for every sector or time period.
- Historical training data may introduce bias against underrepresented groups.

### Operational limitations

- Fixed decision thresholds may need recalibration as the portfolio changes.
- Relationship history and guarantees are not currently considered.

## Future Improvements

The report proposes the following enhancements:

- Add macroeconomic indicators to capture systemic risk.
- Optimize weights dynamically by sector or time period.
- Add qualitative scoring for management capability and market position.
- Perform fairness audits and apply bias-mitigation techniques.
- Retrain models continuously as new data becomes available.
- Add scenario analysis and stress testing for adverse conditions.
- Integrate customer relationship and repayment history from a CRM system.
- Improve explainability so applicants can understand decisions.

## Conclusion

This project demonstrates a hybrid approach to SME credit-risk assessment. Weighted scoring provides transparency and interpretability, while machine learning adds predictive power for complex patterns. The simulations and sensitivity analysis support the use of sector risk as a secondary factor while keeping core financial fundamentals central to the decision. The proposed DSS offers a systematic foundation for more consistent, data-informed, and potentially fairer lending decisions, while requiring further validation, recalibration, and operational integration before production use.

## References

The report's main references include work by Altman on bankruptcy prediction, Mays on credit risk management, Saaty on the Analytic Hierarchy Process, Siddiqi on credit scorecards, Thomas on credit and behavioral scoring, and research on machine learning for corporate bankruptcy and credit-risk assessment.