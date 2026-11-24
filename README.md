# Exploring the FlakyLens artifact

Reproduction and dataset checks for *Understanding and Improving Flaky Test Classification* (Rahman, Dutta, Shi, OOPSLA 2025).

## Contents
- `Flakylens_exp.ipynb` – Colab notebook (T4 GPU, no Docker): reproduction, formatting analysis, perturbation experiments, IDoFT cross-check, verificaton checks.
- `results/` – CSV outputs of the main experiments.
- `nondex/` – NonDex logs for Mapper `ComplexEntityTest#test`.

## How to run
**Notebook:** open in Google Colab. The fine-tuned models are not in the FlakyLens GitHub repo; download them from the authors' Box folder (link in `models/README.md` of the FlakyLens repo) into Google Drive at `MyDrive/flakylens_models/`.

**NonDex** (GitHub Codespaces, OpenJDK 1.8, Mapper commit `1764748e`, module `core`):

```
mvn -pl core test -Dtest=ComplexEntityTest#test
mvn -pl core edu.illinois:nondex-maven-plugin:2.2.1:nondex -Dtest=ComplexEntityTest#test -DnondexRuns=10
mvn -pl core edu.illinois:nondex-maven-plugin:2.2.1:debug -Dtest=ComplexEntityTest#test
```

## Observations
- Predictions match the authors' saved results on all four folds (8,574/8,574).
- In FlakeBench, non-flaky tests end with a newline and flaky tests do not; adding one trailing newline changes 56/280 flaky-test predictions to non-flaky.
- 129 non-flaky tests match tests listed as flaky in IDoFT (name-based matching; a few checked by hand).
- NonDex: `ComplexEntityTest#test` passes normally but fails 10/10 shuffled runs; NonDex debug points to `Class.getDeclaredFields()` in Mapper's `FieldHelper`.

## Limitations
Name-based IDoFT matching; one NonDex case examined in depth; no retraining on normalized data; iDFlakies not run.

## Credit
FlakyLens code excerpts shown in the notebook outputs are from the authors' artifact: https://doi.org/10.5281/zenodo.15761937 (CC BY 4.0) · https://github.com/UT-SE-Research/FlakyLens
