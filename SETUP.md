# Togo AI Summer School 2026 — Setup Test Protocol

Environment: Ubuntu (WSL2), conda for Python env management.
Goal: verify every tool actually works before the lab starts, in a logical order (base → data → orchestration → containers → ML → API).

Run each block in order. If a step fails, fix it before moving to the next — later tools often depend on earlier ones (e.g. Airflow needs Postgres, FastAPI work needs the ML env).

**Single-env setup:** everything below — ML stack, Airflow, dbt, FastAPI, mlflow — lives in one conda env, `taiss2026`. This is a deliberate centralization choice; the tradeoff is that Airflow's dependency constraints (Flask, SQLAlchemy, Jinja2, etc.) can collide with what other tools want in the same env. Airflow was installed last, using its official constraints file, specifically to reduce that risk. Run `pip check` any time something behaves oddly — see section 10.

---

## 0. Conda environment — sanity check

```bash
conda env list
```

Expected: `taiss2026` only. Activate it once at the start of your terminal session — every command in this doc runs inside it.

```bash
conda activate taiss2026
python --version
```

---

## 1. PostgreSQL

```bash
sudo systemctl status postgresql        # should show "active (running)" — WSL: use "sudo service postgresql status" if systemctl misbehaves
psql --version
psql -d main_db -c "SELECT version();"
```

Pass: returns a PostgreSQL version string with no connection error.

---

## 2. pgAdmin (web mode)

```bash
sudo systemctl status apache2
```

Then open a browser to:
```
http://localhost:8080/pgadmin4
```

Pass: login page loads, you can log in with the email/password you set during `setup-web.sh`, and you can add a server connection to `localhost:5432`.

---

## 3. sqlalchemy / psycopg2

```bash
conda activate taiss2026
python3 -c "
import psycopg2
import sqlalchemy
print('psycopg2:', psycopg2.__version__)
print('sqlalchemy:', sqlalchemy.__version__)

engine = sqlalchemy.create_engine('postgresql+psycopg2://othnielchristian:059103@localhost/main_db')
with engine.connect() as conn:
    result = conn.execute(sqlalchemy.text('SELECT 1'))
    print('DB connection OK:', result.scalar())
"
```

Pass: prints both version numbers and `DB connection OK: 1`.

---

## 4. Core ML / data science stack (taiss2026)

```bash
conda activate taiss2026
python3 -c "
import numpy, pandas, matplotlib, seaborn, sklearn
import xgboost, lightgbm
print('numpy', numpy.__version__)
print('pandas', pandas.__version__)
print('sklearn', sklearn.__version__)
print('xgboost', xgboost.__version__)
print('lightgbm', lightgbm.__version__)
"
```

Quick functional test (trains a tiny model, proves the stack actually works end to end):

```bash
python3 -c "
from sklearn.datasets import load_iris
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split

X, y = load_iris(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
clf = RandomForestClassifier().fit(X_train, y_train)
print('Test accuracy:', clf.score(X_test, y_test))
"
```

Pass: prints an accuracy between 0 and 1 with no errors.

---

## 5. PyTorch + TensorFlow (GPU check — RTX 3050)

```bash
python3 -c "
import torch
print('torch', torch.__version__)
print('CUDA available:', torch.cuda.is_available())
if torch.cuda.is_available():
    print('Device:', torch.cuda.get_device_name(0))
"
```

```bash
python3 -c "
import tensorflow as tf
print('tensorflow', tf.__version__)
print('GPUs:', tf.config.list_physical_devices('GPU'))
"
```

Pass: `CUDA available: True` and your RTX 3050 shows up. If `False`, that's a flag to fix before doing GPU-heavy work — not a blocker for CPU-only exercises.

---

## 6. OpenCV

```bash
python3 -c "
import cv2
print('opencv', cv2.__version__)
"
```

---

## 7. NLP stack — transformers / spacy / nltk

```bash
python3 -c "
import transformers, datasets, sentence_transformers, langchain
print('transformers', transformers.__version__)
print('datasets', datasets.__version__)
"
```

```bash
python3 -c "
import spacy
nlp = spacy.load('en_core_web_sm')
doc = nlp('Testing the Togo AI lab setup.')
print([token.text for token in doc])
"
```

```bash
python3 -c "
import nltk
from nltk.tokenize import word_tokenize
print(word_tokenize('Testing nltk tokenization.'))
"
```

Pass: each block prints tokens/text with no `OSError`/`LookupError` (those mean the `spacy download` or `nltk.download` step from setup was missed — rerun it).

---

## 8. mlflow

```bash
mlflow ui --port 5001 &
```

Open `http://localhost:5001` in your browser — should show the empty MLflow tracking UI. Then log a test run:

```bash
python3 -c "
import mlflow
mlflow.set_tracking_uri('http://localhost:5001')
with mlflow.start_run():
    mlflow.log_param('test_param', 1)
    mlflow.log_metric('test_metric', 0.95)
print('Run logged.')
"
```

Pass: refresh the MLflow UI, a new run appears with the logged param/metric.

```bash
kill %1   # stops the background mlflow ui process when done
```

---

## 9. FastAPI / uvicorn

Create a throwaway test file:

```bash
cat > /tmp/test_api.py << 'EOF'
from fastapi import FastAPI
app = FastAPI()

@app.get("/")
def read_root():
    return {"status": "ok", "lab": "taiss2026"}
EOF

uvicorn test_api:app --app-dir /tmp --port 8000 &
sleep 2
curl http://localhost:8000/
kill %1
```

Pass: curl returns `{"status":"ok","lab":"taiss2026"}`.

---

## 10. Apache Airflow (taiss2026)

First, confirm nothing got silently broken when Airflow was installed alongside the ML/API stack:

```bash
conda create -n taiss2026-airflow python=3.10 -y
conda activate taiss2026-airflow
pip install "apache-airflow==2.10.4" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.10.4/constraints-3.10.txt"
pip check
```

Pass: no output (or only warnings unrelated to Flask/SQLAlchemy/Jinja2/click/markupsafe). If it flags one of those, note it but continue — most surface as warnings, not hard failures, unless FastAPI or mlflow stop working in sections 8–9 above.

```bash
export AIRFLOW_HOME=~/airflow
airflow db check
```

```bash
airflow webserver -p 8081 &
airflow scheduler &
sleep 5
curl -I http://localhost:8081
```

Pass: `curl -I` returns `HTTP/1.1 200 OK` (or a redirect to login). Open `http://localhost:8081` in a browser to confirm the UI loads and log in with the admin user you created.

```bash
kill %1 %2   # stop webserver + scheduler when done testing
```

---

## 11. dbt-core (taiss2026)

```bash
conda activate taiss2026
dbt --version
```

Quick functional test:

```bash
mkdir -p /tmp/dbt_test && cd /tmp/dbt_test
dbt init test_project --skip-profile-setup
cd test_project
dbt debug
```

Pass: `dbt debug` completes without a Python traceback (a DB connection failure is expected here unless you set up a `profiles.yml` — that's fine, we're just confirming dbt itself runs).

---

## 12. Docker

```bash
docker --version
docker run hello-world
```

Pass: prints the "Hello from Docker!" welcome message.

```bash
docker compose version
```

Pass: prints a Compose version (confirms the plugin installed correctly).

---

## 13. Flatpak

```bash
flatpak --version
flatpak remote-list
```

Pass: `flatpak-1.x.x`, and `flathub` appears in the remote list.

---

## 14. kubectl + minikube

```bash
kubectl version --client
minikube status
```

If minikube isn't started yet:

```bash
minikube start --driver=docker
kubectl get nodes
```

Pass: `kubectl get nodes` shows one node in `Ready` state.

Quick functional test — deploy something trivial:

```bash
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080
kubectl get pods
kubectl delete deployment hello-node
```

Pass: pod reaches `Running` status before you delete it.

---

## 15. streamlit / plotly / wordcloud (quick visual check)

```bash
conda activate taiss2026
cat > /tmp/test_streamlit.py << 'EOF'
import streamlit as st
import plotly.express as px
import pandas as pd

st.title("Togo AI Lab — Setup Test")
df = pd.DataFrame({"x": [1,2,3], "y": [4,1,7]})
st.plotly_chart(px.line(df, x="x", y="y"))
EOF

streamlit run /tmp/test_streamlit.py
```

Pass: opens in browser (usually `http://localhost:8501`), shows title + a line chart. `Ctrl+C` in the terminal to stop.

---

## Summary checklist

Copy this into your notes and tick off as you go:

- [ ] PostgreSQL running, `psql` connects
- [ ] pgAdmin web UI loads and connects to Postgres
- [ ] sqlalchemy / psycopg2 connect from Python
- [ ] numpy/pandas/sklearn/xgboost/lightgbm import + trained a test model
- [ ] torch + tensorflow import, GPU detected (or confirmed CPU-only)
- [ ] opencv imports
- [ ] transformers/datasets/spacy/nltk work, no missing-download errors
- [ ] mlflow UI runs, logs a test run
- [ ] fastapi/uvicorn serves and responds to curl
- [ ] Airflow webserver + scheduler start, UI reachable
- [ ] dbt --version and dbt debug run cleanly
- [ ] Docker runs hello-world, docker compose works
- [ ] Flatpak remote list shows flathub
- [ ] kubectl + minikube cluster reachable, test pod runs
- [ ] streamlit app renders in browser

---

## Known environment notes (from setup)

- **WSL2**: `systemctl` mostly works but can be flaky — fall back to `service <name> status/start` if a `systemctl` command hangs.
- **Port 80** is taken by nginx on this machine — Apache/pgAdmin was moved to port **8080**.
- **pgModeler**: skipped — not freely distributed for Linux anymore. Use pgAdmin's ERD tool instead.
- **RTX 3050 (4GB VRAM)**: fine for the ML/CV/NLP lab exercises, but keep batch sizes small for transformer-heavy work — 4GB fills up fast.
- **Single env (`taiss2026`)**: ML stack, Airflow, dbt, FastAPI, and mlflow all share one conda env by choice. Airflow was installed last (using its constraints file) to minimize conflicts. Run `pip check` periodically, especially after installing anything new — if Flask/SQLAlchemy/Jinja2 versions ever get fought over, that's the first place it'll show up.