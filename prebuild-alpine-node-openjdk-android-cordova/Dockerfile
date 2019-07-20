FROM dmastag/alpine-node-openjdk-android-cordova

RUN apk add --no-cache --virtual .gyp python make g++ openssh-client rsync zip git
RUN npm install -g xmldom playup moment


WORKDIR /usr/src/app
# COPY package.json package-lock.json /usr/src/app/
# RUN npm install

ADD ./prebuild-alpine-node-openjdk-android-cordova /usr/src/app

RUN cordova platform add android@8.0.0 && \
    cordova prepare android && \
    cordova plugin add https://github.com/tripflex/wifiwizard2 && \
    cordova plugin add cordova-plugin-android-permissions && \
    cordova plugin add https://github.com/indodevniaga/GeolocationPlugin.git && \
    cordova plugin add https://github.com/indodevniaga/CamPlugin.git && \
    cordova plugin add https://github.com/subhanrn/cordova-plugin-mockchecker

RUN cordova build android -- --gradleArg=-PcdvMinSdkVersion=21 && \
    cordova build android --release -- --gradleArg=-PcdvMinSdkVersion=21

RUN cordova plugin add cordova-plugin-crosswalk-webview && \
    cordova build android -- --gradleArg=-PcdvMinSdkVersion=19 && \
    cordova build android --release -- --gradleArg=-PcdvMinSdkVersion=19
    # cordova plugin rm cordova-plugin-crosswalk-webview && \
    
RUN rm -r /usr/src/app/*