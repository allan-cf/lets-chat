FROM yarnpkg/node-yarn

ENV PKG_JSON_URL=https://github.com/allan-cf/lets-chat/blob/master/package.json \
    TAR_GZ_URL=https://github.com/sdelements/lets-chat/archive/master.tar.gz \
    BUILD_DEPS='g++ gcc git make python'

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

ADD $PKG_JSON_URL ./package.json

RUN set -x \
&&  apt-get update \
&&  apt-get install -y $BUILD_DEPS --no-install-recommends --unsafe-perm\
&&  rm -rf /var/lib/apt/lists/* \
&&  npm install --production \
&&  npm install $LCB_PLUGINS \
&&  npm dedupe \
&&  rm -rf /tmp/npm* \
&&  apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $BUILD_DEPS

ADD $TAR_GZ_URL ./master.tar.gz

RUN tar -xzvf master.tar.gz \
&&  cp -a lets-chat-master/. . \
&&  rm -rf lets-chat-master

RUN mkdir -p builtAssets

RUN mkdir -p /instana
WORKDIR /instana
RUN apt-get update
RUN apt-get install python -y \
&&  apt-get install build-essential

RUN npm install --save instana-nodejs-sensor

ENV LCB_DATABASE_URI=mongodb://mongo/letschat \
    LCB_HTTP_HOST=0.0.0.0 \
    LCB_HTTP_PORT=8080 \
    LCB_XMPP_ENABLE=true \
    LCB_XMPP_PORT=5222

EXPOSE 8080 5222

VOLUME ["/usr/src/app/config"]
VOLUME ["/usr/src/app/uploads"]

CMD ["node", "app.js"]
