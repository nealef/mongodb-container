FROM sinenomine/clefos-base-s390x:latest

LABEL io.k8s.description="MongoDB is a scalable, high-performance, open source NoSQL database." \
      io.k8s.display-name="MongoDB 3.4" \
      io.openshift.expose-services="27017:mongodb" \
      io.openshift.tags="database,mongodb,rh-mongodb34"

# Labels consumed by Red Hat build service
LABEL com.redhat.component="rh-mongodb34-docker" \
      name="sinenomine/mongodb-34-centos7" \
      version="3.4" \
      release="1" \
      architecture="s390x"

ENV MONGODB_VERSION=3.4 \
    # Set paths to avoid hard-coding them in scripts.
    HOME=/var/lib/mongodb \
    CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/mongodb \
    # Incantations to enable Software Collections on `bash` and `sh -i`.
    ENABLED_COLLECTIONS=rh-mongodb34 \
    BASH_ENV="\${CONTAINER_SCRIPTS_PATH}/scl_enable" \
    ENV="\${CONTAINER_SCRIPTS_PATH}/scl_enable" \
    PROMPT_COMMAND=". \${CONTAINER_SCRIPTS_PATH}/scl_enable"

EXPOSE 27017

ENTRYPOINT ["container-entrypoint"]
CMD ["run-mongod"]

ADD mongodb.repo /etc/yum.repos.d/

RUN yum install -y yum-utils && \
    INSTALL_PKGS="bind-utils gettext iproute rsync tar scl-utils mongodb-enterprise-server mongodb-enterprise-tools mongodb-enterprise-shell" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all

ADD root /

RUN useradd -c "MongoDB Owner" -s /sbin/nologin -u 184 mongodb || true

# Container setup
RUN mkdir -p ${HOME}/data && \
    # Set owner 'mongodb:0' and 'g+rw(x)' permission - to avoid problems running container with arbitrary UID
    /usr/libexec/fix-permissions /etc/mongod.conf ${CONTAINER_SCRIPTS_PATH}/mongodb.conf.template \
    ${HOME} /var/log/mongodb /var/run/mongodb /var/lib/mongo

VOLUME ["/var/lib/mongodb/data"]

USER 184
