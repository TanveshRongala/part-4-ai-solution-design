# AI Solution Design Report

## Task 1: Choose a Business Domain
**Selected Domain:** Insurance

## Task 2: Define the Business Problem
* **Problem being solved:** The insurance company faces challenges in efficiently identifying fraudulent claims. Currently, manual investigations bottleneck the processing of legitimate claims, leading to poor customer experiences and high operational costs.
* **Users/Stakeholders:** Claims adjusters, the Fraud Investigation Unit (FIU), insurance executives, and policyholders.
* **Current manual/traditional process:** Claims are manually reviewed by adjusters or passed through a rigid, simple rules-based engine. Suspicious claims are then forwarded to the FIU for a lengthy manual investigation.
* **Limitations of the current process:** * *High Effort & Delays:* Based on recent KPI data, the team spends over 500 manual processing hours a month, with an average resolution time peaking at 44.7 hours (March 2025).
  * *High Error Rate:* The manual process is error-prone, with error rates reaching up to 11.16% (May 2025), meaning fraud is missed or legitimate claims are unnecessarily delayed. 
  * *Customer Dissatisfaction:* The slow resolution time negatively impacts the customer satisfaction score, which hovered around 6.4 - 6.8 out of 10 in early 2025.

## Task 3: Identify the AI Task Type
* **AI Task Type:** Classification (specifically, Binary Classification) / Anomaly Detection.
* **Why it is suitable:** The core objective is to categorize an incoming insurance claim into one of two distinct classes: "Legitimate" (0) or "Fraudulent" (1). Anomaly detection techniques can also be layered in to flag highly unusual claim patterns that deviate from normal baseline behaviors, even if they don't match historical fraud patterns.

## Task 4: Data Requirement Plan
* **Type of data needed:** Historical insurance claims, customer policy details, and submitted evidence.
* **Structured or unstructured data:** Both. 
  * *Structured:* Claim amounts, dates, policyholder age, location, past claim counts.
  * *Unstructured:* Claim description text, adjuster notes, and scanned PDF documents/images of receipts or damage.
* **Input features:** Claim amount, time since policy inception, previous claims history, discrepancy flags, and sentiment/keywords from adjuster notes.
* **Target variable or labels:** `is_fraud` (Boolean: True/False or 1/0), labeled by historical post-investigation outcomes.
* **Data collection method:** Extraction from the company's central Claims Management System (CMS) and historical FIU investigation databases.
* **Data quality risks:** * *Class Imbalance:* Fraudulent claims typically make up a very small percentage of total claims, which can bias the model toward predicting "Legitimate" every time.
  * *Incomplete Data:* Missing fields in older claims or poorly scanned documents.

## Task 5: Model Recommendation
* **Recommended Model:** Feed-forward neural network (for structured tabular data) combined with a Gradient Boosting algorithm (e.g., XGBoost) for tabular feature importance. If unstructured text notes are heavily relied upon, a hybrid approach using a Transformer-based NLP model (like BERT) to extract embeddings from the text to feed into the neural network.
* **Why it is appropriate:** Feed-forward neural networks and gradient boosting models are highly effective at finding complex, non-linear patterns in structured tabular data (which makes up the bulk of insurance claims data). They can process thousands of features quickly and output a probability score, allowing adjusters to rank claims by their "fraud risk score."

## Task 6: Evaluation Plan
* **Technical metrics:** * *Recall (Sensitivity):* Most important, as we want to capture as many fraudulent claims as possible (high fraud catch rate). 
  * *Precision:* Important to ensure we don't overwhelm investigators with false positives. 
  * *F1-Score:* To balance precision and recall.
* **Business metrics:** * Reduction in `manual_processing_hours` (Targeting < 300 hours/month).
  * Decrease in `average_resolution_time_hours` for legitimate claims.
  * Improvement in `customer_satisfaction_score` (Targeting > 8.0).
* **Possible failure cases:** The model encounters a completely novel type of fraud (data drift) and fails to flag it, or seasonal spikes (e.g., storm season) cause erratic predictions.
* **Human review/validation process:** The AI will act as a *triage* system. It will auto-approve low-risk claims and flag high-risk claims for the FIU. The AI will **never** automatically reject a claim.

## Task 7: Responsible AI Considerations
* **Bias in data:** The model might inadvertently learn to flag claims from specific zip codes or demographic groups as fraudulent due to historical biases in manual investigations.
* **Incorrect predictions:** False positives (flagging a legitimate claim as fraud) will delay payouts to vulnerable customers who desperately need funds after an accident or disaster.
* **Privacy concerns:** Models will ingest highly sensitive Personally Identifiable Information (PII) and potentially medical records. Data must be anonymized before training.
* **Over-reliance on AI:** Investigators might succumb to "automation bias," blindly trusting the AI's fraud score and failing to properly investigate claims themselves.
* **Need for human oversight:** A strict "Human-in-the-Loop" (HITL) policy must be enforced. Any claim denial must be signed off by a human adjuster, and the model must provide explainability (e.g., SHAP values) showing *why* it flagged a claim.

---

## Task 8: Final Solution Summary

### Executive Summary: AI-Powered Claim Triage & Fraud Detection

**Problem:** The claims department is overwhelmed by rising monthly caseloads (reaching over 3,000 cases/month). Manual reviews lead to high resolution times (~35 hours), high operational costs, and lower customer satisfaction, while sophisticated fraud still slips through.

**Proposed AI Solution:** Implement an AI-driven triage system using a Binary Classification/Anomaly Detection model. The system will score incoming claims in real-time. Low-risk claims are fast-tracked for payment, while high-risk claims are routed to human investigators with an attached "fraud probability score" and reasoning.

**Required Data:** Historical structured claim data (amounts, customer history) and unstructured data (adjuster notes) extracted from the Claims Management System, supervised by historical `is_fraud` labels. 

**Model Recommendation:** A Feed-Forward Neural Network (paired with gradient boosting for structured data). This architecture scales well with tabular financial data and outputs reliable probability distributions for risk ranking.

**Expected Business Impact:** * **Cost Efficiency:** Projected 40% reduction in `manual_processing_hours`.
* **Speed:** Decrease `average_resolution_time_hours` for legitimate claims to under 20 hours.
* **Customer Success:** Expected lift in `customer_satisfaction_score` as valid claims are paid out faster.

**Risks and Mitigation Plan:**
* *Risk:* Algorithmic bias leading to unfair treatment of genuine claimants from specific demographics.
  * *Mitigation:* Remove PII and protected class variables during training. Conduct regular fairness audits.
* *Risk:* False positives delaying urgent payouts.
  * *Mitigation:* The AI cannot auto-reject. It only routes to humans. Explainable AI (XAI) techniques will highlight the exact features that triggered the flag to speed up human verification.
