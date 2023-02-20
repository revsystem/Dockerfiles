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
ENV RUBY_VERSION="3.2.1" \
    PYTHON_VERSION="3.9.10" \
    TERRAFORM_VERSION="1.3.9" \
    ANSIBLE_VERSION="2.10.17" \
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
        libyaml-devel \
        make \
        openssl \
        openssl-devel \
        openssl-libs \
        # shasum tool. (need for Terraform SHA256 hash validation)
        perl-Digest-SHA \
        procps \
        readline \
        readline-devel \
        sqlite-devel \
        sudo \
        tar \
        tk-devel \
        unzip \
        wget \
        xz-devel \
        yum-utils \
        zlib-devel \
        # install keybase (need for Teffarom OpenPGP signature verification)
        https://prerelease.keybase.io/keybase_amd64.rpm \
    && amazon-linux-extras install epel -y \
    && yum install -y sshpass \
    && yum clean all \
    && rm -rf /var/cache/yum \
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
    && git clone https://github.com/yyuu/pyenv.git ~/.pyenv \
    && echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile \
    && echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile \
    && echo 'eval "$(pyenv init -)"' | tee -a ~/.bash_profile \
    # install rbenv.
    && git clone https://github.com/sstephenson/rbenv.git ~/.rbenv \
    && git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build \
    && echo 'eval "$(~/.rbenv/bin/rbenv init - bash)"' >> ~/.bash_profile \
    # aws-cli
    && echo 'export PATH="/usr/local/bin/aws:$PATH"' | tee -a ~/.bash_profile \
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
    && pip3 install --upgrade pip \
    # install wheel
    && pip3 install wheel \
    # install Ansible.
    && pip3 install ansible~=${ANSIBLE_VERSION}

