# Tigrinya ASR Dataset Merger

This notebook combines three Tigrinya speech datasets into a single unified ASR corpus and pushes it to your Hugging Face Hub account.

  **The combined dataset is available on huggingface here:**  [https://huggingface.co/datasets/Harbidel/tigrinya-asr-merged]

## Dataset Sources

- [`badrex/tigrinya-speech`](https://huggingface.co/datasets/badrex/tigrinya-speech)
- [`google/WaxalNLP`](https://huggingface.co/datasets/google/WaxalNLP) (config `tir_asr`)
- [`UBC-NLP/SimbaBench_dataset`](https://huggingface.co/datasets/UBC-NLP/SimbaBench_dataset) (config `asr_test_tir`)

## Important Note on Test Data

**`SimbaBench_dataset`** is a held‑out benchmark test set, not training data. To avoid leaking benchmark data into training:

- By default, SimbaBench is kept **separate** as an untouched test split.
- The training data (`badrex` + `WaxalNLP`) is merged and re‑split into train/validation.
- Set `MERGE_SIMBABENCH_INTO_TRAINING = True` in section 3 if you want it treated as ordinary training data.

## Workflow

1. Install dependencies & log in to Hugging Face.
2. Load each dataset and inspect the raw schema.
3. Standardize each dataset to a common schema: `audio`, `text`, `source`.
4. Concatenate the training‑eligible sources.
5. Normalize Tigrinya text, resample audio, drop empty/broken rows.
6. Deduplicate (by transcript text).
7. Re‑split into train/validation, append SimbaBench as `test`.
8. Sanity check + audio‑hours check.
9. Push the merged dataset to the Hub, with a dataset card.

## Output

The merged dataset will have three splits:

- `train`: Combined training data from badrex + WaxalNLP.
- `validation`: 10% holdout from the training pool.
- `test`: SimbaBench (kept intact as a clean benchmark).

## Requirements

This notebook was designed to run in Google Colab. The required packages are installed automatically in section 1.

## Usage

1. Open the notebook in Google Colab or Jupyter.
2. Run the cells in order.
3. When prompted, paste a Hugging Face token with WRITE access.
4. Review the schema inspection output in section 2 to verify column names.
5. Update `COLUMN_MAP` if needed (the defaults should work as of writing).
6. Run the remaining cells to merge and push the dataset.

## Customization

### Column Mapping
In section 3, `COLUMN_MAP` defines how each dataset is loaded and standardized. The defaults are based on current dataset schemas, but you should verify them against the inspection output in section 2.

### Test Data Handling
The `role` field in `COLUMN_MAP` controls what happens to each source:

- `"train_pool"`: Gets merged with other train_pool sources and re‑split.
- `"held_out_test"`: Kept completely separate, becomes the final `test` split.

---

**Note**: The notebook file itself is `merge_tigrinya_asr.ipynb`. This README describes its purpose and usage.
