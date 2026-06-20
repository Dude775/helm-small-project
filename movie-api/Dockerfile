FROM python:3.12-slim

WORKDIR /app

ENV PYTHONUNBUFFERED=1
ENV PORT=3000

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app ./app

RUN useradd -m appuser
USER appuser

EXPOSE 3000

CMD uvicorn app.main:app --host 0.0.0.0 --port ${PORT}
