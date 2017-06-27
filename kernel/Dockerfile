# (C) Copyright IBM Corporation 2015.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM ibmjava:8-sdk

MAINTAINER Mathias Hermansson <mathias.hermansson@se.ibm.com> (@se35710)

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        apt-utils \
    && rm -rf /var/lib/apt/lists/*
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get upgrade -y \
    && rm -rf /var/lib/apt/lists/*
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
#        ca-certificates \
#        wget \
        unzip \
    && rm -rf /var/lib/apt/lists/*

# Install WebSphere Liberty
ARG LIBERTY_VERSION
ARG LIBERTY_URL
ARG DOWNLOAD_OPTIONS=""
RUN INDEX_CACHE=$(wget -q -O - $DOWNLOAD_OPTIONS https://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/wasdev/downloads/wlp/index.yml) \
    && LIBERTY_VERSION=${LIBERTY_VERSION:-$(echo "$INDEX_CACHE" | grep -o -E "^[[:digit:]]{2}\.[[:digit:]._]+" | sort -V | tail -1)} \
    && LIBERTY_URL=${LIBERTY_URL:-$(echo "$INDEX_CACHE" | grep $LIBERTY_VERSION -A 6 | sed -n 's/\s*kernel:\s//p' | tr -d '\r' )} \
    && wget -o /dev/stdout --progress=dot:mega $DOWNLOAD_OPTIONS $LIBERTY_URL -U UA-IBM-WebSphere-Liberty-Docker -O /tmp/wlp.zip \
    && unzip -q /tmp/wlp.zip -d /opt/ibm \
    && rm /tmp/wlp.zip
ENV PATH=/opt/ibm/wlp/bin:$PATH

# Set Path Shortcuts
ENV LOG_DIR=/logs \
    WLP_OUTPUT_DIR=/opt/ibm/wlp/output
RUN mkdir /logs \
    && ln -s $WLP_OUTPUT_DIR/defaultServer /output \
    && ln -s /opt/ibm/wlp/usr/servers/defaultServer /config

# Configure WebSphere Liberty
RUN /opt/ibm/wlp/bin/server create \
    && rm -rf $WLP_OUTPUT_DIR/.classCache /output/workarea
COPY docker-server /opt/ibm/docker/
EXPOSE 9080

CMD ["/opt/ibm/docker/docker-server", "run", "defaultServer"]
