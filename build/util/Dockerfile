# Argo CD v2.13.5
# TODO: Pinned to 2.13.x https://github.com/argoproj-labs/argocd-operator/issues/1631#issue-2752065624
FROM quay.io/argoproj/argocd@sha256:18d4b5b5b81d01dda5584a71e3210d098f870d9ee71944790052f1a9835e2ceb as argocd

# Final Image
FROM docker.io/library/ubuntu:24.04

USER root

ENV ARGOCD_USER_ID=999
ENV DEBIAN_FRONTEND noninteractive

RUN groupadd -g $ARGOCD_USER_ID argocd && \
    useradd -r -u $ARGOCD_USER_ID -g argocd argocd && \
    mkdir -p /home/argocd && \
    chown argocd:0 /home/argocd && \
    chmod g=u /home/argocd && \
    apt-get update && \
    apt-get dist-upgrade -y && \
    apt-get install -y \
    git git-lfs tini curl python3-pip gpg tzdata unzip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Create a symlink for python to point to python3
RUN ln -s /usr/bin/python3 /usr/bin/python

# Install AWS CLI v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf awscliv2.zip aws

# Install the Microsoft Azure CLI
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install the Google Cloud SDK (CLI)
RUN curl -sL https://sdk.cloud.google.com > /tmp/install.sh && \
    bash /tmp/install.sh --disable-prompts --install-dir=/home/argocd && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV PATH="/usr/local/google-cloud-sdk/bin:${PATH}" 

COPY entrypoint.sh /usr/local/bin/uid_entrypoint.sh
RUN chmod +x /usr/local/bin/uid_entrypoint.sh

# Install Argo CD CLI from argocd
COPY --from=argocd /usr/local/bin/argocd /usr/local/bin/argocd

# Copy util wrapper script
COPY util.sh /usr/local/bin/argocd-operator-util

ENV USER_NAME=argocd
ENV HOME=/home/argocd

USER argocd
WORKDIR /home/argocd
