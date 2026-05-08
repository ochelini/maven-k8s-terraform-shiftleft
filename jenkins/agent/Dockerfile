FROM jenkins/inbound-agent:latest

USER root

RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    pipx \
    curl \
    git \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

USER jenkins

RUN /usr/bin/pipx ensurepath && /usr/bin/pipx install semgrep

USER root

RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh \
    | sh -s -- -b /usr/bin

USER jenkins
