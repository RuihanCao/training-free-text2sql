# Data setup

The datasets and databases are **not** bundled in this repo (size + the BIRD
license). Download them separately and point the notebooks at them. Everything
under `data/` is git-ignored except this file.

## What you need

- **BIRD dev set** — databases + `dev.json`, from the official BIRD release.
- **Corrected BIRD subset** — the human-corrected dev examples used for the main
  results (Jin et al.); see paper §4.4. The cross-model notebooks (`04_*`) load
  this directly from the `RuihanCao/bird-dev-corrected` HuggingFace dataset.
- **Schema cache** — `bird_schema_cache.json`, a DDL-style schema per database.
  Not shipped; generate it once with the snippet below.
- **(Fine-tuning baseline only)** BIRD *train* databases + the training `.jsonl`,
  used by `notebooks/01_finetuning_baseline/`.

## Filling in the paths

The path constants near the top of each notebook are left as `path/to/...`
placeholders — set them to wherever you put the data. They assume the standard
BIRD layout, with each database at `<root>/<db_id>/<db_id>.sqlite`:

| placeholder | what it is |
|---|---|
| `path/to/dev/dev_20240627/dev_databases/dev_databases/` | BIRD dev databases |
| `path/to/dev_corrected/dev_databases` | corrected-subset databases |
| `path/to/Bird-Data-Trials/.../train_databases/` | train DBs (FT baseline only) |
| `path/to/bird_schema_cache.json` | the schema cache (see below) |

Output files (result CSVs, the SFT `output_dir`) default to the **working
directory** — change them if you like.

## Generating the schema cache

`bird_schema_cache.json` is just `{db_id: ddl_schema_string}`, built with the
[M-Schema](https://github.com/RuihanCao/M-Schema) generator. Run this once (in a
Colab cell or locally) after you have the BIRD databases:

```python
!git clone -q https://github.com/RuihanCao/M-Schema /content/mschema
!pip install -q sqlalchemy -r /content/mschema/requirements.txt
import sys, os, json, glob
sys.path.insert(0, "/content/mschema")
from sqlalchemy import create_engine
from schema_engine import SchemaEngine

DB_ROOT = "path/to/dev/dev_20240627/dev_databases/dev_databases"  # (or dev_corrected/dev_databases)
cache = {}
for db_dir in glob.glob(os.path.join(DB_ROOT, "*")):
    db_id = os.path.basename(db_dir)
    db_path = os.path.join(db_dir, f"{db_id}.sqlite")
    if not os.path.exists(db_path):
        continue
    se = SchemaEngine(engine=create_engine(f"sqlite:///{db_path}"), db_name=db_id)
    cache[db_id] = se.mschema.to_ddl(example_num=3, max_token=3500)

json.dump(cache, open("bird_schema_cache.json", "w"))
print(f"wrote {len(cache)} schemas")
```

Then point the notebook's schema-cache path at the file. If you'd rather not
precompute, the same `SchemaEngine(...).mschema.to_ddl(...)` call can be inlined
where a notebook currently loads the cache.
