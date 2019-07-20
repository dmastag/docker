FROM node:alpine

# Setup Timezone
RUN apk add --update tzdata git
ENV TZ=Asia/Jakarta

RUN apk add --update \
  && apk add --no-cache ffmpeg opus pixman cairo pango giflib ca-certificates \
  && apk add --no-cache --virtual .build-deps git curl build-base jpeg-dev pixman-dev \
  cairo-dev pango-dev pangomm-dev libjpeg-turbo-dev giflib-dev freetype-dev python g++ make