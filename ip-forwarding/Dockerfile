FROM debian

RUN apt-get update && \
  apt-get install -y --no-install-recommends \
  curl \
  iputils-ping \
  iproute2 && \
  apt-get autoremove -y && apt-get purge -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

