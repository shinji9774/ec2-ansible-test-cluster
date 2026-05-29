FROM python:3.12-slim-trixie AS base

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo

FROM base AS installed

RUN --mount=type=cache,target=/root/.cache \
    pip install --no-cache-dir ansible

RUN --mount=type=cache,target=/var/cache/apt \
    --mount=type=cache,target=/var/lib/apt/lists \
    apt-get update && \
    apt-get install make unzip -y

WORKDIR /ansible-playbook
COPY . /ansible-playbook

CMD ["ansible-playbook", "--version"]
