# Deployment Guide — GitHub → Railway

This project is already configured for a one-click Railway deploy. Nothing has
to be built or trained on the server: `models/` ships with the trained
artefacts and `dashboard/app.py` starts reading them immediately.

---

## 1. What is already prepared

| File | Purpose |
|---|---|
| `Procfile` | Start command: `streamlit run dashboard/app.py --server.port $PORT --server.address 0.0.0.0 ...` |
| `.python-version` | Pins Python 3.12 (matches the version that pickled the models) |
| `requirements.txt` | All runtime dependencies with pinned versions |
| `.streamlit/config.toml` | `headless = true`, 200 MB upload cap, watcher off |
| `.gitignore` | Ignores secrets and the SQLite DB — **keeps** `models/*.pkl` |

---

## 2. Push to GitHub

From the project folder (the one containing `dashboard/`, `src/`, `models/`):

```cmd
git init
git add .
git commit -m "Churn prediction dashboard"
git branch -M main
git remote add origin https://github.com/<your-user>/<your-repo>.git
git push -u origin main
```

Check on github.com that these are present after the push:

- `Procfile`
- `.python-version`
- `models/best_model.pkl` (~80 MB)
- `models/model_results.json`
- `data/raw/telco_churn_raw.csv`

If `models/*.pkl` is missing, the repository's `.gitignore` was replaced by an
old copy. Restore the version that ships with this project, or force-add them:

```cmd
git add -f models/*.pkl
git commit -m "Add trained models"
git push
```

### Size warning

`models/` is ~107 MB, of which `best_model.pkl` is ~80 MB. That is below
GitHub's hard 100 MB per-file limit, but the first push will take a few minutes
on a slow connection. If GitHub rejects the push, the file exceeded the limit —
regenerate a smaller model with `python main.py` or host the artefacts
separately (see §5).

---

## 3. Deploy on Railway

1. Go to <https://railway.app> → **New Project**.
2. Choose **Deploy from GitHub repo** and authorise Railway to read your repos.
3. Pick the repository. Railway detects Python from `requirements.txt` /
   `.python-version`, installs the dependencies and runs the `Procfile`.
4. Wait for the build log to show `You can now view your Streamlit app`.
5. Open **Settings → Networking → Generate Domain**. Railway gives you a URL
   such as `https://your-app.up.railway.app`.

No environment variables, build command or start command are needed — the
`Procfile` supplies the start command and Railway supplies `$PORT`.

### Expected build output

```
[...] Installing python 3.12
[...] pip install -r requirements.txt
[...] web: streamlit run dashboard/app.py --server.port $PORT ...
[...] You can now view your Streamlit app in your browser.
```

---

## 4. Troubleshooting

**Build fails on a dependency**
The pins in `requirements.txt` are known-good together (pandas 2.1.4,
scikit-learn 1.3.2, xgboost 2.0.3, streamlit 1.29.0). If Railway ignores
`.python-version`, add a variable `PYTHON_VERSION=3.12` in the service settings.

**Page loads but stays on "RUNNING…"**
The websocket is being blocked by the proxy. Uncomment the last two lines of
`.streamlit/config.toml` (`enableCORS` / `enableXsrfProtection`) and redeploy.

**`FileNotFoundError: models/...` in the logs**
The model artefacts were not committed. See §2 (`git add -f models/*.pkl`).

**Uploads fail on a large CSV**
The cap is 200 MB (`maxUploadSize`). Railway's free tier also has limited RAM;
a 10,000-row file needs well under 1 GB.

**Prediction history disappears after a redeploy**
Expected. Railway containers have an ephemeral filesystem, so
`data/churn.db` is recreated empty on every deploy. The dashboard recomputes
everything from the uploaded file, so no user-visible result depends on it.

---

## 5. Optional: keep large models out of Git

If a 107 MB repo is a problem (or GitHub rejects the push), move the artefacts
to object storage and download them at boot:

1. Upload `models/` to S3 / Cloudflare R2 / a GitHub Release asset.
2. Add a `start.sh` that downloads and extracts the archive into `models/`
   before launching Streamlit.
3. Point the `Procfile` at `web: bash start.sh`.

For a mini-project submission the committed-model approach is simpler and is
what this package uses.
