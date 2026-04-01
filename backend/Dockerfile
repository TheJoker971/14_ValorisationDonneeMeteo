FROM dhi.io/python:3.13-dev AS build

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_NO_DEV=1

# uv binary (kept in final image to run the app)
COPY --from=ghcr.io/astral-sh/uv:0.10.0 /uv /uvx /bin/

COPY . .

# Install locked deps (no dev deps) and build static assets at image build-time.
RUN uv sync --locked
RUN uv run python manage.py collectstatic --noinput


FROM dhi.io/python:3.13

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_NO_DEV=1

COPY --from=build /bin/uv /bin/uv
COPY --from=build /bin/uvx /bin/uvx
COPY --from=build /app /app

EXPOSE 8000
CMD ["uv", "run", "gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8000"]
