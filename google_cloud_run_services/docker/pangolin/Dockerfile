FROM pytorch/pytorch:2.3.1-cuda12.1-cudnn8-runtime

RUN apt update \
    && apt-get install --no-install-recommends -y \
         ca-certificates \
         wget \
         bzip2 \
         unzip \
         git \
	 build-essential \
	 libpq-dev \
    && rm -rf /var/lib/apt/lists/*

COPY docker/pangolin/requirements.txt .
RUN python3 -m pip install --upgrade -r requirements.txt && pip cache purge

ARG CONCURRENCY="2"
ARG GENOME_VERSION="unknown"

COPY docker/ref/GRCh${GENOME_VERSION} .
COPY docker/pangolin/annotations/GRCh${GENOME_VERSION} .

COPY server.py .

ENV TOOL=pangolin
ENV GENOME_VERSION=${GENOME_VERSION}
ENV PORT=8080
ENV CONCURRENCY=${CONCURRENCY}

CMD exec gunicorn --preload --bind :$PORT --workers ${CONCURRENCY} --threads 1 --timeout 0 server:app
