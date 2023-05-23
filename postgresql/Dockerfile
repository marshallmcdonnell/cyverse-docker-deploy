FROM ubuntu:20.04

ARG DEBIAN_FRONTEND=noninteractive
ARG LC_COLLATE=C

RUN	apt-get update \
	&& apt-get install -qq -y \
            apt-utils \
			ca-certificates \
			iproute2 \
			jq \
            locales \
			openssh-server \
			python3-apt \
			sudo \
    && rm -rf /var/lib/apt/lists/*

#RUN ssh-keygen -q -f /etc/ssh/ssh_host_dsa_key -N '' -t dsa \
#    && ssh-keygen -q -f /etc/ssh/ssh_host_ecdsa_key -N '' -t ecdsa \
#    && ssh-keygen -q -f /etc/ssh/ssh_host_ed25519_key -N '' -t ed25519 \
#    && mkdir --parents /var/run/sshd \
#    && mkdir --mode 0700 /root/.ssh

RUN mkdir --parents /var/run/sshd \
    && mkdir --mode 0700 /root/.ssh

RUN locale-gen en_US.UTF-8

RUN apt-get update && \
      apt-get install --yes --quiet=2 postgresql postgresql-client python3-psycopg2 && \
      rm -rf /var/lib/apt/lists/*

COPY postgresql/config/* /etc/postgresql/12/main/
RUN chown postgres:postgres /etc/postgresql/12/main/*

COPY postgresql/scripts/*.sql* /tmp/
COPY postgresql/scripts/config.sh /tmp/config

USER postgres

ARG DB_NAME=ICAT
ARG DB_PASSWORD=testpassword
ARG DB_USER=irods
ARG DBMS_PORT=5432
ARG IRODS_RESOURCES=demoResc:rs:/var/lib/irods/Vault
ARG IRODS_ZONE_NAME=tempZone
ARG IRODS_ADMIN_PASSWORD=irods
ARG IRODS_ADMIN_USER=irods
RUN /tmp/config

USER root

COPY postgresql/service.sh /
RUN chmod a+rx /service.sh

COPY base/entrypoint.sh /entrypoint
ENTRYPOINT [ "/entrypoint" ]

CMD [ "/service.sh" ]
