FROM python:3.12-alpine AS base

ENV TZ=Asia/Tokyo

FROM base AS installed

RUN --mount=type=cache,target=/root/.cache \
    pip install --no-cache-dir ansible

RUN --mount=type=cache,target=/var/cache/apk \
    apk add --update --no-cache make

WORKDIR /ansible-playbook
COPY . /ansible-playbook

CMD ["ansible-playbook", "--version"]
