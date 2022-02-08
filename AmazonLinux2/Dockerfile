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
ENV RUBY_VERSION="2.6" \
    PYTHON_VERSION="3.8" \
    TERRAFORM_VERSION="1.1.5" \
    ANSIBLE_VERSION="2.9.9" \
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
        make \
        openssl \
        openssl-devel \
        procps \
        sshpass \
        sudo \
        tar \
        unzip \
        wget \
        yum-utils \
        zlib-devel \
    # add epel repository.
    && amazon-linux-extras install epel -y \
    # install Python 3.8
    && amazon-linux-extras install -y python${PYTHON_VERSION} \
    # install Ruby
    && amazon-linux-extras install ruby${RUBY_VERSION} \
    # install ansible
    && amazon-linux-extras install ansible2 \
    # add official HashiCorp Linux repository.
    && yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo \
    && yum install -y terraform \
    && yum clean all \
    && rm -rf /var/cache/yum \
    # add user.
    && useradd -m -r -G wheel -s /bin/bash ${USERNAME} \
    && echo "${USERNAME}:${PASSWORD}" | chpasswd \
    && echo '%wheel ALL=(ALL) ALL' | EDITOR='tee -a' visudo

COPY --from=installer /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=installer /usr/local/bin/ /usr/local/bin/
