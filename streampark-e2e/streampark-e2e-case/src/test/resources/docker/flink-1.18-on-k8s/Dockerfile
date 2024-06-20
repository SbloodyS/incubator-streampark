#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
FROM apache/streampark:ci as base-image

# Install basic tools
FROM ubuntu:22.04
RUN apt update && apt install -y sudo wget curl vim net-tools iputils-ping \
    && sudo apt install software-properties-common -y \
    && sudo add-apt-repository ppa:openjdk-r/ppa -y
RUN sudo apt update && sudo apt install -y openjdk-8-jdk
RUN JAVA_PATH=$(ls -l /usr/lib/jvm | grep java-8-openjdk-arm64 | grep ^d | awk -F ' ' '{print $9}'); \
    if [ -z "${JAVA_PATH}" ];then \
        echo "JAVA_PATH not found: $JAVA_PATH"; \
        exit 1; \
    else \
        ln -s /usr/lib/jvm/$JAVA_PATH/ /usr/lib/jvm/jdk8; \
    fi
ENV JAVA_HOME=/usr/lib/jvm/jdk8

# Install streampark
COPY --from=base-image /streampark /streampark

ENV FLINK_VERSION 1.18.1

# Install Flink
RUN sudo wget --no-check-certificate -O /flink-${FLINK_VERSION}-bin-scala_2.12.tgz https://dlcdn.apache.org/flink/flink-${FLINK_VERSION}/flink-${FLINK_VERSION}-bin-scala_2.12.tgz \
    && cd / \
    && sudo tar -zxf flink-${FLINK_VERSION}-bin-scala_2.12.tgz

# Install docker
RUN \
    # Add Docker's official GPG key:
    apt update && \
    apt install -y ca-certificates curl gnupg && \
    install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    chmod a+r /etc/apt/keyrings/docker.gpg && \
    # Add the repository to Apt sources:
    echo \
        "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
        "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
        tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt update && \
    # Install the Docker packages.
    apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
