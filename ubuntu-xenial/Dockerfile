FROM ubuntu:xenial

# Labels.
LABEL maintainer="romikov@gmail.com"

RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    apt-get install -y gnupg2 python3-pip sshpass git openssh-client htop curl vim && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean
    
RUN python3 -m pip install --upgrade pip cffi && \
    pip install ansible && \
    pip install mitogen ansible-lint && \
    pip install --upgrade pywinrm && \
pip install 'ansible[azure]'

RUN mkdir /ansible && \
    mkdir -p /etc/ansible && \
    echo 'localhost' > /etc/ansible/hosts

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

WORKDIR /ansible

# configure entrypoint
ENTRYPOINT [ "/bin/bash", "-c" ]
CMD [ "ansible-playbook", "--version" ]
