# TAISS 2026 — Togo AI Summer School

Personal workspace for my week at the Togo AI Summer School (TAISS) 2026.

## Environments

This project uses two conda/virtual environments:

- **`taiss2026`** — main environment for general coursework and exercises (Data Science & ML track packages)
- **`taiss2026-airflow`** — dedicated environment for Data Engineering & MLOps track packages (Airflow, dbt, FastAPI, MLflow, etc.) — run inside WSL2 since Airflow requires Linux/macOS

> Make sure to activate the correct environment before running notebooks or scripts, depending on the topic.

```bash
# Example activation (conda)
conda activate taiss2026
# or
conda activate taiss2026-airflow
```

## Repository Structure

```
taiss2026/
├── learning/
│   ├── notes/                    # Daily notes and session summaries
│   ├── exercises/                # Hands-on exercises from each session
│   └── coursework/               # Assignments / graded work (if any)
├── client-scope-rfm-project/     # Final project (pulled in via git subtree)
├── README.md                     # This file
```

- **`learning/notes/`** — Notes taken during sessions (concepts, explanations, key takeaways).
- **`learning/exercises/`** — Practical exercises and code worked through during labs.
- **`learning/coursework/`** — Reserved for any formal assignments or graded deliverables from the program (may stay empty if the program is exercise-only).
- **`client-scope-rfm-project/`** — The final project, a client-scoped RFM (Recency, Frequency, Monetary) analysis. Developed collaboratively in its own repo and pulled into this workspace as a `git subtree` (see below).

## Final Project — Subtree Strategy

The final project (`client-scope-rfm-project`) started life as its own collaborative repo, worked on with the rest of the project team. To keep a complete, self-contained portfolio record here in `taiss2026`, it was merged in as a **git subtree** rather than a submodule — meaning the project's files and history live directly inside this repo, with no extra clone/init steps needed for anyone browsing this workspace.

### Remotes involved

| Remote | Points to | Purpose |
|---|---|---|
| `origin` (in `client-scope-rfm-project`) | Team's shared repo | Collaborative work, PRs, team pushes |
| `mine` (in `client-scope-rfm-project`) | My personal fork | Personal backup of my contributions |
| — (used ad hoc, not saved) | My personal fork | Source used to pull the subtree into `taiss2026` |

### How it was added

```bash
cd ~/development/taiss2026
git subtree add --prefix=client-scope-rfm-project \
  https://github.com/othnielchristian/client-scope-rfm-taiss2026-final-project.git main --squash
```

- `--prefix` set the destination folder name inside `taiss2026`
- Pulled from my personal fork (not the team repo) since this is my portfolio copy
- `--squash` collapsed the project's full commit history into a single commit here, keeping this repo's own history clean

### Keeping it up to date

If the project folder changes locally and those changes are pushed to the fork, the copy here can be refreshed with:

```bash
cd ~/development/taiss2026
git subtree pull --prefix=client-scope-rfm-project \
  https://github.com/othnielchristian/client-scope-rfm-taiss2026-final-project.git main --squash
```

Since this is meant to be a final portfolio snapshot, this is optional and mainly useful if the project gets revisited after the bootcamp.

## System Requirements

Based on the official TAISS 2026 setup guide.

| Component | Minimum | Recommended |
|---|---|---|
| OS | Windows 10 / macOS 11 / Ubuntu 20.04 | Windows 11 / macOS 13+ / Ubuntu 22.04 |
| CPU | Intel Core i5 / AMD Ryzen 5 | Intel Core i7 / Apple M1/M2 |
| RAM | 8 GB | 16 GB+ |
| Storage | 20 GB free | 50 GB (for Docker & data) |
| Display | 1280×768 | 1920×1080 (Full HD) |

### My Machine

| Component | Spec |
|---|---|
| OS | Windows 11 (build 10.0.26200) |
| WSL2 Distro | Ubuntu 24.04.2 LTS (kernel 6.6.87.2-microsoft-standard-WSL2) |
| CPU | AMD Ryzen 7 5800H (8 cores / 16 threads) |
| RAM (Windows) | 15.34 GB |
| RAM (WSL2, capped) | 7.4 GB — WSL2 defaults to ~50% of host RAM, adjustable via `.wslconfig` |
| GPU | NVIDIA GeForce RTX 3050 Laptop GPU (4 GB VRAM) + AMD Radeon Graphics (iGPU) |
| NVIDIA Driver | 592.27 (CUDA 13.1) |
| Disk | 1007 GB total, 901 GB free |

> ⚠️ **Note:** WSL2 RAM is capped below physical total. For memory-heavy tasks (TensorFlow/PyTorch training), consider raising the limit in `C:\Users\USERNAME\.wslconfig`:
> ```ini
> [wsl2]
> memory=12GB
> processors=8
> ```
> Restart WSL (`wsl --shutdown` in PowerShell) after editing.
>
> Also: with 4 GB VRAM, large deep learning models may run into GPU memory limits — Google Colab remains a good fallback for heavier notebooks.

## Prerequisites (Common to All Tracks)

- **Git & GitHub** — [git-scm.com](https://git-scm.com/downloads), verify with `git --version`
- **VS Code** — [code.visualstudio.com](https://code.visualstudio.com), with extensions: Python (Microsoft), Jupyter (Microsoft), GitLens, Rainbow CSV
- **Python 3.10+** — [python.org](https://www.python.org/downloads) or [Anaconda](https://www.anaconda.com/download) (recommended), verify with `python --version` / `python3 --version`
- **Jupyter Notebook / JupyterLab** — included with Anaconda, or `pip install jupyterlab`, verify with `jupyter lab --version`
- **Google Colab** — no install needed, [colab.research.google.com](https://colab.research.google.com) (fallback for machines low on resources / no GPU)

## Track: Data Science & Machine Learning

### Installation

```bash
pip install numpy pandas matplotlib seaborn scikit-learn xgboost lightgbm
pip install tensorflow keras torch torchvision opencv-python
pip install transformers datasets sentence-transformers langchain
pip install mlflow nltk spacy wordcloud plotly streamlit
python -m spacy download fr_core_news_sm
```

### Packages Used

| Library | Purpose | Session |
|---|---|---|
| `numpy` / `pandas` | Data manipulation and analysis |
| `matplotlib` / `seaborn` / `plotly` | Data visualization |
| `scikit-learn` | Classic ML: regression, classification, clustering |
| `xgboost` / `lightgbm` | Advanced boosting algorithms|
| `tensorflow` / `keras` | Deep learning & neural networks|
| `torch` / `torchvision` | PyTorch for computer vision |
| `opencv-python` | Image processing — Plant disease detection |
| `transformers` / `datasets` | Hugging Face NLP models — African languages NLP |
| `langchain` | LLM orchestration & RAG |
| `mlflow` | ML experiment tracking | Cross-track |
| `streamlit` | Fast deployment of data apps | Cross-track |

> 💡 If your machine is short on RAM for TensorFlow/PyTorch, use Google Colab for free GPU access.

## Track: Data Engineering & MLOps

Packages for this track are installed in the **`taiss2026-airflow`** environment (kept separate from `taiss2026` since Airflow has its own dependency constraints).

### System Tools

- **Docker Desktop** — [docker.com](https://www.docker.com/products/docker-desktop/) — on Windows, requires WSL2 enabled first. Verify: `docker --version` and `docker compose version`. Test: `docker run hello-world`.
- **PostgreSQL 15+** — [postgresql.org](https://www.postgresql.org/download/), or via Docker: `docker run -e POSTGRES_PASSWORD=taiss2026 -p 5432:5432 postgres:15`. GUI client: pgAdmin 4.
- **kubectl** (Kubernetes CLI) — [kubernetes.io/docs/tasks/tools](https://kubernetes.io/docs/tasks/tools/)
- **Apache NiFi** — hosted instance provided by the program, no local install needed.

> ⚠️ Apache Airflow requires Linux or native macOS — on Windows, run it via Docker or WSL2. Since I'm on Windows 11 + WSL2, the `taiss2026-airflow` environment lives in WSL2 (Ubuntu 24.04).

### Installation

```bash
pip install pandas sqlalchemy psycopg2-binary apache-airflow
pip install fastapi uvicorn pydantic httpx requests
pip install mlflow scikit-learn great-expectations dbt-core
```

### Packages Used

| Tool / Library | Purpose
|---|---|---|
| PostgreSQL + pgAdmin | Relational database |
| `sqlalchemy` / `psycopg2` | Python → PostgreSQL connection |
| Apache Airflow | Data pipeline orchestration |
| Docker Desktop | Application containerization |
| `fastapi` / `uvicorn` | REST APIs to expose models |
| `mlflow` | Experiment tracking & model versioning |
| `dbt-core` | Data warehouse transformations|
| Kubernetes (`kubectl`) | Large-scale deployment |

> 💡 Apache NiFi runs on a hosted instance — no local setup required.

## Status

- [x] Set up `taiss2026` and `taiss2026-airflow` environments
- [x] Working through `learning/` materials
- [x] Decide on final project name/topic
- [x] Create final project folder (`client-scope-rfm-project`, added via `git subtree`)
- [ ] Build and document final project

## Notes

This README will be updated as the week progresses.