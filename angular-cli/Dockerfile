FROM node:8-alpine

# install angular-cli as node user
RUN chown -R node:node /usr/local/lib/node_modules \
  && chown -R node:node /usr/local/bin

USER node
RUN npm install -g @angular/cli@1.5.2

# set npm as default package manager for root
USER root
RUN ng set --global packageManager=npm