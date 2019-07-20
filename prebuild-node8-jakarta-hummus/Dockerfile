FROM node:8-alpine

# Setup Timezone
RUN apk add --update tzdata
ENV TZ=Asia/Jakarta

# Issue with Hummus
# https://github.com/galkahana/HummusJS/issues/320
RUN apk add --no-cache --virtual .gyp python make g++ git