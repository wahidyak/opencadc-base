# Layer 2: Interactive Terminal (No OpenCode)
FROM opencadc-base:latest

LABEL description="Standard CANFAR Terminal Environment"

# Install Interactive Tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    ttyd \
    tmux \
    htop \
    rclone \
    jq \
    && rm -rf /var/lib/apt/lists/*

# Install Starship Prompt
RUN curl -sS https://starship.rs/install.sh | sh -s -- --yes

# Configure Shell, Aliases, and Starship Theme
RUN echo 'alias py="python3"' > /etc/profile.d/terminal.sh \
    && echo 'alias ..="cd .."' >> /etc/profile.d/terminal.sh \
    && echo 'alias ll="ls -alF"' >> /etc/profile.d/terminal.sh \
    && echo 'alias la="ls -A"' >> /etc/profile.d/terminal.sh \
    && echo 'alias l="ls -CF"' >> /etc/profile.d/terminal.sh \
    && echo 'eval "$(starship init bash)"' >> /etc/bash.bashrc

# Starship Gruvbox Config
RUN mkdir -p /etc && printf 'scan_timeout = 1000\ncommand_timeout = 1000\npalette = "gruvbox"\n\n[palettes.gruvbox]\nbg0 = "#282828"\nyellow = "#d79921"\naqua = "#689d6a"\ngreen = "#98971a"\nred = "#cc241d"\n\n[username]\nshow_always = true\nstyle_user = "bg:yellow fg:bg0"\nstyle_root = "bg:red fg:bg0"\nformat = "[ $user ]($style)"\n\n[directory]\nstyle = "bg:aqua fg:bg0"\nformat = "[ $path ]($style)"\ntruncation_length = 0\ntruncate_to_repo = false\n\n[git_branch]\nsymbol = "git:"\nstyle = "bg:green fg:bg0"\nformat = "[ $symbol$branch ]($style)"\n\n[git_status]\ndisabled = true\n\n[character]\nsuccess_symbol = "[ > ](bold green)"\nerror_symbol = "[ x ](bold red)"\n' > /etc/starship.toml

# Startup Script
RUN mkdir -p /skaha \
    && printf '#!/bin/bash\nttyd --writable --port 5000 -w "${HOME}" -t titleFixed="CANFAR Terminal" /bin/bash -l\n' > /skaha/startup.sh \
    && chmod +x /skaha/startup.sh

ENV STARSHIP_CONFIG=/etc/starship.toml

EXPOSE 5000
WORKDIR /root
CMD ["/skaha/startup.sh"]
