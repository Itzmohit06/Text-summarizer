# Text Summarizer

A small text-summarization app built with FastAPI and a fine-tuned T5 model. It provides a browser interface and a JSON API for generating summaries.

## Features

- Summarizes text using a locally saved T5 model.
- Offers a simple browser interface at `/`.
- Exposes a `POST /summarize/` endpoint.
- Includes a Jupyter notebook for preparing the SAMSum data and fine-tuning `t5-small`.

## Project files

```text
.
├── app.py
├── index.html
├── text_summarizer.ipynb
├── requirements.txt
├── datasets/             # Local data; not included in Git
├── results/              # Local training checkpoints;not  included in Git
└── saved_summary_model/  # Local model/tokenizer; not included in Git
```

The `.gitignore` excludes `datasets/`, `results/`, and `saved_summary_model/`, so these folders and their contents are not included in a GitHub checkout. The dataset files and model may remain in your local working copy. A fresh checkout needs the SAMSum CSV files for training and a generated or otherwise provided `saved_summary_model/` directory before the app can start. Training checkpoints in `results/` are optional.

## Setup

Run these commands from the project directory, using the same Python environment for installing packages and starting the app:

```powershell
python -m pip install -r requirements.txt
```

PyTorch installation can vary by operating system and hardware. If you need CUDA acceleration, install the PyTorch build that matches your CUDA setup; the app also supports CPU inference.

## Prepare the model

If `saved_summary_model/` does not exist, first place the SAMSum CSV files in `datasets/`, then run `text_summarizer.ipynb` to fine-tune `t5-small`. The notebook saves the trained model and tokenizer to `./saved_summary_model`, which is the directory loaded by the app.

The notebook currently reads `samsum-train.csv` and `samsum-validation.csv` from the project root. If you keep the CSV files in `datasets/`, update those `pd.read_csv(...)` paths to `datasets/samsum-train.csv` and `datasets/samsum-validation.csv` before running the notebook.

Training may take a while, especially on CPU.

## Run the web app

From the project directory, start the development server:

```powershell
python -m uvicorn app:app --reload
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000) for the web interface. The interactive API documentation is available at [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs).

## API

Send a JSON request to `POST /summarize/`:

```json
{
  "dialogue": "Alex: Are we still meeting at 3? Sam: Yes, see you then."
}
```

The response contains the generated summary:

```json
{
  "summary": "..."
}
```

## Troubleshooting

- **`ModuleNotFo3undError` for a package:** Install it with `python -m pip install <package>` using the same `python` command used to start Uvicorn.
- **Missing model files:** Generate `saved_summary_model/` by running the training notebook, or provide a compatible model and tokenizer in that directory.
- **Page or API returns an error:** Check the terminal running Uvicorn for the traceback; it contains the underlying server error.
