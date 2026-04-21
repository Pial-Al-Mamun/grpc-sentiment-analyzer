FROM ghcr.io/astral-sh/uv:python3.14-alpine AS builder

# Set uv environment
ENV UV_LINK_MODE=copy \
    # compile bytecode to speed up startup time at the cost of increased installation time and image size
    UV_COMPILE_BYTECODE=1 \ 
    # Enforce the python version of the system
    UV_PYTHON_DOWNLOADS=never

WORKDIR /app

# Copy dependency files first (better layer caching)
COPY pyproject.toml uv.lock ./

# Install dependencies
RUN uv sync --frozen --no-dev --no-install-project

# Copy source code
COPY . /app

# Install the project itself
RUN uv sync --frozen --no-dev

FROM python:3.14-slim AS runtime

WORKDIR /app

# Copy virtual environment and source from builder
COPY --from=builder /app/.venv /app/.venv
COPY --from=builder /app /app

# Add venv to PATH
ENV PATH="/app/.venv/bin:$PATH"

EXPOSE 50051

ENTRYPOINT ["python", "app/main.py"]