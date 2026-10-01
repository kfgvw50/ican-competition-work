# GraphSAGE Fraud Investigation Platform

工银神盾 · 面向金融机构的 AI 驱动型交易风险识别与关联分析工具

A fraud investigation platform built around GraphSAGE transaction-relationship modeling, FastAPI inference, a React investigation workspace, and optional DeepSeek-assisted explanation.

> This repository uses public datasets for research and demonstration. It is not a bank-internal production system. GraphSAGE is the only risk-scoring model; DeepSeek is an optional natural-language investigation assistant and never replaces the model output.

## 参赛资料 / iCAN Competition Materials

- 完整源代码与资料（百度网盘）：
  - 链接：https://pan.baidu.com/s/1JRhQmy9SUJ7pwrTubjTdqQ
  - 提取密码：队长手机号后四位
- 项目计划书：[工银神盾-项目计划书.pdf](./工银神盾-项目计划书.pdf)（iCAN 大学生创新创业大赛 AI 应用创新挑战赛 · 智融盾队）
- 联系邮箱：903931319@qq.com / hmingli2026@163.com

## What it demonstrates

- Card–transaction–merchant graph construction;
- GraphSAGE single-transaction risk inference;
- 24M-scale streaming engineering validation;
- IEEE-CIS strict time-split validation;
- FastAPI service deployment;
- React risk investigation workflow;
- Optional server-side DeepSeek explanation with field allowlisting and redaction;
- Streamlit fallback interface.

## Architecture

```text
Public transaction data
        ↓
Field mapping and validation
        ↓
Card ── Transaction ── Merchant graph
        ↓
GraphSAGE risk inference
        ├── FastAPI /score
        └── React investigation workspace
                ↓
        Optional FastAPI /ai/explain
                ↓
        DeepSeek-assisted explanation
```

## Repository layout

```text
src/                 FastAPI, data, graph, model, inference and evaluation code
frontend/            React + Vite investigation workspace
configs/             Training and service configuration
scripts/             Smoke and utility scripts
tests/               Backend tests
docs/                Dataset and experiment documentation
outputs/             Local model outputs; ignored by Git
DATASET.md           Dataset acquisition and local placement instructions
```

## Environment

Python 3.10+ and Node.js 18+ are recommended.

```bash
python -m venv .venv
# Windows: .venv\\Scripts\\activate
# Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt

cd frontend
npm install
cd ..
```

## Configuration

Copy `.env.example` to `.env` and set local paths as needed. Never commit `.env` or API keys.

DeepSeek is optional:

```text
DEEPSEEK_API_KEY=
DEEPSEEK_BASE_URL=https://api.deepseek.com
DEEPSEEK_MODEL=deepseek-flash
```

The key is read server-side only. The React application never calls DeepSeek directly.

## Run the API

```bash
uvicorn src.api.main:app --host 127.0.0.1 --port 8000
```

Health and API documentation:

```text
http://127.0.0.1:8000/health
http://127.0.0.1:8000/docs
```

## Run the React frontend

```bash
cd frontend
npm run dev
```

Open:

```text
http://127.0.0.1:5173/
```

The Vite proxy forwards `/api/*` to the FastAPI service.

## Optional Docker run

```bash
docker compose up -d api
```

The Compose file also contains an optional MLflow service. Do not place credentials in the Compose file; use an ignored `.env` file or deployment secret injection.

## API workflow

1. Submit a public demonstration transaction to `POST /score`.
2. The service returns the GraphSAGE probability, label, threshold and an `investigation_id`.
3. The server stores a short-lived investigation context in process memory.
4. The frontend may submit only the `investigation_id` to `POST /ai/explain`.
5. The server filters and redacts data before an optional DeepSeek request.

DeepSeek is not involved in probability, label or threshold calculation.

## Model and experiment notes

The repository contains documentation for:

- IBM TabFormer integration and field mapping;
- 24M-scale streaming GraphSAGE engineering validation;
- IEEE-CIS strict time-split GraphSAGE validation;
- evaluation metrics and reproducibility boundaries.

Large datasets and model checkpoints are excluded from Git by default. See `DATASET.md` and the documentation under `docs/`.

## Testing

```bash
python -m pytest tests -q
cd frontend
npm run build
```

## Demo flow

```text
Home
→ Quick triage
→ Transaction investigation
→ GraphSAGE /score
→ AI explanation
→ Optional DeepSeek report
→ Return to investigation
```

AI-generated text is for investigation assistance only and does not constitute a final fraud determination or automatic business action.

## License

This repository is proprietary and provided solely for iCAN competition evaluation. See the LICENSE file. Copying, modification, redistribution, competition use (other than iCAN evaluation), or commercial use is prohibited without prior written permission. Dataset licenses and terms remain the responsibility of the downloader and end user.


