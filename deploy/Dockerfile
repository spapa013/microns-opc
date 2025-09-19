# ---- Builder stage ----
FROM ubuntu:24.04 AS builder

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 python3-venv python3-dev \
    build-essential git curl ca-certificates \
 && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:${PATH}"

WORKDIR /tmp/app
COPY pyproject.toml uv.lock ./

ENV UV_PROJECT_ENVIRONMENT=/tmp/venv
RUN uv sync --frozen --no-install-project \
 && /tmp/venv/bin/python -m ensurepip --upgrade \
 && /tmp/venv/bin/python -m pip install --no-cache-dir --upgrade pip setuptools wheel

# ---- Final stage ----
FROM ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive
# runtime only needs python + git (for VCS versioning) + certs
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 git ca-certificates \
 && rm -rf /var/lib/apt/lists/*

COPY --from=builder /tmp/venv /opt/venv
ENV PATH="/opt/venv/bin:${PATH}"
