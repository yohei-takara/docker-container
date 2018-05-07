FROM debian:wheezy-slim

# Replace sh to bash
# RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# 鍵の追加
RUN  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

ENV MONGO_MAJAR_VERSION 3
ENV MONGO_MINER_VERSION 4
ENV MONGO_BATCH_VERSION 10
ENV MONGO_REPO_VERSION ${MONGO_MAJAR_VERSION}.${MONGO_MINER_VERSION}
ENV MONGO_INSTALL_VAERSION ${MONGO_MAJAR_VERSION}.${MONGO_MINER_VERSION}.${MONGO_BATCH_VERSION}

# apt repostitory に 追加
RUN echo "deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/${MONGO_REPO_VERSION} main" | tee /etc/apt/sources.list.d/mongodb-org-${MONGO_REPO_VERSION}.list

# apt update && MongoDB install
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
          mongodb-org=${MONGO_INSTALL_VAERSION}\
          mongodb-org-server=${MONGO_INSTALL_VAERSION}\
          mongodb-org-shell=${MONGO_INSTALL_VAERSION}\
          mongodb-org-mongos=${MONGO_INSTALL_VAERSION}\
          mongodb-org-tools=${MONGO_INSTALL_VAERSION}\
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY config /etc/

COPY initialize /usr/src/init/

RUN mkdir -p /var/lib/mongo

ENV MONGO_HOST localhost
ENV MONGO_PORT 27017

RUN mongod --config /etc/mongod.conf \
    && mongo --quiet ${MONGO_HOST}:${MONGO_PORT}/admin /usr/src/init/create_user/admin.js \
    && mongo --quiet ${MONGO_HOST}:${MONGO_PORT}/demo /usr/src/init/create_user/demo.js \
    && mongorestore --db demo --host ${MONGO_HOST} --port ${MONGO_PORT} /usr/src/init/dump/demo \
    && rm -rf /usr/src/init/

EXPOSE ${MONGO_PORT}

CMD ["mongod"]
