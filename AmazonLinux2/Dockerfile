FROM amazonlinux:2 as installer

# install awscli_v2.
RUN set -x \
    && yum install -y unzip \
        && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
        && unzip awscliv2.zip \
        && ./aws/install -i /usr/local/aws-cli -b /usr/local/bin/aws



FROM amazonlinux:2
LABEL maintainer="<tsuyoshi@rev-system.net>" \
        description="Amazon Linux 2 with some development environments"

# set package version.
ENV XPANES_VERSION="4.1.1" \
    RUBY_VERSION="2.7.1" \
    PYTHON_VERSION="3.8.2" \
    TERRAFORM_VERSION="0.12.24" \
    ANSIBLE_VERSION="2.9.7" \
    USERNAME="ec2-user" \
    PASSWORD="ec2-user"

# install packages.
RUN set -x \
    && yum update -y && yum install -y \
        bzip2 \
        bzip2-devel \
        curl \
        gcc-c++ \
        git \
        jq \ 
        less \
        libffi-devel \
        make \
        openssl \
        openssl-devel \
        # shasum tool. (need for Terraform SHA256 hash validation)
        perl-Digest-SHA \
        procps \
        readline \
        readline-devel \
        sqlite-devel \
        sudo \
        tar \
        unzip \
        wget \
        zlib-devel \
        # install tmux-xpanes.
        https://github.com/greymd/tmux-xpanes/releases/download/v${XPANES_VERSION}/tmux-xpanes_v${XPANES_VERSION}.rpm \
        # install keybase (need for Teffarom OpenPGP signature verification)
        https://prerelease.keybase.io/keybase_amd64.rpm \
    && yum clean all \
    # add user.
    && useradd -m -r -G wheel -s /bin/bash ${USERNAME} \
    && echo "${USERNAME}:${PASSWORD}" | chpasswd \
    && echo '%wheel ALL=(ALL) ALL' | EDITOR='tee -a' visudo

COPY --from=installer /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=installer /usr/local/bin/ /usr/local/bin/

USER ${USERNAME}
WORKDIR /home/${USERNAME}

    # install tfenv.
RUN git clone https://github.com/tfutils/tfenv.git ~/.tfenv \
    && echo 'export PATH="$HOME/.tfenv/bin:$PATH"' | tee -a ~/.bash_profile \
    # install pyenv.
    && git clone git://github.com/yyuu/pyenv.git ~/.pyenv \
    && echo 'export PATH="$HOME/.pyenv/shims:$HOME/.pyenv/bin:$PATH"' | tee -a ~/.bash_profile \
    && echo 'eval "$(pyenv init -)"' | tee -a ~/.bash_profile \
    # install rbenv.
    && git clone https://github.com/sstephenson/rbenv.git ~/.rbenv \
    && git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build \
    && echo 'export PATH="$HOME/.rbenv/bin:$PATH"' | tee -a ~/.bash_profile \
    && echo 'eval "$(rbenv init -)"' | tee -a ~/.bash_profile \
    && . ~/.bash_profile \
    # install Terraform.
    && tfenv install ${TERRAFORM_VERSION} \
    && tfenv use ${TERRAFORM_VERSION} \
    # install Python.
    && pyenv install ${PYTHON_VERSION} \
    && pyenv global ${PYTHON_VERSION} \
    # install Ruby.
    && rbenv install ${RUBY_VERSION} \
    && rbenv global ${RUBY_VERSION} \
    # upgrade pip.
    && pip install --upgrade pip \ 
    # install Ansible.
    && pip install ansible==${ANSIBLE_VERSION}

