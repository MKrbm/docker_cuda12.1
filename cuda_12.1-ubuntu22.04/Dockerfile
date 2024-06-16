FROM nvidia/cuda:12.1.1-runtime-ubuntu22.04 as base

ARG USERNAME=user
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install necessary packages
RUN apt-get update -y && apt-get -y install git vim software-properties-common htop cmake openssh-server sudo libeigen3-dev locales wget curl x11-apps ripgrep unzip xclip libssl-dev vim cmake-curses-gui git gdb libopenmpi-dev liblapack-dev libblas-dev libhdf5-dev libboost-dev libboost-mpi-dev libboost-serialization-dev libboost-filesystem-dev libboost-system-dev ninja-build hstr

# Install	necessary packages for building
RUN apt-get -y install build-essential

# Setup user, timezone, locale, and other configurations
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo "${USERNAME} ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers \
    && echo "root:root" | chpasswd \
    && echo "$USERNAME:$USERNAME" | chpasswd \
    && ln -snf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && echo Asia/Tokyo > /etc/timezone \
    && locale-gen en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
ENV TZ=Asia/Tokyo
ENV NVM_DIR /home/${USERNAME}/.nvm
ENV NODE_VERSION 18
ENV DISPLAY host.docker.internal:0.0

# Copy necessary scripts
COPY terminal-colors-branch.sh /tmp/
COPY install-libconfig.sh install-libconfig.sh
COPY install-alpscore.sh install-alpscore.sh
COPY ssh-find-agent/ /home/${USERNAME}/ssh-find-agent/
COPY ssh-bashrc.sh ssh-bashrc.sh

# Make scripts executable and run installation scripts
RUN chmod +x ./install-libconfig.sh ./install-alpscore.sh \
    && ./install-libconfig.sh \
    && ./install-alpscore.sh \
    && rm ./install-libconfig.sh ./install-alpscore.sh

# Configure SSH and other services
RUN mkdir -p /var/run/sshd \
    && sed -i 's/^#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#X11Forwarding no/X11Forwarding yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#X11UseLocalhost yes/X11UseLocalhost no/' /etc/ssh/sshd_config \
    && ssh-keygen -A  # Generate SSH host keys here


# Install Miniconda
# TODO: Following codes must be rafaactored

RUN apt install -y zsh
ENV SHELL /bin/zsh
ENV SHELL /bin/zsh
RUN chsh -s /bin/zsh



USER ${USERNAME}

RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# RUN wget -P /home/${USERNAME} http://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh \
#     && mkdir /home/${USERNAME}/.conda \
#     && bash /home/${USERNAME}/Miniconda3-latest-Linux-x86_64.sh -b -p /home/${USERNAME}/miniconda3 -u \
#     && rm /home/${USERNAME}/Miniconda3-latest-Linux-x86_64.sh \
#     && conda init \
#     && conda update -y conda


# Download and run the NVM installation script



# Add NVM to the user's bash profile
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
RUN echo 'export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"' >> ~/.zshrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.zshrc


# Set the working directory to the user's home directory
WORKDIR /home/${USERNAME}

# Set environment variables for runtime
# Here, you can set any environment variables your application needs
# For example, PATH adjustments or environment-specific variables
# ENV PATH="/home/${USERNAME}/miniconda3/bin:${PATH}"

# Switch back to the created user for security
# USER ${USERNAME}

RUN ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519

# Reset user to root
USER root

# TODO: Need to be put in the right place
RUN apt-get -y install libffi-dev  


# This is required to run sshd
EXPOSE 22 


USER ${USERNAME}	


  # Append other configurations to the user's .bashrc
# RUN echo "source /usr/share/bash-completion/completions/git" >> "/home/${USERNAME}/.bashrc"
# RUN cat /tmp/terminal-colors-branch.sh >> "/home/${USERNAME}/.zshrc"
RUN curl -fsSL https://install.julialang.org -o /tmp/install_julia.sh && \
    sh /tmp/install_julia.sh -y && \
    rm /tmp/install_julia.sh

user root 

RUN apt update && \
    apt-get update && \
    apt install -y curl git ripgrep tar unzip vim wget locales

RUN wget https://github.com/neovim/neovim/releases/download/v0.10.0/nvim-linux64.tar.gz && \
    tar -zxvf nvim-linux64.tar.gz && \
    mv nvim-linux64/bin/nvim /usr/bin/nvim && \
    mv nvim-linux64/lib/nvim /usr/lib/nvim && \
    mv nvim-linux64/share/nvim/ /usr/share/nvim && \
    rm -rf nvim-linux64 && \
    rm nvim-linux64.tar.gz

USER ${USERNAME}


RUN curl https://pyenv.run | bash

ENV PYENV_ROOT /home/${USERNAME}/.pyenv
ENV PATH $PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH

RUN pyenv install 3.11
RUN pyenv global 3.11



RUN git clone https://github.com/MKrbm/MyNvim.git /home/${USERNAME}/.config/nvim

RUN nvim --headless +qall

USER root

CMD ["/usr/bin/zsh"]



# Ensure the entrypoint script is executable
# RUN chmod +x /entrypoint.sh

# Set the entrypoint to initialize the environment
# ENTRYPOINT ["/entrypoint.sh", "/bin/sh", "-c"]
# CMD ["bash"]

# Be sure to use root user at the end of the dockerfile. 

