ARG VERSION=3.12-slim
FROM python:${VERSION}
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 6000
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:6000", "app:app"]

