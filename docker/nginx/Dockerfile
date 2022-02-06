FROM nginx:1.21.6-alpine

RUN apk add --no-cache bash

COPY ./docker/nginx/nginx.conf /etc/nginx/nginx.conf
COPY ./docker/nginx/default.conf /etc/nginx/conf.d/default.conf
