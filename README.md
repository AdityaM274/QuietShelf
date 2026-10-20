# Source distribution

The complete tested source is in **QuietShelf-source.zip**. Download and extract it, then open a terminal inside the extracted **QuietShelf** folder before following the run instructions below. The archive preserves all source, tests, assets, model lock, licenses and fictional samples. It excludes model weights, virtual environments and private data.

# QuietShelf

Less sorting. More living. A local, open-weight AI review desk for a pile of `.txt` / `.md` notes. Built20October2026 for [DEV Hacktoberfest Week3: Slow and Steady](https://dev.to/challenges/hacktoberfest-week3-2026-10-19). This is new work, not a fork of the earlier competition entries. AI-assisted development with Codex is disclosed.

## Start

Python3.11+ recommended; tested on Windows with Python3.13. Internet is needed for first dependency/model download only. No account/API key or paid inference. Model download is approximately90MB. Current machine is already set up; double-click `START.cmd`, or run:

```powershell
python -m venv .venv
.venv/Scripts/python.exe -m pip install -r requirements-lock.txt
.venv/Scripts/python.exe setup_model.py
.venv/Scripts/python.exe server.py
# Open http://127.0.0.1:4175/
```

On macOS/Linux use `.venv/bin/python` instead. If a pinned dependency has no wheel for your platform, use requirements.txt and record versions; tested versions are in requirements-lock.txt. Model identity and SHA256 values are pinned in model-lock.json and verified before load. No remote model Python code is executed.

```powershell
.venv/Scripts/python.exe -m unittest discover -s tests -v
```

## The loop

1. Try the fictional pile, or choose up to30 nonempty UTF-8 text/Markdown files (40,000characters each,500,000total). PDFs/images/Word files are not supported.
2. Edit what each of the four shelves means. Choose a similarity threshold and find their shelves.
3. Inspect source excerpts and similarity scores. Low scores, a close tie or very short text lead to abstention. A top-ranked shelf is not automatically accepted.
4. Confirm or override each choice; leave uncertain notes in the inbox if desired. Changing a reviewed choice requires confirmation again. Changing descriptions or threshold clears old reviews.
5. Export JSON with only reviewed decisions, source hashes and quotes. Unreviewed notes are excluded and counted. **No files are moved, renamed or deleted.** This is a triage/review plan, not a filesystem automation tool.

## Open AI is the core

[all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2), Apache-2.0, produces384-dimensional text embeddings through [ONNX Runtime CPU](https://onnxruntime.ai/docs/get-started/with-python.html). The Rust tokenizers library prepares inputs. Notes are chunked into up to254 content tokens plus special tokens with a192-token stride. Normalized mean-pooled chunk vectors are averaged and normalized; cosine similarity compares each note with shelf descriptions. This is classification by similarity, not a generative language model or a trained custom classifier. No cloud model or closed embedding API is called.

An example threshold of0.32 and margin0.04 are engineering defaults, **not calibrated probabilities**. Exact copies use SHA256 of the text string's UTF-8 encoding after browser decoding, not a hash of the original filesystem byte stream. Different encodings or decoding normalization may not preserve original byte identity. Near matches at0.92 are suggestions for comparison only. Excerpts are quoted from input, never model-generated summaries. The source excerpt selection is a lexical-overlap heuristic, not a faithful explanation of the neural network's internal reasoning.

## Honest evaluation and limits

10 automated tests pass. Tests include actual inference with socket connections blocked, chunking, custom shelves, abstention, duplicate distinction, malformed input, loopback/Origin checks and static-file allowlisting. In the seven-note fictional fixture, all five substantive distinct notes ranked their intended shelf first, but the conservative threshold abstained on three. The duplicate was flagged and a tiny fragment left for review. This is an illustrative fixture, **not general accuracy evidence**. No user study, minutes-saved figure or broad performance claim is made.

Browser validation covered actual model inference, override, review invalidation, filtering and an export containing exactly two reviewed records and five excluded records. No browser console errors were observed in that check. See docs/VERIFICATION.md for evidence and outstanding limits.

English short notes are the model's intended fit. Long mixed-topic notes are averaged and can lose detail. Code, unusual domains and non-English text can perform poorly. Duplicate detection doesn't imply safe deletion. Four fixed shelves can be re-described, but adding/removing shelves in the UI is not implemented.

## Privacy boundary

The browser sends chosen text to **this local loopback server**, not a remote inference provider. The server keeps no document database or body logs; it doesn't write chosen documents to disk. Browser and process memory are not a secure vault; the local service has no per-user authentication and local processes can access it. Do not expose its port publicly. Exported JSON contains filenames and quotes; review before sharing. Clear session removes the app's displayed batch and decisions, not browser/OS forensic traces or downloaded exports. Setup downloads packages/weights; ordinary inference does not need internet.

## Source and licenses

- engine.py: real local model, validation, chunking, score/abstention and duplicate logic.
- server.py: loopback JSON API and static allowlist; public/: interface and human review/export.
- setup_model.py, model-lock.json: reproducible public weight download and integrity checks.
- tests/: actual model and HTTP tests; samples/: original fictional demo notes.

App code MIT, copyright2026AdityaMittal. Model license Apache-2.0; attribution and license in THIRD_PARTY/. Dependencies retain their licenses; see THIRD_PARTY/README.md. Do not upload .venv, models, real personal documents or exports to a public repository.

