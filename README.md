# The Effectiveness of Twitch Moderation Policies

Innovation and Marketing Analytics course project by **Group 14** at Bocconi University (2024).

The project asks two questions. How did the ratio of bans to audience activity change around Twitch policy dates? How well could the text of an unban request predict whether a moderator would approve it? This repository preserves the course notebooks with the closest evidence of use in the submitted report and two anonymised collection notebooks. The three analysis notebooks open with verified aggregate results. The appeals notebook includes a static chart of classifier accuracies. Datasets, prediction files and original cell outputs are held privately.

## Course report

[**The Effectiveness of Twitch Moderation Policies** (PDF)](report/course-report-public.pdf) is the public copy of Group 14's original course submission. Creator names were replaced with labels A–D. Figures with channel-level time series, moderator activity or individual appeals were omitted, including the graphical appendix. The submitted statistical table keeps its original values. See [Execution notes](#execution-notes) for differences found in the local rerun.

## Which notebooks match the report

Cell numbers below refer to the course source notebooks, including Markdown cells. In each public analysis notebook, one results cell precedes that sequence.

| Notebook | Course source | Link to the final work |
| --- | --- | --- |
| [`01_policy_before_after.ipynb`](notebooks/01_policy_before_after.ipynb) | `Dataset_description_causation (2).ipynb` | Most complete policy-analysis copy. It covers all four creator blocks, including the block left incomplete in version `(1)`. Cells 5 and 7 prepare and describe the data. Cells 9 and 10 define the rolling bands and the policy-date comparisons. Cells 20, 30, 40 and 50 report those comparisons when run. |
| [`02_appeal_text_classification.ipynb`](notebooks/02_appeal_text_classification.ipynb) | `Unban_requests_analysis (5).ipynb` | Cells 5–6 clean the text and sample denied appeals within creators. Cell 10 matches the report's word-frequency figure. Cells 12 and 15 build TF–IDF features and set up the split. Cells 16, 18, 20, 22 and 24 contain the archived model results. Cell 26 generates the interactive PCA plot with class filters. Version `(4)` has the same substantive code and saved results. |
| [`03_chat_users_estimation.ipynb`](notebooks/03_chat_users_estimation.ipynb) | `code.ipynb` | Supporting notebook. Cells 1–4 train a linear regression for chat-user counts, despite the original variable name and output label referring to random forest. Cells 5–8 apply it to another creator's stream metrics and export the historical chat-user predictions. The final merge into the policy analysis is undocumented. |

For reading, start with `01` and `02`. They cover separate analyses. `03` documents an upstream estimation step. Its export is an intermediate file and does not replace the audience input expected by `01`.

The unnumbered policy notebook is a prototype without Welch tests. Policy version `(1)` is an intermediate copy with an unfinished fourth creator block and a manually assembled table. The unnumbered appeal notebook and appeal version `(2)` use a different input and omit the within-creator balancing used in the final workflow. Appeal version `(3)` switches between workbooks and contains a saved error. Of the two API variants, one representative is included in anonymised form.

## Findings and limits

### Bans around policy dates

The policy notebook compares daily bans relative to average viewers, unique viewers and chat users. The Bollinger plots use 20 observations and a width of four standard deviations. For 5 March 2020, 22 January 2021 and 1 September 2023, the notebook uses unequal-variance t-tests on the 30 calendar days before and after the date, excluding the policy day. The original preparation replaces undefined or infinite ratios with zero.

The September 2023 comparisons for the bans-to-average-viewers ratio include increases and decreases across creators. The workflow is an observational before/after comparison. No control series or adjustment for time dependence is shown. The submitted PDF and the saved notebooks disagree on some p-values, including one September 2023 result with the same pre/post means. The surviving notebooks establish the analysis lineage while leaving parts of the PDF's final table unresolved.

### Classification of unban requests

The five historical test accuracies are in the saved original outputs of `(5)`. The course report mentions a majority baseline of about 57%. The representative notebook has no saved baseline result for its own split.

The original classifier notebook uses Italian stopwords, keeps appeals with more than five preprocessed tokens and builds up to 128 TF–IDF features using one- to three-word sequences. It fits TF–IDF before the 80/20 split. It also samples denied requests with replacement when a creator has too few of them, before splitting. The archived scores are exploratory and should not be used as leakage-free validation. The word chart sorts ranks separately by class and labels both series with the approved class's words. The PCA filter buttons in the saved version swap the two class traces. These issues limit the interpretation of the report's visuals.

## Open the notebooks locally

Python `3.9.16` is recorded in the two main notebooks and `3.8.15` in the supporting notebook. [`requirements.txt`](requirements.txt) lists dependencies inferred from imports. The course files have no version lock. Start Jupyter from the repository root in an environment with the listed packages.

```sh
python -m pip install -r requirements.txt
python -m nltk.downloader punkt punkt_tab stopwords wordnet
jupyter notebook
```

The appeals notebook expects the NLTK resources to be installed in advance. Its Plotly import is loaded in the first cell for execution from a clean kernel.

These notebooks require private course inputs. If you use the default location inside the checkout, create `data/private/` and its subdirectories locally. Set `TWITCH_MODERATION_DATA` to a private location outside the checkout if preferred, with the same `policy/`, `appeals/` and `support/` subdirectories. The neutral creator labels A–D correspond to the original blocks in order.

| Original input cell | Paths relative to `data/private/` | Input availability |
| --- | --- | --- |
| Policy 13 | `policy/audience_a.csv`, `policy/bans_a.xlsx`, `policy/appeals_a.xlsx` | The appeals workbook is in the course folder and the bans workbook was found in an older archive. The original audience CSV remains unavailable. |
| Policy 23 | `policy/audience_b.xlsx`, `policy/bans_b.xlsx`, `policy/appeals_b.xlsx` | All three inputs were found under the expected names in the older archive. |
| Policy 33 | `policy/audience_c.xlsx`, `policy/bans_c.xlsx`, `policy/appeals_c.xlsx` | The moderation workbooks are in the course folder. The prepared audience workbook remains unavailable. The raw stream table has a different schema. |
| Policy 43 | `policy/audience_d.xlsx`, `policy/bans_d.xlsx`, `policy/appeals_d.xlsx` | All three inputs were found under the expected names in the older archive. The merged audience workbook was recovered directly. |
| Appeals 3 | `appeals/requests.xlsx` | Present as `NLP.xlsx`. The notebook uses `text`, `status` and `broadcaster_login`. |
| Chat-user estimation 1 and 5 | `support/training_audience_b.xlsx`, `support/stream_metrics_d.xlsx` | The exact training filename is unavailable. The older audience workbook used for Policy 23 has the required columns and reproduces the archived training diagnostics. The stream metrics workbook is present under its original name in the course folder. Cell 8 writes `support/chat_users_predictions_d.csv`. |

Use the original load cells as the cross-reference for private filenames. The availability of the exact historical inputs limits direct reproduction. See the execution notes below.

## Collection notebooks

- [`01_twitch_moderation_api.ipynb`](notebooks/collection/01_twitch_moderation_api.ipynb) documents historical collection of ban records and unban requests. It uses locally supplied Twitch application, redirect and broadcaster settings. Its additional dependency is `requests`.
- [`02_twitchtracker_stream_metrics.ipynb`](notebooks/collection/02_twitchtracker_stream_metrics.ipynb) documents historical collection of stream metadata. Its additional dependencies are `selenium` and a local Firefox/WebDriver setup.

Both notebooks clear credentials, real creator identifiers and saved outputs from the public copies. They write any explicitly generated exports to the private collection directory. The collection calls are commented out and were not executed during the verification.

## Execution notes

The analysis was checked locally with Python 3.12.2 and private course inputs.

- **Policy comparisons.** Blocks B and D ran with the recovered audience and moderation files. Blocks A and C used the daily audience series saved in the original notebook figures and the surviving moderation workbooks. The original audience files for A and C remain unavailable. All 36 rows matched the original notebook outputs to their displayed precision. Eleven of the 30 rows printed in the submitted report differ beyond rounding. The two September 2023 comparisons with `p < 0.05` agree in direction. For the decrease in block D, the rerun gives `p = 0.001712` and the report gives `p = 0.007712`.
- **Appeal classifiers.** Random Forest, SVM and XGBoost matched their saved accuracies. Logistic regression differed by one correct prediction on the 348-row test set. The neural network has no fixed seed and scored 57.47%, compared with 60.34% in the archived output. The rerun scores ranged from 54.60% to 57.47%. On this split, always predicting the majority class gave 51.44% accuracy. The report gives approximately 57% for its baseline.
- **Chat-user estimation.** The recovered training workbook reproduced the saved mean squared error and R² (0.84242), and the rerun matched all ten rows displayed in the original notebook's preview. The generated export had the same 1,438 input rows as the archived CSV, but the CSV contained different predictions. The notebooks do not document the merge that used this export.

## Publication copies

The original unredacted report, slides, account-specific exports, raw appeals and original collection notebooks with authentication details remain private. Original cell outputs and execution counts were removed, so the public copies show empty execution prompts. The public copies use neutral creator labels and relative private-data paths. Raw table previews were removed, and generated charts, vocabulary files and predictions now write to the private data directory. The analysis copies retain the original computations. Keep any newly generated plots and row-level outputs private when working with the source data.
