# Backend Development Dockerfile
FROM python:3.12-slim

ARG NODE_MAJOR=22

# Install system dependencies + Node.js (provides npx for MCP servers)
RUN apt-get update && apt-get install -y \
    curl \
    build-essential \
    gnupg \
    ca-certificates \
    && mkdir -p /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_${NODE_MAJOR}.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

# Install Docker CLI (for DooD sandbox containers) - fixed image name
COPY --from=public.ecr.aws/docker/library/docker:cli /usr/local/bin/docker /usr/local/bin/docker

# Install pinned uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /usr/local/bin/

# Set working directory
WORKDIR /app

# Copy full root context
COPY . .

# Config.yaml is gitignored - placeholder prevents build failure
RUN touch /app/config.yaml

# Move to backend and install deps
WORKDIR /app/backend
RUN uv sync --frozen --no-cache

# Expose ports
EXPOSE 8001 2024

# Run command
CMD ["sh", "-c", "PYTHONPATH=. uv run uvicorn app.gateway.app:app --host 0.0.0.0 --port 8001"]
