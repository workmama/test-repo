FROM python:3.11.1-bullseye AS base

LABEL maintainer="Matt Workman <workmama@gmail.com>"
SHELL ["/bin/bash", "-c"]

USER root

# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH
ENV NO_PROXY=localhost,127.0.0.1
ENV no_proxy=localhost,127.0.0.1

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

# do not write to stdout
ENV PYTHONUNBUFFERED=1

# we do not want .pyc files
ENV PYTHONDONTWRITEBYTECODE=1

# apt-get and system utilities
RUN apt-get update && apt-get install -y \
    curl apt-utils apt-transport-https debconf-utils gcc build-essential g++-5\
    && rm -rf /var/lib/apt/lists/*

RUN apt -y install git

# runtime dependencies
RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		libbluetooth-dev \
		tk-dev \
		uuid-dev \
	; \
	rm -rf /var/lib/apt/lists/*

ENV PYTHON_VERSION 3.11.1

# install all python packages in the requirements file
WORKDIR /code
COPY ./requirements.txt /code/requirements.txt
RUN pip install --upgrade pip \
    && pip install --no-cache-dir --upgrade -r /code/requirements.txt

ENV APP_MODULE app.main:app
ENV PORT 8545

WORKDIR /code

CMD uvicorn $APP_MODULE --host 0.0.0.0 --port $PORT --reload
FROM base as release

ENV PORT 80
COPY ./app /code/app
CMD uvicorn $APP_MODULE --host 0.0.0.0 --port $PORT 

