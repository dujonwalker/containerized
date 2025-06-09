FROM ubuntu:24.04

# Install dependencies and useful developer tools
RUN apt-get update && \
    apt-get install -y \
    curl \
    git \
    python3 \
    python3-pip \
    build-essential \
    ripgrep \
    fd-find \
    tree \
    jq \
    wget \
    unzip \
    gawk \
    diffutils \
    ncdu \
    htop \
    watch \
    entr \
    && rm -rf /var/lib/apt/lists/*

# Install bat (better cat with syntax highlighting)
RUN wget -q https://github.com/sharkdp/bat/releases/download/v0.24.0/bat_0.24.0_amd64.deb && \
    dpkg -i bat_0.24.0_amd64.deb && \
    rm bat_0.24.0_amd64.deb

# Install yq (YAML processor)
RUN wget -q https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq && \
    chmod +x /usr/bin/yq

# Install fzf (fuzzy finder)
RUN git clone --depth 1 https://github.com/junegunn/fzf.git /opt/fzf && \
    /opt/fzf/install --bin && \
    ln -s /opt/fzf/bin/fzf /usr/local/bin/fzf

# Install dust (better du)
RUN wget -q https://github.com/bootandy/dust/releases/download/v0.8.6/dust-v0.8.6-x86_64-unknown-linux-gnu.tar.gz && \
    tar xzf dust-v0.8.6-x86_64-unknown-linux-gnu.tar.gz && \
    mv dust-v0.8.6-x86_64-unknown-linux-gnu/dust /usr/local/bin/ && \
    rm -rf dust-v0.8.6-*

# Install delta (better git diff)
RUN wget -q https://github.com/dandavison/delta/releases/download/0.16.5/git-delta_0.16.5_amd64.deb && \
    dpkg -i git-delta_0.16.5_amd64.deb && \
    rm git-delta_0.16.5_amd64.deb

# Install exa (better ls)
RUN wget -q https://github.com/ogham/exa/releases/download/v0.10.1/exa-linux-x86_64-v0.10.1.zip && \
    unzip exa-linux-x86_64-v0.10.1.zip && \
    mv bin/exa /usr/local/bin/ && \
    rm -rf bin/ exa-linux-x86_64-v0.10.1.zip

# Install GitHub CLI
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg && \
    chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt-get update && \
    apt-get install -y gh && \
    rm -rf /var/lib/apt/lists/*

# Create symlink for fd (Ubuntu package name is fd-find)
RUN ln -s /usr/bin/fdfind /usr/bin/fd

# Install Node.js 22.x and npm from NodeSource
RUN curl -fsSL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh && \
    bash nodesource_setup.sh && \
    apt-get install -y nodejs && \
    rm nodesource_setup.sh

# Install useful Node.js tools globally for file processing (not for application development)
RUN npm install -g prettier jsonlint

# Verify installation (optional)
RUN node -v && npm -v && jq --version && rg --version

# Install Claude Code globally
RUN npm install -g @anthropic-ai/claude-code

WORKDIR /workspace

CMD ["/bin/bash"]
