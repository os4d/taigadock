### http://taigaio.github.io/taiga-doc/dist/setup-production.html

FROM pstauffer/curl:latest as builder
ARG DEVELOP
ARG GITHUB_CREDENTIALS
ARG VERSION

RUN mkdir -p taiga-back taiga-front

RUN cd taiga-back ; curl -L "https://github.com/taigaio/taiga-back/archive/${VERSION}.tar.gz" | tar -xzf - --strip-components=1; cd ..
RUN cd taiga-front ; curl -L "https://github.com/taigaio/taiga-front/archive/${VERSION}.tar.gz" | tar -xzf - --strip-components=1; cd ..

run ls -l

RUN apk add --no-cache --virtual .build-deps \
        autoconf \
        automake \
        pkgconf \
        g++ \
        json-c-dev \
        libtool \
        libxml2-dev \
        make \
        perl

RUN apk add --no-cache --virtual .taiga-run-deps \
        freetype-dev \
        cyrus-sasl-dev \
        fontconfig-dev \
        freetype-dev \
        jpeg-dev \
        lcms2-dev \
        libffi-dev \
        libressl-dev \
        linux-headers \
        musl-dev \
        openjpeg-dev \
        postgresql-dev \
        python3-dev \
        tcl-dev \
        tiff-dev \
        tk-dev \
        zlib-dev \
        postgresql-libs

RUN apk add --no-cache --virtual .system-run-deps \
        bash

FROM postgres:9.6.12-alpine

FROM python:3.6.8-alpine3.8

ARG BUILD_DATE
ARG PIPENV_ARGS
ARG VERSION
ARG DEVELOP

RUN cd taiga-back ; pip install -r requirements.txt; \
    python manage.py migrate --noinput ; \
    python manage.py loaddata initial_user ; \
    python manage.py loaddata initial_project_templates ; \
    python manage.py compilemessages ; \
    python manage.py collectstatic --noinput ; \
    python manage.py sample_data ; \
    cd ..

FROM rabbitmq:3.8.0-beta.3-alpine

FROM redis:5.0.4-alpine


FROM nginx:1.15.10-alpine
WORKDIR /usr/share/nginx/html

EXPOSE 80


CMD ["/bin/bash"]