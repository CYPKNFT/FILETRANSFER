# Fix the Vector DB on PC

Run these on the Windows PC in PowerShell. In order. One at a time.

## Step 1 — Download the embedding model first

```powershell
python -c "from sentence_transformers import SentenceTransformer; m = SentenceTransformer('all-MiniLM-L6-v2'); print('Model ready')"
```

Wait until it prints `Model ready`. If it hangs, the model is downloading (~90MB). Let it finish.

## Step 2 — Verify GPU works

```powershell
python -c "import torch; print('CUDA:', torch.cuda.is_available()); print('GPU:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'NONE')"
```

Should print `CUDA: True` and `GPU: NVIDIA GeForce RTX 4090`.

## Step 3 — Build the vector index (FOREGROUND, VISIBLE OUTPUT)

```powershell
cd F:\Workspace\GitHubVault\OfflineMPCs
python -u tools/index-docs build --vec --force
```

`-u` = unbuffered output. You WILL see progress. Do NOT run this in the background.

## Step 4 — Verify it worked

```powershell
ls F:\Workspace\GitHubVault\OfflineMPCs\data\chromadb\
```

`chroma.sqlite3` should be more than 0 bytes.

## What went wrong last time

1. Model was downloading silently (first run downloads ~90MB)
2. Build was run in background with buffered output — so nothing was visible
3. ChromaDB doesn't write to disk until the first batch of embeddings finishes
4. Combination of all three = looks stuck/broken but wasn't

## Rules for PC Claude next time

- ALWAYS run new builds in foreground first
- ALWAYS use `python -u` for unbuffered output
- ALWAYS pre-download models before starting a build
- NEVER run background tasks until you've confirmed it works foreground
- NEVER try DirectML — just install CUDA
- NEVER waste time on git commits when the user says build the DB
