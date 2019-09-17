FROM node:lts-alpine
LABEL maintainer="Julian Alimin"

# # Update References
# https://github.com/docker-library/openjdk/blob/master/8/jre/alpine/Dockerfile
# https://developer.android.com/studio/#downloads
# https://gradle.org/releases/
# https://github.com/frol/docker-alpine-glibc/blob/master/Dockerfile
# https://www.npmjs.com/package/cordova
# https://developer.android.com/studio/releases/build-tools

# References
# https://hub.docker.com/r/greyfoxit/alpine-glibc/~/dockerfile/
# https://hub.docker.com/r/greyfoxit/alpine-android/~/dockerfile/
# https://github.com/docker-library/openjdk/blob/master/8/jre/alpine/Dockerfile

# install JRE 8 see: https://github.com/docker-library/openjdk/blob/master/8/jre/alpine/Dockerfile

# A few reasons for installing distribution-provided OpenJDK:
#
#  1. Oracle.  Licensing prevents us from redistributing the official JDK.
#
#  2. Compiling OpenJDK also requires the JDK to be installed, and it gets
#     really hairy.
#
#     For some sample build times, see Debian's buildd logs:
#       https://buildd.debian.org/status/logs.php?pkg=openjdk-8

# Default to UTF-8 file.encoding
ENV LANG C.UTF-8

# add a simple script that can auto-detect the appropriate JAVA_HOME value
# based on whether the JDK or only the JRE is installed
RUN { \
		echo '#!/bin/sh'; \
		echo 'set -e'; \
		echo; \
		echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
	} > /usr/local/bin/docker-java-home \
	&& chmod +x /usr/local/bin/docker-java-home
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk
ENV PATH $PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin

ENV JAVA_VERSION 8u212
ENV JAVA_ALPINE_VERSION 8.212.04-r1

RUN set -x \
	&& apk add --no-cache \
		openjdk8="$JAVA_ALPINE_VERSION" \
	&& [ "$JAVA_HOME" = "$(docker-java-home)" ]

# If you're reading this and have any feedback on how this image could be
# improved, please open an issue or a pull request so we can discuss it!
#
#   https://github.com/docker-library/openjdk/issues

# install command line android sdk
# Cannot use latest because of
# https://github.com/jangrewe/gitlab-ci-android/issues/23
# https://issuetracker.google.com/issues/66465833

ENV VERSION_SDK_TOOLS=4333796 
ENV ANDROID_HOME "/sdk"
ENV PATH "$PATH:${ANDROID_HOME}/tools"

RUN apk update
RUN apk add --no-cache bash bzip2 curl unzip git

RUN curl -s https://dl.google.com/android/repository/sdk-tools-linux-${VERSION_SDK_TOOLS}.zip > /sdk.zip
RUN unzip /sdk.zip -d /sdk
RUN rm -v /sdk.zip

RUN mkdir -p $ANDROID_HOME/licenses/
RUN echo "8933bad161af4178b1185d1a37fbf41ea5269c55\nd56f5187479451eabf01fb78af6dfcb131a6481e" > $ANDROID_HOME/licenses/android-sdk-license
RUN echo "84831b9409646a918e30573bab4c9c91346d8abd" > $ANDROID_HOME/licenses/android-sdk-preview-license

# ADD packages.txt /sdk
RUN mkdir -p /root/.android
RUN touch /root/.android/repositories.cfg
RUN ${ANDROID_HOME}/tools/bin/sdkmanager --update | grep -v = || true

RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "add-ons;addon-google_apis-google-24" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "build-tools;29.0.2" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "extras;android;m2repository" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "extras;google;m2repository" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "extras;google;google_play_services" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "extras;m2repository;com;android;support;constraint;constraint-layout;1.0.2" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "extras;m2repository;com;android;support;constraint;constraint-layout-solver;1.0.2" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "platform-tools" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager "platforms;android-29" | grep -v = || true
RUN yes | ${ANDROID_HOME}/tools/bin/sdkmanager --licenses | grep -v = || true


ENV GRADLE_VERSION=5.6.2

# Install Gradle
RUN apk add --no-cache wget && \
    wget -nv https://services.gradle.org/distributions/gradle-$GRADLE_VERSION-bin.zip && \
    mkdir /opt/gradle && \
    unzip -d /opt/gradle gradle-$GRADLE_VERSION-bin.zip && \
    rm -rf gradle-$GRADLE_VERSION-bin.zip

# Setup environment

ENV PATH ${PATH}:/opt/gradle/gradle-"$GRADLE_VERSION"/bin

# Ref: https://github.com/frol/docker-alpine-glibc/blob/master/Dockerfile
# install GNU C library (glibc)

# Here we install GNU libc (aka glibc) and set C.UTF-8 locale as default.

RUN ALPINE_GLIBC_BASE_URL="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" && \
    ALPINE_GLIBC_PACKAGE_VERSION="2.30-r0" && \
    ALPINE_GLIBC_BASE_PACKAGE_FILENAME="glibc-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_BIN_PACKAGE_FILENAME="glibc-bin-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_I18N_PACKAGE_FILENAME="glibc-i18n-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    apk add --no-cache --virtual=.build-dependencies wget ca-certificates && \
    echo \
        "-----BEGIN PUBLIC KEY-----\
        MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApZ2u1KJKUu/fW4A25y9m\
        y70AGEa/J3Wi5ibNVGNn1gT1r0VfgeWd0pUybS4UmcHdiNzxJPgoWQhV2SSW1JYu\
        tOqKZF5QSN6X937PTUpNBjUvLtTQ1ve1fp39uf/lEXPpFpOPL88LKnDBgbh7wkCp\
        m2KzLVGChf83MS0ShL6G9EQIAUxLm99VpgRjwqTQ/KfzGtpke1wqws4au0Ab4qPY\
        KXvMLSPLUp7cfulWvhmZSegr5AdhNw5KNizPqCJT8ZrGvgHypXyiFvvAH5YRtSsc\
        Zvo9GI2e2MaZyo9/lvb+LbLEJZKEQckqRj4P26gmASrZEPStwc+yqy1ShHLA0j6m\
        1QIDAQAB\
        -----END PUBLIC KEY-----" | sed 's/   */\n/g' > "/etc/apk/keys/sgerrand.rsa.pub" && \
    wget \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    apk add --no-cache \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    \
    rm "/etc/apk/keys/sgerrand.rsa.pub" && \
    /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 "$LANG" || true && \
    echo "export LANG=$LANG" > /etc/profile.d/locale.sh && \
    \
    apk del glibc-i18n && \
    \
    rm "/root/.wget-hsts" && \
    apk del .build-dependencies && \
    rm \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME"

# Credits:
#
# Vlad Frolov @frol
# Andy Shinn @andyshinn

# Install basics


ENV CORDOVA_VERSION=9.0.0

RUN npm install -g cordova@"$CORDOVA_VERSION"

# RUN npm install -g cordova@"$CORDOVA_VERSION" ionic@"$IONIC_VERSION" && \
#     git config --global user.email "you@example.com" && \
#     git config --global user.name "Your Name" && \
#     ionic start myApp sidemenu --no-interactive

# RUN cd myApp && ionic cordova build android --release

RUN rm -rf /root/.gradle/wrapper/dists/*

ENV	PATH=$PATH:$ANDROID_HOME/build-tools/29.0.2
