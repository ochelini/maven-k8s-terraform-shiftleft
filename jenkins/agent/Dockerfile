FROM jenkins/inbound-agent:latest

USER root

# ✅ Install basic tools + Docker CLI properly
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    curl \
    git \
    ca-certificates \
    docker.io \
    && rm -rf /var/lib/apt/lists/*

# ✅ Ensure docker is accessible globally
RUN ln -s /usr/bin/docker /usr/local/bin/docker || true

# ✅ Install Semgrep
RUN pip3 install --no-cache-dir --break-system-packages semgrep

# ✅ Install Trivy
RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh \
    | sh -s -- -b /usr/local/bin

# ✅ Ensure PATH includes everything (very important)
ENV PATH="/usr/local/bin:/usr/bin:/bin:${PATH}"

USER jenkins
