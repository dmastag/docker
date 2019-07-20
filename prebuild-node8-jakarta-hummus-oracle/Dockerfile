FROM dmastag/prebuild-node8-jakarta-hummus

# ref: https://github.com/oracle/node-oracledb/issues/825#issuecomment-396960483
# the client version we will download from bumpx repo
ENV CLIENT_FILENAME instantclient-basic-linux.x64-12.1.0.1.0.zip

# work in this directory
WORKDIR /opt/oracle/lib

# take advantage of this repo to easily download the client (use it at your own risk)
ADD https://github.com/bumpx/oracle-instantclient/raw/master/${CLIENT_FILENAME} .

# we need libaio and libnsl, the latter is only available as package in the edge repository
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    apk add --update libaio libnsl && \
    ln -s /usr/lib/libnsl.so.2 /usr/lib/libnsl.so.1

# unzip the necessary libraries, create the base symlink and remove the zip file
RUN LIBS="*/libociei.so */libons.so */libnnz12.so */libclntshcore.so.12.1 */libclntsh.so.12.1" && \
    unzip ${CLIENT_FILENAME} ${LIBS} && \
    for lib in ${LIBS}; do mv ${lib} /usr/lib; done && \
    ln -s /usr/lib/libclntsh.so.12.1 /usr/lib/libclntsh.so && \
    rm ${CLIENT_FILENAME}