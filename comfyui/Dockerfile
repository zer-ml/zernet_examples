FROM debian:bookworm
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y bash sudo git pip python3 openssh-server jq
RUN DEBIAN_FRONTEND=noninteractive  apt autoremove -y &&  rm -rf /var/cache/apt/archives /var/lib/apt/lists

RUN mkdir -p /var/run/sshd && chown root:root /var/run/sshd && chmod 0755 /var/run/sshd
# delete ssh host keys so they can be generated at runtime
RUN rm -v /etc/ssh/ssh_host_*

RUN mkdir -p /opt/zerworker/
WORKDIR /opt/zerworker/

RUN groupadd -r zeruser; useradd -r -g zeruser -d /opt/zerworker zeruser -s /bin/bash; chown -R zeruser:zeruser /opt/zerworker; cd /opt/zerworker
COPY --chown=root:root --chmod=700 10-sshd /etc/sudoers.d/10-ssh

USER zeruser

# Pull repo
RUN cd /opt/zerworker && git clone --depth 1 --branch v0.3.24 https://github.com/comfyanonymous/ComfyUI.git
WORKDIR /opt/zerworker/ComfyUI
# copy models and extensions
COPY --chown=zeruser:zeruser models/ /opt/zerworker/ComfyUI/models/
COPY --chown=zeruser:zeruser extensions/ /opt/zerworker/ComfyUI/custom_nodes/
RUN mkdir -p /opt/zerworker/ComfyUI/user/default/workflows/
COPY --chown=zeruser:zeruser workflows/ /opt/zerworker/ComfyUI/user/default/workflows/

RUN python3 -m pip config set global.break-system-packages true && pip install torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu124 && pip install -r ./requirements.txt && pip install gitpython typing-extensions toml uv chardet && pip cache purge

COPY --chown=root:root --chmod=755 entrypoint.sh /opt/zerworker/entrypoint.sh


# Exposing port 8188
EXPOSE 8188/tcp
EXPOSE 22/tcp

# Launch at startup
CMD [ "/opt/zerworker/entrypoint.sh" ]