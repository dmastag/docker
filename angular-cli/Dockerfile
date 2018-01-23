FROM node:8-alpine

# install angular-cli as node user
RUN chown -R node:node /usr/local/lib/node_modules \
  && chown -R node:node /usr/local/bin

# install git
RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh

USER node
RUN npm install -g @angular/cli@1.6.4

# set npm as default package manager for root
USER root
RUN ng set --global packageManager=npm