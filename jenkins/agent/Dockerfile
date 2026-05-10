FROM jenkins/inbound-agent:latest

USER root

# Base tools
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    curl \
    git \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# ✅ Install Semgrep system-wide
RUN pip3 install --no-cache-dir --break-system-packages semgrep

# ✅ Install Trivy system-wide
RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh \
    | sh -s -- -b /usr/bin

USER jenkins
