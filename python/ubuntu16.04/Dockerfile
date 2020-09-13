FROM ubuntu:16.04

ENV USER root

ARG PYTHON_VERSION=3.7
ENV PYTHON_VERSION=${PYTHON_VERSION}

# add python repo
RUN echo "deb http://ppa.launchpad.net/deadsnakes/ppa/ubuntu xenial main \ndeb-src http://ppa.launchpad.net/deadsnakes/ppa/ubuntu xenial main" | tee /etc/apt/sources.list.d/deadsnakes.list \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F23C5A6CF475977595C89F51BA6932366A755776

RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        wget \
        bash \
        sudo \
        coreutils \
        procps \
        openssh-server \
        build-essential \
        gnupg \
        # python
        python${PYTHON_VERSION} python${PYTHON_VERSION}-dev python${PYTHON_VERSION}-distutils python${PYTHON_VERSION}-lib2to3 python${PYTHON_VERSION}-gdbm \
    && apt-get clean \
    && rm -rf \
        /var/lib/apt/lists/* \
        /tmp/* \
        /var/tmp/* \
        /usr/share/man \
        /usr/share/doc \
        /usr/share/doc-base

RUN cd /usr/bin \
        && ln -s idle${PYTHON_VERSION} idle \
        && ln -s pydoc${PYTHON_VERSION} pydoc \
        && ln -s python${PYTHON_VERSION} python \
        && ln -s python${PYTHON_VERSION}-config python-config \
        && cd

# pip install
ENV PYTHON_PIP_VERSION 20.2.3
# https://github.com/pypa/get-pip
ENV PYTHON_GET_PIP_URL https://github.com/pypa/get-pip/raw/d59197a3c169cef378a22428a3fa99d33e080a5d/get-pip.py
ENV PYTHON_GET_PIP_SHA256 421ac1d44c0cf9730a088e337867d974b91bdce4ea2636099275071878cc189e

RUN set -ex; \
	\
	wget --no-check-certificate -O get-pip.py "$PYTHON_GET_PIP_URL"; \
	echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum --check --strict -; \
	\
	python get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		"pip==$PYTHON_PIP_VERSION" \
	; \
	pip --version; \
	\
	find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests -o -name idle_test \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' +; \
	rm -f get-pip.py

RUN pip install --no-cache-dir poetry
