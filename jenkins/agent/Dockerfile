FROM jenkins/inbound-agent:latest

USER root

RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    curl \
    git \
    ca-certificates \
    docker.io \
    && rm -rf /var/lib/apt/lists/*

# ✅ Install Semgrep
RUN pip3 install --no-cache-dir --break-system-packages semgrep

# ✅ Install Trivy
RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh \
    | sh -s -- -b /usr/bin

USER jenkins
