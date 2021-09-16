
FROM ubuntu:xenial as fetcher

COPY query-builder.tar.gz .
RUN mkdir query-builder \
    && tar xfv query-builder.tar.gz -C ./query-builder \
    && rm query-builder.tar.gz

FROM node:12 as builder

COPY --from=fetcher /query-builder /tmp/query-builder

WORKDIR /tmp/query-builder

RUN apt-get update && \
	apt-get install -y \
		ca-certificates \
		build-essential \
		python-pkgconfig \
		git \
		xvfb libnss3 libgtk2.0-0 libgtk-3-0 libgbm-dev libnotify-dev libgconf-2-4 libxss1 libasound2 libxtst6 xauth

# TODO do npm build instead of leaving any dev node modules hanging around
RUN cd query-builder && pwd && npm install --no-dev \
    && npm run build && ls dist

FROM httpd:2.4 as final

RUN rm -rf /usr/local/apache2/htdocs/*
RUN mkdir /usr/local/apache2/htdocs/querybuilder

COPY --from=builder /tmp/query-builder/query-builder/dist /usr/local/apache2/htdocs/querybuilder

RUN mv /usr/local/apache2/htdocs/querybuilder/index.html /usr/local/apache2/htdocs/