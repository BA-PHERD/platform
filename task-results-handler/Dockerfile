ARG PYTHON_VERSION=3.12
FROM python:${PYTHON_VERSION}-slim AS base

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

RUN <<EOF
    apt update
    apt install -y curl
    pip install pip-tools
EOF

RUN --mount=type=cache,target=/root/.cache/pip \
    --mount=type=bind,source=requirements.in,target=requirements.in \
    pip-compile requirements.in && pip-sync

COPY . .

ENV KAFKA_BOOTSTRAP_SERVERS=
ENV KAFKA_TOPIC=devprin.task.result

ENV S3_ENDPOINT=
ENV S3_BUCKET=prin
ENV S3_ACCESS_KEY_ID=
ENV S3_SECRET_ACCESS_KEY=
# Expiration defaults to 604800 seconds = 1 week
ENV S3_PRE_SIGNED_URL_EXPIRATION_SECONDS=604800

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=10s --start-period=1m --retries=3 CMD ["curl", "http://localhost/health"]

ENTRYPOINT ["flask", "--app", "api", "run", "--port", "80", "--host", "0.0.0.0"]
