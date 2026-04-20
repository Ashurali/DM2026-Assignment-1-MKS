# Assignment 2 — Work Plan

**Deadline:** May 7, 11:59 PM
**Submission:** `DM_asg2_314540066.pdf` + public GitHub repo (link inside report)

---

## Q1 — K-fold Cross-Validation (15 pts) — *Real_World_Classification.ipynb*

- [x] **Q1(a)** 5-fold CV on training data, 4×4 grid (lr × λ), L2 reg, table of avg accuracy.
- [x] **Q1(b)** Top-2 settings (CV winners: `lr=0.01, λ=2.0` and `lr=0.01, λ=4.0`) trained on training set, evaluated on test set with `evaluate_binary_classifier`.
- [x] **Q1(b)-extension** Added cell evaluating all 4 tied configs at CV=0.7257 (`lr=0.005,λ=4`, `lr=0.01,λ=4`, `lr=0.1,λ=4`, `lr=0.1,λ=2`) — to investigate whether λ=4 gives a consistent boost. *(needs execution)*
- [ ] **Q1(c)** Observations write-up (for report):
    - **User observations:**
        - Overall accuracy higher than Asg1 best (lr=0.1, λ=1) experiment.
        - Big recall increase across all configs (even 2nd-best) → mostly attributable to the learning-rate jump from 0.01 baseline.
        - 4-way tie at CV=0.7257: λ=4 takes 3 slots (lr={0.005, 0.01, 0.1}) + λ=2 at lr=0.1 → λ=4 looked like it gives a consistent CV boost.
        - "Slight lr increase doesn't move accuracy much, but recall jumps a lot" — recall is the lr-sensitive metric here, not accuracy.
    - **Test-set observations after running the tied configs:**
        - CV-picked "Top 2" (`lr=0.01, λ=4`) is actually the *worst* of the tie on test (Acc 0.74) — CV ranking did not transfer.
        - `lr=0.01, λ=4` and `lr=0.1, λ=4` produce **identical** test metrics → same converged minimum; lr only controls speed here, not destination.
        - λ=2 beats λ=4 on the test set (Top 1 = 0.76 vs 0.74) → λ=4's apparent CV boost is **CV-noise**, not real generalization gain.
        - `lr=0.005, λ=4` gives the **highest recall (0.8289)** in the entire experiment → slower lr + stronger reg pushes the decision boundary to predict positive more readily.
        - λ=8 / lr=0.5 cells collapse → over-regularization or step-size divergence.

---

## Q2 — SVM (15 pts) — `mobile_price.csv`, **new notebook** `Mobile_Price_SVM.ipynb`

- [ ] Load `data/mobile_price.csv`. Shuffle with `random_state=42`. Split 60/20/20 train/val/test using `train_test_split`.
- [ ] **Q2(a)** Train `sklearn.svm.SVC(C=1.0)`. Report accuracy + F1 on train/val/test.
- [ ] **Q2(b)** Loop C ∈ {0.001, 0.01, 0.1, 1, 10, 100, 1000, 10000}. Plot accuracy and F1 vs log(C) for all three splits (line plot, two subplots).
- [ ] **Q2(c)** Pick C with best validation generalization (val_acc highest, small train-val gap). Justify in report.
- [ ] Save charts to `chart/Q2_*.png`.

---

## Q3 — Association Rule Mining via FP-growth (15 pts) — same dataset, **new notebook** `Mobile_Price_FPgrowth.ipynb`

- [ ] Filter rows with `price_range == 1`.
- [ ] For features `{ram, int_memory, px_width, battery_power}`:
    - Compute `min`/`max`, split range with **3:4:3** ratio → `low / medium / high`.
    - Convert each row to transaction list `["ram_high", "int_memory_low", ...]`.
- [ ] Install `mlxtend` if needed. Use `mlxtend.frequent_patterns.fpgrowth` and `association_rules`.
- [ ] **Q3(a)** Frequent patterns with `support ≥ 0.3` → table.
- [ ] **Q3(b)** Association rules with `support ≥ 0.3, confidence ≥ 0.4, lift ≥ 0.8` → table.
- [ ] **Q3(c)** Observations: which feature buckets co-occur in mid-priced phones; lift > 1 vs lift ≈ 1 interpretation.

---

## Q4 — PCA + K-Means (20 pts) — same dataset, **new notebook** `Mobile_Price_PCA_KMeans.ipynb`

- [ ] Split features (X) / label (`price_range`). Standardize with `StandardScaler`.
- [ ] **Q4(a)** Fit `PCA(n_components=2)`, scatter plot colored by class label, with legend. Save `chart/Q4a_pca_scatter.png`.
- [ ] **Q4(b)** `KMeans(n_clusters=4)` on **all standardized features**. Scatter on the PCA-2D projection colored by predicted cluster, with legend. Report `adjusted_rand_score` vs true labels.
- [ ] **Q4(c)** `KMeans(n_clusters=4)` on the **2-D PCA features**. Scatter + legend + ARI.
- [ ] **Q4(d)** Observations: ARI difference between (b) and (c); information loss from PCA-then-cluster vs cluster-in-full-space.

---

## Q5 — Enhancing K-Means with Association Rule Mining (35 pts) — *biggest weight*, **new notebook** `Mobile_Price_KMeans_Plus_ARM.ipynb`

Brainstorming directions to discuss before coding:

1. **Rule-based feature engineering** — discretize all features (like Q3), mine frequent itemsets / rules per (tentative cluster, label) pair, encode each sample with a binary indicator vector "matches rule i?", concatenate to standardized features, then K-Means.
2. **Rule-guided initialization** — find frequent itemsets that co-occur with each `price_range` value, use the centroid of samples matching the strongest rule per class as the K-Means seed (instead of `k-means++` randomness).
3. **Rule-weighted distance** — give higher weight to features that appear in high-lift rules (those features carry stronger class signal).

- [ ] Pick one approach (Option 2 or a hybrid is most defensible) and document the inspiration (cite e.g. "association-rule-guided clustering" papers, or honestly "inspired by Q3 and class lecture on rule mining").
- [ ] Implement baseline K-Means + improved K-Means.
- [ ] Loop `random_state ∈ {0, 10, 42, 100, 999}`, average accuracy / precision / recall / F1 (use Hungarian / `linear_sum_assignment` to map cluster id → label, since K-Means labels are unordered).
- [ ] Report comparison table + framework figure.

---

## Report (`DM_asg2_314540066.md` → PDF)

- [ ] Same compact format as `DM_asg1_314540066.md`.
- [ ] Embed charts via relative paths.
- [ ] Include GitHub link at top.
- [ ] Convert to PDF (Pandoc or VSCode print).

---

## Repo Hygiene

- [ ] Commit before deadline.
- [ ] Update `README.md` with Asg2 instructions to run.
- [ ] Verify all notebooks run top-to-bottom on a clean kernel.
