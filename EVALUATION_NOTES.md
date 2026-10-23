# Evaluation and reproducibility notes

## What is directly supported by the original notebook

- The original data load reports **902 records**. After score and language filtering the notebook records **893** English/Malay reviews; after duplicate handling it records **777** rows.
- The final class-weighted LSTM evaluation (original cell 234) reports test **accuracy 0.9103** and test **loss 0.2444** on **156** samples.
- The classification report (original cell 237) prints **0.90** macro F1 after rounding to two decimal places.
- Misclassification analysis (original cell 255) reports **14 / 156** errors, approximately **8.97%**.
- The historical checkpoint is `best_weighted_lstm_model.h5`, saved by the original notebook after the weighted-LSTM experiment.

## Limitations to disclose

1. **Tokenizer leakage:** Original cell 87 executes `tokenizer.fit_on_texts(df["Clean_Review"])` before the train/test split at cell 99. This exposes the fitted vocabulary and token frequencies to test-set texts. This is unsupervised leakage and undermines claims of a fully independent evaluation.
2. **Model selection / repeated test use:** The notebook explores multiple architectures and choices on the same held-out split. Thus the final selected metric may be optimistic compared with a fresh, untouched holdout.
3. **Translation reproducibility:** Malay reviews were machine-translated and cached. The cache and original coursework CSV are not distributed. Future translations may differ.
4. **Saved model usability:** The `.h5` file contains a Keras model, but the separately fitted original tokenizer and label encoder were not exported as a complete inference pipeline. Loading this file alone is insufficient for raw-text sentiment inference.
5. **No independent rerun:** The portfolio preparation used the original notebook outputs and saved artifact; the original dataset was unavailable, so no corrected model was retrained or separately validated.
6. **Split and label decisions:** Scores were converted to sentiment using `Score < 4` → Negative, `4 <= Score < 7` → Neutral, and `Score >= 7` → Positive. This is a task-specific proxy label definition, not independently annotated sentiment ground truth.

## What to improve in a follow-up version

- Deduplicate and define the final evaluation protocol **before** fitting preprocessing on any textual data.
- Establish separate **train**, **validation**, and **test** splits.
- Fit tokenizer and other learned preprocessing steps on the **training** partition only, then transform validation and test partitions.
- Select the architecture using only the validation split; report final test metrics only once after model selection.
- Save the trained model **plus** tokenizer configuration, label mapping and preprocessing function in a versioned inference bundle.
- With publication permission, provide an appropriately licensed public dataset or a synthetic example schema to improve reproducibility.

The repository README deliberately describes the **recorded**, not fully independent, 91.03% evaluation result. The provided notebook is a historical source record with code outputs removed for safe portfolio publication.
