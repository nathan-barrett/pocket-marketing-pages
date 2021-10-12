# BACKEND IMAGE
FROM python:3.9-slim-bullseye AS backend

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PATH="/venv/bin:$PATH"

# Install a few essentials and clean apt caches afterwards.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        apt-transport-https \
        build-essential \
        curl \
        git \
        libffi-dev && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN python -m venv /venv

WORKDIR /app

# We copy the pip requirements first to leverage Docker caching.
COPY ./requirements.txt /app/
RUN pip install -U pip \
    && pip install --no-cache-dir -r requirements.txt

# END BACKEND IMAGE


# FRONTEND BUILDER IMAGE
FROM node:lts-slim AS frontend

# WORKDIR /app

# COPY package*.json /app/
# RUN npm install
# COPY . /app/
# RUN npm run build

# END FRONTEND BUILDER IMAGE


# FINAL IMAGE
FROM python:3.7-slim AS final

EXPOSE 5000

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PATH="/venv/bin:$PATH"

WORKDIR /app

COPY --from=backend /venv/ /venv/
# COPY --from=frontend /app/public/ /app/public/

CMD exec gunicorn \
    --bind 0.0.0.0:5000 \
    --workers 2 \
    --threads 8 \
    --worker-tmp-dir /dev/shm \
    --log-file - \
    --access-logfile - \
    wsgi:application
# END FINAL IMAGE
