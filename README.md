# AeroHealth: Aircraft Engine Failure Prediction and Maintenance Alert System

**Authors:** Thogaru Praneetha(VTU25728), M.Aravind Kumar(VTU25288)  
**Faculty:** Dr. K. Nithya, Assistant Professor (Senior Grade)

## What this project does

AeroHealth estimates an aircraft engine's **Remaining Useful Life (RUL)** from a sequence of sensor readings and converts that estimate into a maintenance alert:

| Predicted RUL | Alert |
| --- | --- |
| 0–30 cycles | Critical |
| 31–60 cycles | Warning |
| 61–125 cycles | Healthy |

The included experiment uses **NASA C-MAPSS FD001**, a simulated turbofan-engine run-to-failure benchmark. It is a final-year research and decision-support prototype, not a certified aircraft-maintenance system.

## Included in this ZIP

- Full NASA C-MAPSS raw dataset: FD001, FD002, FD003, and FD004.
- A reproducible FD001 training/evaluation script.
- Pretrained Histogram Gradient Boosting model, metrics, test predictions, and six output figures.
- FastAPI prediction service with `/health` and `/predict` endpoints.
- Dockerfile and Docker Compose setup.
- Research paper and bilingual Telugu/English viva guide.

## Quick start

Requires Python 3.10 or later.

```bash
python -m venv .venv
```

**Windows PowerShell**

```powershell
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

**macOS / Linux**

```bash
source .venv/bin/activate
pip install -r requirements.txt
```

Run the included prediction demo:

```bash
python scripts/demo_predict.py --engine 1
```

Start the API:

```bash
uvicorn app.main:app --reload
```

Open `http://127.0.0.1:8000/docs` for the interactive API page.

## Reproduce the experiment

This command retrains the two executed baseline models on FD001, evaluates the final cycle of every test engine, and refreshes the model and output files.

```bash
python scripts/train.py
```

The expected experiment baseline is:

| Model | MAE (cycles) | RMSE (cycles) |
| --- | ---: | ---: |
| Random Forest | 8.60 | 11.79 |
| Histogram Gradient Boosting | 8.56 | 11.70 |

Minor variation can occur across library versions and hardware.

## API request example

`POST /predict` accepts one engine's chronological sensor records. Each record has a cycle, three operating settings, and exactly 21 sensor values. Use the interactive docs or the body printed by `scripts/demo_predict.py`.

```json
{
  "engine_id": "demo-engine-1",
  "records": [
    {
      "cycle": 1,
      "operating_settings": [-0.0007, -0.0004, 100.0],
      "sensors": [518.67, 641.82, 1589.7, 1400.6, 14.62, 21.61, 553.9, 2388.02, 9050.35, 1.3, 47.47, 521.66, 2388.03, 8138.62, 8.42, 0.03, 392, 2388, 100, 39.06, 23.42]
    }
  ]
}
```

For a meaningful prediction, send at least five chronological cycles. The API returns the model version, RUL in cycles, and the alert band.

## Docker

```bash
docker compose up --build
```

Then visit `http://localhost:8000/docs`.

## Dataset and responsible use

The `data/cmapss/` folder contains the NASA C-MAPSS simulated dataset. See `docs/DATASET_AND_RESPONSIBLE_USE.md` for source attribution and limitations. Do not present this simulated benchmark result as proof that the system is safe for a real aircraft or use it to automate a maintenance decision.

## Suggested final product architecture

```
Angular dashboard → ASP.NET Core REST API → SQL database
                                      ↓
                         Python FastAPI model service
```

This ZIP delivers the runnable ML service baseline. Angular, .NET, SQL persistence, CI/CD, Azure deployment, SHAP, LSTM, and uncertainty estimation are the planned full-product extensions, not falsely claimed as complete code here.
