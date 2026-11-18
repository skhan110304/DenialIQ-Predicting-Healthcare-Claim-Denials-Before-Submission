# DenialIQ: Economic-Aware Single AI Agent (portable reproduction)

This project extends the original **DenialIQ** synthetic denial-prediction notebook with a post-denial experimental decision agent. It uses an economic calculator, a structured evidence-gap checker, and synthetic payer-rule lookup, all accessible via one OpenAI LLM's tool-calling interface. This is **administrative research**, not an actual claim appeal or clinical coverage decision.

## Files

- `DenialIQ_Economic_Agent_PORTABLE.ipynb`: the notebook to run from a fresh computer, including the original XGBoost baseline and the agent extension.
- `predict-claim-denials-before-submission.ipynb`: original DenialIQ notebook from the upstream repository (optional reference).
- `data/`: unchanged DenialIQ CSVs (see next section).
- `requirements.txt`: Python dependencies.
- `reference_six_claim_decisions.csv`: **previously observed** six-case decisions from the Kaggle experiment, not fresh live LLM predictions.
- `results/`: output directory created by the notebook for a new run; ignored by Git.

## Dataset setup

Put the following files **directly in `data/`** (not the repository root):

```
data/
  claims_main.csv
  train_test_split.csv
  denial_labels.csv
  payer_rules (1).csv
```

The originals are from [DenialIQ: 120K Medical Claims | X12 Denial Codes](https://www.kaggle.com/datasets/nudratabbas/denialiq-120k-medical-claims-x12-denial-codes). Do not manually re-split or modify the CSVs. The original `train_test_split.csv` has named `train`, `test` and `val` rows. The agent development code includes `train` and `test` and withholds `val`; the six-case demonstration uses training claims only.

If `data/` isn't included in your GitHub fork, download the dataset from Kaggle and copy those four files into this folder before running. Check the dataset's redistribution license before publishing original CSV copies.

## Fresh Windows computer: exact setup

1. On a **computer never used for development**, download the GitHub repository via **Code → Download ZIP** and extract it. Use a computer where Python 3.11 or 3.12 and installing packages are permitted.
2. Open **PowerShell** in the extracted repository folder. You can open a terminal from the folder's address bar or use `cd` to reach it.
3. Run:

```powershell
py -3 -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m jupyterlab
```

4. Jupyter opens in your browser. Open **`DenialIQ_Economic_Agent_PORTABLE.ipynb`** and select the newly installed Python kernel (usually `.venv` or Python 3).
5. Confirm `data/` contains the CSVs, and that you started Jupyter **from the repository root**, then choose **Run → Run All Cells**. One cell asks privately for a valid **OpenAI API key** when an `OPENAI_API_KEY` environment variable is not set. The key is not included in GitHub.
6. Do not show or read out the key during a Zoom recording. The API requires internet and may require credits. The notebook makes multiple model requests; avoid unnecessary re-runs.
7. Verify the printed claim counts, economic/evidence/rule tool outputs, three live LLM tool calls for the example, and six-claim comparison. Outputs are written under `results/`.

If `py -3` is not available, install a supported Python and use its corresponding `python` command. On locked-down library computers, check whether the university's IT policy allows installing Python packages before recording. Do not claim this environment is verified until a clean execution succeeds.

## Mac or Linux setup

From the downloaded repository root:

```bash
python3 -m venv .venv
.venv/bin/python -m pip install --upgrade pip
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m jupyterlab
```

## Expected results and interpretation

The intact dataset contains **120,000 claims** and **120,000 predefined split rows**. The `train`+`test` development subset contains **101,982 claims**, including **28,699 denied claims**. The original `val` split is reserved for final evaluation.

The prior **unseeded** Kaggle XGBoost test result was **ROC AUC 0.4997**. This portable edition sets `np.random.seed(42)` and a model seed to support repeatability. **Its AUC can differ from 0.4997**; report the newly observed value and do not claim exact numerical reproduction of the old unseeded metric. This XGBoost model predicts initial denial, not appeal success.

The original six-claim *Kaggle* demonstration reported:

| Method | Prioritize appeal | Request evidence | Do not prioritize |
|---|---:|---:|---:|
| Economic-only | 4 | 0 | 2 |
| Evidence-aware rule | 0 | 6 | 0 |
| LLM agent | 0 | 5 | 1 |

The LLM agreed with economic-only in **1/6 cases (16.7%)** and evidence-aware rules in **5/6 cases (83.3%)**. These are **behavioral agreement results**, not accuracy, recovered dollars, or proven improvement. The agent was prompted to call all three tools. `reference_six_claim_decisions.csv` records **prior observed decisions only**. The notebook performs **fresh live API calls**, whose actions and counts may differ across runs; describe any differences transparently. Exact repeatability of stochastic hosted model output is not guaranteed.

Economic assumptions: appeal cost **$150**, documentation-gap threshold **0.70**, synthetic appeal probabilities from DenialIQ. These do not represent observed administrative costs or clinical coverage rules. The initial threshold flagged 96.44% of denied training cases and is a sensitivity-analysis limitation.

## Privacy and troubleshooting

- Never commit API keys, `.env` files, or saved stack traces containing tokens. The notebook outputs were cleared for this portable edition, including an old Kaggle traceback containing a **revoked** key.
- If data are missing, verify `data/` sits alongside the notebook and includes all four required CSVs.
- If `ModuleNotFoundError` occurs, ensure `requirements.txt` was installed in the notebook's Python environment.
- If an OpenAI API call fails, check internet access, key validity, available API credits, and model availability; don't include secrets in screenshots or bug reports.
- The first SHAP run may take more time and memory. For a controlled comparison, use the same Python/package versions on subsequent runs.

## Research limitations

The source code is based on [DenialIQ](https://github.com/NudratDS/DenialIQ-Predicting-Healthcare-Claim-Denials-Before-Submission), rather than direct numerical reproduction of HICRIC. The project uses synthetic data and currently has a six-claim agent demonstration. No independently simulated actual recovery outcomes have yet been validated. The professor's paper-replication rubric should be addressed separately if it requires HICRIC's actual benchmark.
