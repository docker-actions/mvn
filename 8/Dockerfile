FROM ubuntu:bionic as build

ARG MAJOR_VERSION=3
ARG MINOR_VERSION=5
ARG PATCH_VERSION=4
ARG SHA256_SUM=ce50b1c91364cb77efe3776f756a6d92b76d9038b0a0782f7d53acf1e997a14d

ENV ROOTFS /build/rootfs
ENV DEBIAN_FRONTEND=noninteractive

# Build pre-requisites
RUN bash -c 'mkdir -p ${ROOTFS}/{bin,usr/bin,usr/local/bin,opt}'

# Install pre-requisites
RUN apt-get update \
      && apt-get -y install apt-utils curl unzip

# Maven
RUN cd ${ROOTFS}/opt \
  && curl -L -o mvn.tar.gz http://www-eu.apache.org/dist/maven/maven-${MAJOR_VERSION}/${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}/binaries/apache-maven-${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}-bin.tar.gz \
  && echo "$SHA256_SUM mvn.tar.gz" | sha256sum -c - \
  && tar -xzf mvn.tar.gz \
  && rm -f mvn.tar.gz \
  && mv apache-maven-* maven \
  && for bin in mvn mvnDebug mvnyjp; do \
       ln -s /opt/maven/bin/${bin} ${ROOTFS}/usr/bin/${bin}; \
     done

# Maven wants /bin/sh
RUN ln -s /bin/bash ${ROOTFS}/bin/sh

COPY entrypoint.sh ${ROOTFS}/usr/local/bin/entrypoint.sh
RUN chmod +x ${ROOTFS}/usr/local/bin/entrypoint.sh

FROM actions/java:8u181-jdk-2
LABEL maintainer = "ilja+docker@bobkevic.com"

ARG ROOTFS=/build/rootfs

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8
ENV M2_HOME=/opt/maven

COPY --from=build ${ROOTFS} /

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]