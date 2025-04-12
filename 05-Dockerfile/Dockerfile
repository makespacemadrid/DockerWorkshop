FROM python:3.12-slim

WORKDIR /app
EXPOSE 5000

COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py app.py

CMD ["python", "app.py"]
