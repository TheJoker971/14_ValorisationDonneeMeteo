FROM dhi.io/python:3.13-dev AS build

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_NO_DEV=1

COPY --from=ghcr.io/astral-sh/uv:0.10.0 /uv /uvx /bin/

COPY . .

# Install deps and collect statics at build time
RUN uv sync --locked
RUN uv run python manage.py collectstatic --noinput


FROM dhi.io/python:3.13

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_NO_DEV=1 \
    UV_PROJECT_ENVIRONMENT=/app/.venv

COPY --from=build /bin/uv /bin/uv
COPY --from=build /bin/uvx /bin/uvx
# Copy app files and fix ownership for the nonroot user (UID 65532)
COPY --from=build --chown=65532:65532 /app /app

EXPOSE 8000
CMD ["uv", "run", "--no-sync", "gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8000"]
