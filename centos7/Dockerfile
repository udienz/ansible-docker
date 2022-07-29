FROM geerlingguy/docker-centos7-ansible
LABEL maintainer="Mahyuddin Susanto"

ARG user=jenkins
ARG group=jenkins
ARG uid=1001
ARG gid=1001
ARG JENKINS_AGENT_HOME=/home/jenkins

WORKDIR /
RUN yum update -y \
    && yum makecache fast \
    && yum -y install deltarpm epel-release \
	&& yum -y update \
    && yum -y install sudo which python3-pip libselinux-python3 git git-lfs  \
    && yum clean all \
    && pip3 install --upgrade --no-cache-dir pip \
	&& localedef -c -f UTF-8 -i en_US en_US.UTF-8

# Disable requiretty.
RUN sed -i -e "s/^\(Defaults\s*requiretty\)/#--- \1/"  /etc/sudoers \
	&& groupadd -g ${gid} ${group} \
    && useradd -d "${JENKINS_AGENT_HOME}" -u "${uid}" -g "${gid}" -m \
    -s /bin/bash "${user}" \
    && echo "Defaults:${user} !requiretty" >> /etc/sudoers \
    && echo "${user} ALL=NOPASSWD: ALL" >> /etc/sudoers.d/${user}

USER jenkins
RUN export LC_ALL=en_US.UTF-8 \
    && echo "LC_ALL=en_US.UTF-8" >> $HOME/.bashrc \
    && export PATH="$HOME/bin:$HOME/.local/bin:$PATH" \
    && echo 'export PATH=$HOME/bin:$HOME/.local/bin:$PATH' >> $HOME/.bashrc \
    && python3 -m pip install --upgrade pip \
	&& pip3 install --no-cache-dir ansible ansible-lint molecule-docker

USER root
# Install Ansible inventory file.
RUN mkdir -p /etc/ansible \
	&& echo -e '[local]\nlocalhost ansible_connection=local' > /etc/ansible/hosts


VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/lib/systemd/systemd"]
