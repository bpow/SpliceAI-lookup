FROM tensorflow/tensorflow:2.16.1-gpu

# annoyingly, the base image includes dependency to an older verison of blinker which
# conflicts what is needed for flask...
RUN dpkg --purge \
    software-properties-common \
    python3-oauthlib \
    python3-lazr.restfulclient \
    python3-software-properties \
    python3-launchpadlib \
    python3-blinker

RUN apt update && apt-get install --no-install-recommends -y \
    ca-certificates \
    wget \
    bzip2 \
    unzip \
    git \
    libcurl4-openssl-dev \
    libbz2-dev \
    liblzma-dev \
    zlib1g-dev \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

COPY docker/spliceai/requirements.txt .
RUN python3 -m pip install --upgrade -r requirements.txt

ARG CONCURRENCY="2"
ARG GENOME_VERSION="unknown"

COPY docker/ref/GRCh${GENOME_VERSION} .
COPY docker/spliceai/annotations/GRCh${GENOME_VERSION} .
COPY server.py .

ENV PORT=8080
ENV TOOL=spliceai
ENV GENOME_VERSION=${GENOME_VERSION}
ENV CONCURRENCY=${CONCURRENCY}

CMD exec gunicorn --preload --bind :$PORT --workers ${CONCURRENCY} --threads 1 --timeout 0 server:app
