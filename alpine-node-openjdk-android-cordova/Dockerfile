FROM node:8-alpine
LABEL maintainer="Julian Alimin"

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
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk/jre
ENV PATH $PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin

ENV JAVA_VERSION 8u171
ENV JAVA_ALPINE_VERSION 8.171.11-r0

RUN set -x \
	&& apk add --no-cache \
		openjdk8-jre="$JAVA_ALPINE_VERSION" \
	&& [ "$JAVA_HOME" = "$(docker-java-home)" ]

# If you're reading this and have any feedback on how this image could be
# improved, please open an issue or a pull request so we can discuss it!
#
#   https://github.com/docker-library/openjdk/issues

# Setup

ENV VERSION_SDK_TOOLS=4333796 \
	  ANDROID_HOME=/usr/local/android-sdk-linux

ENV	PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools

RUN mkdir -p $ANDROID_HOME && \
    chown -R root.root $ANDROID_HOME && \
    # Install dependencies
    apk add --no-cache bash curl git openssl openssh-client ca-certificates && \
    # Install Android SDK
    wget -q -O sdk.zip http://dl.google.com/android/repository/sdk-tools-linux-$VERSION_SDK_TOOLS.zip && \
    unzip sdk.zip -d $ANDROID_HOME && \
    rm -f sdk.zip

    # Install and update Android packages

ADD packages.txt $ANDROID_HOME

RUN mkdir -p /root/.android && \
    touch /root/.android/repositories.cfg && \
    sdkmanager --update && yes | sdkmanager --licenses && \
    sdkmanager --package_file=$ANDROID_HOME/packages.txt

ENV GRADLE_VERSION=4.10.2

# Install Gradle
RUN apk add --no-cache wget && \
    wget https://services.gradle.org/distributions/gradle-$GRADLE_VERSION-bin.zip && \
    mkdir /opt/gradle && \
    unzip -d /opt/gradle gradle-$GRADLE_VERSION-bin.zip && \
    rm -rf gradle-$GRADLE_VERSION-bin.zip

# Setup environment

ENV PATH ${PATH}:/opt/gradle/gradle-"$GRADLE_VERSION"/bin

# install GNU C library (glibc)

RUN ALPINE_GLIBC_BASE_URL="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" && \
    ALPINE_GLIBC_PACKAGE_VERSION="2.28-r0" && \
    ALPINE_GLIBC_BASE_PACKAGE_FILENAME="glibc-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_BIN_PACKAGE_FILENAME="glibc-bin-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_I18N_PACKAGE_FILENAME="glibc-i18n-$ALPINE_GLIBC_PACKAGE_VERSION.apk" \
    && apk add --no-cache --virtual=.build-dependencies wget ca-certificates \
    && wget "https://raw.githubusercontent.com/andyshinn/alpine-pkg-glibc/master/sgerrand.rsa.pub" \
        -O "/etc/apk/keys/sgerrand.rsa.pub" \
    && wget "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
         "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
         "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" \
    && apk add --no-cache "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" \
    && rm "/etc/apk/keys/sgerrand.rsa.pub" \
    && /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true \
    && echo "export LANG=C.UTF-8" > /etc/profile.d/locale.sh \
    && apk del glibc-i18n \
    && rm "/root/.wget-hsts" \
    && apk del .build-dependencies \
    && rm "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
       "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME"

ENV LANG=C.UTF-8

# Credits:
#
# Vlad Frolov @frol
# Andy Shinn @andyshinn

# Install basics


ENV CORDOVA_VERSION=8.0.0

RUN npm install -g cordova@"$CORDOVA_VERSION"
# RUN npm install -g cordova@"$CORDOVA_VERSION" ionic@"$IONIC_VERSION" && \
#     # git config --global user.email "you@example.com" && \
#     # git config --global user.name "Your Name" && \
    # ionic start myApp sidemenu --no-interactive

RUN rm -rf /root/.gradle/wrapper/dists/*

ENV	PATH=$PATH:$ANDROID_HOME/build-tools/28.0.2
