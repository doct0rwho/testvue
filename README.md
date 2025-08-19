# testvue

## Як запустити локально

### Backend

```bash
python -m venv venv
.\venv\Scripts\activate   # для Windows

pip install -r requirements.txt
uvicorn main:app --reload
