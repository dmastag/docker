FROM node:8-alpine
LABEL maintainer="Julian Alimin"


# install JRE 8 see: https://github.com/docker-library/openjdk/blob/master/8-jre/alpine/Dockerfile

# A few reasons for installing distribution-provided OpenJDK:
#
#  1. Oracle.  Licensing prevents us from redistributing the official JDK.
#
#  2. Compiling OpenJDK also requires the JDK to be installed, and it gets
#     really hairy.
#
#     For some sample build times, see Debian's buildd logs:
#       https://buildd.debian.org/status/logs.php?pkg=openjdk-8

# # Default to UTF-8 file.encoding
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

ENV JAVA_VERSION 8u171
ENV JAVA_ALPINE_VERSION 8.171.11-r0

RUN set -x \
	&& apk add --no-cache \
		openjdk8="$JAVA_ALPINE_VERSION" \
	&& [ "$JAVA_HOME" = "$(docker-java-home)" ]

# If you're reading this and have any feedback on how this image could be
# improved, please open an issue or a pull request so we can discuss it!
#
#   https://github.com/docker-library/openjdk/issues

# Setup

ENV VERSION_SDK_TOOLS=3952940 \
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

ENV IONIC_VERSION=4.0.2 \
    CORDOVA_VERSION=8.0.0 \
    GRADLE_VERSION=4.9

# Install Gradle
RUN apk add --no-cache wget && \
    wget https://services.gradle.org/distributions/gradle-$GRADLE_VERSION-bin.zip && \
    mkdir /opt/gradle && \
    unzip -d /opt/gradle gradle-$GRADLE_VERSION-bin.zip && \
    rm -rf gradle-$GRADLE_VERSION-bin.zip

# Setup environment

ENV PATH ${PATH}:/opt/gradle/gradle-"$GRADLE_VERSION"/bin

# Install basics
RUN npm install -g cordova@"$CORDOVA_VERSION" ionic@"$IONIC_VERSION"
#     # git config --global user.email "you@example.com" && \
#     # git config --global user.name "Your Name" && \
#     ionic start myApp sidemenu --no-interactive

# # Test First Build so that it will be faster later
# RUN cd myApp && \
#     ionic cordova platform add android@7.1.1 --no-interactive && \
#     # ionic cordova plugin add cordova-plugin-firebase --no-interactive && \
#     # ionic cordova build android --prod --no-interactive --release && \
#     # ionic cordova plugin add cordova-plugin-crosswalk-webview --no-interactive && \
#     ionic cordova build android --prod --no-interactive --release