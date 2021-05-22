FROM ubuntu:20.04

# Set the environment up
ENV TERM xterm-256color

# Install general shizzle we want
# rvm requirements
# vim requirements
RUN apt-get update && \
    DEBIAN_FRONTEND="noninteractive" apt-get install -y \
    # Build tools
    make build-essential gcc cmake autoconf patch automake software-properties-common\
    # General Utils
    unzip jq git wget python3-pip sudo vim  tmux telnet httpie redis ansible  gettext bzip2 python3-pip\
    # Build library
    libtool bison luajit libncurses-dev clang libpython3-dev zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget && \
    # Clean up
    apt-get clean

RUN cd /usr/local/bin && \
    wget --quiet https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh

RUN groupadd docker && \
    useradd -g docker docker && \
    echo 'docker ALL=(ALL:ALL) NOPASSWD:ALL' >> /etc/sudoers

RUN mkdir -p /storage
WORKDIR /storage
RUN chown docker:docker /storage

USER docker
ENV HOME=/home/docker
ENV USER=docker

# Gem GPG2
#RUN gpg2 --keyserver hkp://keys.gnupg.net:80 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

# Add all our config files
COPY home $HOME/
RUN sudo chown -R docker:docker $HOME/

# Clone on the .vimrc stuff
RUN mkdir -p $HOME/.vim/vim-addons && \
    cd $HOME/.vim/vim-addons && \
    git clone --depth=1 https://github.com/MarcWeber/vim-addon-manager && \
    git clone --depth=1 https://github.com/tpope/vim-fugitive && \
    git clone --depth=1 https://github.com/airblade/vim-gitgutter && \
    git clone --depth=1 https://github.com/editorconfig/editorconfig-vim && \
    git clone --depth=1 https://github.com/geekjuice/vim-spec && \
    git clone --depth=1 https://github.com/scrooloose/nerdtree && \
    git clone --depth=1 https://github.com/tomtom/tlib_vim && \
    git clone --depth=1 https://github.com/MarcWeber/vim-addon-commenting && \
    git clone --depth=1 https://github.com/Chiel92/vim-autoformat && \
    git clone --depth=1 https://github.com/digitaltoad/vim-jade && \
    git clone --depth=1 https://github.com/tpope/vim-cucumber && \
    git clone --depth=1 https://github.com/tmhedberg/matchit && \
    git clone --depth=1 https://github.com/vim-scripts/restore_view.vim && \
    git clone --depth=1 https://github.com/vim-ruby/vim-ruby && \
    git clone --depth=1 https://github.com/Valloric/YouCompleteMe && \
    git clone --depth=1 https://github.com/ternjs/tern_for_vim && \
    git clone --depth=1 https://github.com/ctrlpvim/ctrlp.vim && \
    git clone --depth=1 https://github.com/digitaltoad/vim-pug && \
    git clone --depth=1 https://github.com/isRuslan/vim-es6 && \
    git clone --depth=1 https://github.com/othree/html5-syntax.vim && \
    git clone --depth=1 https://github.com/othree/html5.vim  && \
    git clone --depth=1 https://github.com/othree/javascript-libraries-syntax.vim && \
    git clone --depth=1 https://github.com/leafgarland/typescript-vim && \
    git clone --depth=1 https://github.com/HerringtonDarkholme/yats.vim && \
    git clone --depth=1 https://github.com/flazz/vim-colorschemes && \
    git clone --depth=1 https://github.com/scrooloose/syntastic && \
    git clone --depth=1 https://github.com/goatslacker/mango.vim && \
    git clone --depth=1 https://bitbucket.org/vimcommunity/vim-pi

# Terraform
ARG DEVENV_TERRAFORM_VERSION
RUN cd /tmp && \
    wget --quiet https://releases.hashicorp.com/terraform/$DEVENV_TERRAFORM_VERSION/terraform_$DEVENV_TERRAFORM_VERSION\_linux_amd64.zip && \
    unzip terraform_*.zip && \
    sudo mv terraform /usr/local/bin && \
    rm -rf *terraform*

# Setup AWS CLI

# Setup Kubernetes CLI
ARG DEVENV_KUBECTL_VERSION
RUN cd /usr/local/bin && \
    sudo wget --quiet https://storage.googleapis.com/kubernetes-release/release/v$DEVENV_KUBECTL_VERSION/bin/linux/amd64/kubectl && \
    sudo chmod +x kubectl

# Add GoLANG
ARG GOLANG_VERSION
RUN cd /tmp && \
    wget --quiet https://dl.google.com/go/go$GOLANG_VERSION.linux-amd64.tar.gz && \
    tar xf go$GOLANG_VERSION.linux-amd64.tar.gz && \
    sudo mv go /usr/local && \
    rm -f go*

ENV GOROOT /usr/local/go
ENV GOPATH /storage/go
ENV PATH $GOPATH/bin:$GOROOT/bin:$PATH

RUN go get -u github.com/golang/lint/golint
RUN go get -u github.com/golang/dep/cmd/dep

# Fix all permissions
ENTRYPOINT ["/bin/bash", "--login"]
CMD ["/usr/local/bin/entrypoint.sh"]
VOLUME /home/docker

COPY entrypoint.sh /usr/local/bin/entrypoint.sh
COPY versions.sh /usr/local/bin/versions.sh
RUN /bin/bash --login -c "/usr/local/bin/versions.sh | sudo dd of=/.devenv-versions"
