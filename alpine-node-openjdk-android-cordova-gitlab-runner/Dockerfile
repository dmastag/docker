FROM dmastag/alpine-node-openjdk-android-cordova

RUN adduser -D -S -h /home/gitlab-runner gitlab-runner

RUN apk add --no-cache \
    bash \
    ca-certificates \
    git \
    openssl \
    tzdata \
    wget

ARG DOCKER_MACHINE_VERSION
ARG DUMB_INIT_VERSION

RUN wget -O /usr/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

RUN chmod +x /usr/bin/gitlab-runner && \
    ln -s /usr/bin/gitlab-runner /usr/bin/gitlab-ci-multi-runner && \
    gitlab-runner --version && \
    mkdir -p /etc/gitlab-runner/certs && \
    chmod -R 700 /etc/gitlab-runner && \
    wget -q https://github.com/docker/machine/releases/download/v${DOCKER_MACHINE_VERSION}/docker-machine-Linux-x86_64 -O /usr/bin/docker-machine && \
    chmod +x /usr/bin/docker-machine && \
    docker-machine --version && \
    wget -q https://github.com/Yelp/dumb-init/releases/download/v${DUMB_INIT_VERSION}/dumb-init_${DUMB_INIT_VERSION}_amd64 -O /usr/bin/dumb-init && \
    chmod +x /usr/bin/dumb-init && \
    dumb-init --version 

COPY entrypoint /
RUN chmod +x /entrypoint

VOLUME ["/etc/gitlab-runner", "/home/gitlab-runner"]
ENTRYPOINT ["/usr/bin/dumb-init", "/entrypoint"]
CMD ["run", "--user=gitlab-runner", "--working-directory=/home/gitlab-runner"]
