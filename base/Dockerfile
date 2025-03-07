FROM ubuntu:22.04
EXPOSE 22
RUN DEBIAN_FRONTEND=noninteractive apt update && apt install -y openssh-server sudo
RUN DEBIAN_FRONTEND=noninteractive apt autoremove -y &&  rm -rf /var/cache/apt/archives /var/lib/apt/lists

RUN mkdir /var/run/sshd
COPY --chown=root:root --chmod=700 10-sshd /etc/sudoers.d/10-ssh

RUN mkdir -p /opt/zerworker/
WORKDIR /opt/zerworker/
RUN groupadd -r zeruser; useradd -r -g zeruser -d /opt/zerworker zeruser -s /bin/bash; chown -R zeruser:zeruser /opt/zerworker; cd /opt/zerworker

# delete ssh host keys so they can be generated at runtime
RUN rm -v /etc/ssh/ssh_host_*

COPY --chown=root:root --chmod=755 entrypoint.sh /opt/zerworker/entrypoint.sh
USER zeruser

ENTRYPOINT ["/opt/zerworker/entrypoint.sh"]
