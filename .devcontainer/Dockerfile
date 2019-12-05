ARG owner
ARG repository

FROM ${repository}_app AS development

USER root

RUN echo 'deb http://download.opensuse.org/repositories/shells:/fish:/release:/3/Debian_9.0/ /' | tee -a /etc/apt/sources.list \
  && curl -L https://download.opensuse.org/repositories/shells:fish:release:3/Debian_9.0/Release.key | apt-key add - \
  && export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)" \
  && echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list \
  && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - \
  && apt-get update -qq \
  && apt-get install --no-install-recommends -y ssh less gnupg git fish man-db silversearcher-ag unzip direnv kubectl google-cloud-sdk fzf hub \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
RUN echo "root:root" | chpasswd

USER ruby
WORKDIR /home/ruby

ENV HOME /home/ruby
ENV SHELL /bin/bash
ENV BUNDLE_PATH vendor/bundle

COPY --chown=ruby .devcontainer/keys/* /home/ruby/keys/
COPY --chown=ruby .devcontainer/ngrok.yml /home/ruby/.ngrok2/ngrok.yml
RUN mkdir -p ~/dev/src ~/dev/bin ~/.vscode-server

RUN mkdir -p ~/.ssh && cp ~/keys/id_rsa* ~/.ssh && chmod 600 ~/.ssh/id_rsa
RUN gpg --import ~/keys/shishi.pub.gpg && gpg --import --allow-secret-key-import ~/keys/shishi.sub.gpg

RUN ssh-keyscan -t rsa github.com > ~/.ssh/known_hosts
RUN if [ ! -d ~/dev/src/github.com/shishi/dotfiles ] ; then git clone git@github.com:shishi/dotfiles.git ~/dev/src/github.com/shishi/dotfiles ; fi && bash ~/dev/src/github.com/shishi/dotfiles/ln_setup.sh

#RUN curl https://git.io/fisher --create-dirs -sLo ~/.config/fish/functions/fisher.fish
RUN echo "exec fish" >> ~/.bashrc

#RUN curl -LO https://github.com/junegunn/fzf-bin/releases/download/0.18.0/fzf-0.18.0-linux_amd64.tgz && tar xvf fzf-0.18.0-linux_amd64.tgz && mv fzf ~/dev/bin && rm -r fzf-0.18.0-linux_amd64.tgz
RUN curl -LO https://github.com/motemen/ghq/releases/download/v0.12.9/ghq_linux_amd64.zip && unzip ghq_linux_amd64.zip && mv ghq_linux_amd64/ghq ~/dev/bin && rm -r ghq_linux_amd64*
RUN curl -LO https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip && unzip ngrok-stable-linux-amd64.zip && mv ngrok ~/dev/bin && rm ngrok-stable-linux-amd64.zip

# RUN ~/dev/bin/ghq get -u ${owner}/${repository}
# RUN cp ~/keys/*.key ~/dev/src/github.com/${owner}/${repository}/config/credentials/

WORKDIR /src

ENTRYPOINT []
