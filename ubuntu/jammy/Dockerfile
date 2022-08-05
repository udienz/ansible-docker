FROM udienz/buildpack:jammy
LABEL maintainer="Mahyuddin Susanto"

ARG user=jenkins
ARG group=jenkins
ARG uid=1001
ARG gid=1001
ARG JENKINS_AGENT_HOME=/home/jenkins
ENV TZ="UTC"
ARG DEBIAN_FRONTEND=noninteractive
# See https://github.com/geerlingguy/docker-ubuntu2204-ansible/blob/master/Dockerfile

RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime \
    && apt update \
    && apt dist-upgrade -y \
    &&  apt-get install -y -q --no-install-recommends \
    git python3-pip locales libffi-devi libssl-dev libyaml-dev python3-dev \
    pthon3-setuptools python3-yaml iproute2 wget \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/* \
    && groupadd -g ${gid} ${group} \
    && useradd -d "${JENKINS_AGENT_HOME}" -u "${uid}" -g "${gid}" -m \
    -s /bin/bash "${user}" \
    && echo "Defaults:${user} !requiretty" >> /etc/sudoers \
    && echo "${user} ALL=NOPASSWD: ALL" >> /etc/sudoers.d/${user} \
    && locale-gen en_US.UTF-8

RUN wget -O /initctl_faker \
    https://raw.githubusercontent.com/geerlingguy/docker-ubuntu2204-ansible/master/initctl_faker \
    && chmod +x /initctl_faker \
    && rm -fr /sbin/initctl && ln -s /initctl_faker /sbin/initctl \
    && mkdir -p /etc/ansible \
    && rm -f /lib/systemd/system/systemd*udev* \
    && rm -f /lib/systemd/system/getty.target

USER jenkins
RUN export LC_ALL=en_US.UTF-8 \
    && echo "LC_ALL=en_US.UTF-8" >> $HOME/.bashrc \
    && export PATH="$HOME/bin:$HOME/.local/bin:$PATH" \
    && echo 'export PATH=$HOME/bin:$HOME/.local/bin:$PATH' >> $HOME/.bashrc \
    && python3 -m pip install --upgrade pip \
    && pip3 install --no-cache-dir ansible ansible-lint molecule-docker

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/lib/systemd/systemd"]
