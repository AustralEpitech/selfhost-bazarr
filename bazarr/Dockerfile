FROM python:3.10-slim as base
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONDONTWRITEBYTECODE=1      \
    PYTHONFAULTHANDLER=1           \
    PYTHONUNBUFFERED=1             \
    VIRTUAL_ENV="/opt/venv"        \
    PATH="/opt/venv/bin:$PATH"
WORKDIR /app/

FROM base as build
RUN python -m venv "$VIRTUAL_ENV"
RUN apt-get update && apt-get install -y \
        curl                             \
        unzip                            \
    && rm -rf /var/lib/apt/ /var/cache/
ARG URL='https://github.com/morpheus65535/bazarr/releases/latest/download/bazarr.zip'
RUN curl -Lo /tmp/bazarr.zip "$URL" \
    && unzip /tmp/bazarr.zip
RUN pip install -U --no-cache-dir -r requirements.txt

FROM base
COPY --from=build /app/ .
COPY --from=build "$VIRTUAL_ENV" "$VIRTUAL_ENV"
COPY docker-entrypoint.sh /usr/local/bin/
VOLUME /config/
RUN useradd -m app
EXPOSE 6767
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["python", "bazarr.py", "--no-update", "--config=/config/"]
