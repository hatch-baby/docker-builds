FROM amazonlinux:2023.12.20260930.0   
LABEL maintainer="Ken DeLong <kenwdelong@gmail.com>"
LABEL org.opencontainers.image.source="https://github.com/kenwdelong/docker-builds"
LABEL org.opencontainers.image.description="Custom Python container for Ansible + MySQL tasks"

ENV WORK="/work" \
    ANSIBLE_VERSION=14.5.0 \
    PYTHON_VERSION=3.14 \
    PYTHON_SYSTEM_VERSION=3.9 \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8

RUN dnf -y update &&\ 
    dnf -y install bash-completion less mariadb1011-client-utils openssh openssh-clients openssl openssl-pkcs11 sudo wget zip unzip python${PYTHON_VERSION} 

# system version
RUN curl https://bootstrap.pypa.io/pip/3.9/get-pip.py -o get-pip.py && python${PYTHON_SYSTEM_VERSION} get-pip.py

# install boto for both system python and modern python
# add packaging to stop error in ansible route53 module
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && python${PYTHON_VERSION} get-pip.py  &&\
    pip${PYTHON_VERSION} install ansible==$ANSIBLE_VERSION &&\
    pip${PYTHON_VERSION} install packaging &&\                 
    pip${PYTHON_SYSTEM_VERSION} install boto boto3 botocore   &&\     
    pip${PYTHON_VERSION} install boto boto3 botocore PyMySQL

# Install AWS CLI v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o /tmp/awscliv2.zip &&\
    unzip /tmp/awscliv2.zip -d /tmp &&\
    /tmp/aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update &&\
    rm -rf /tmp/awscliv2.zip /tmp/aws

RUN mv /etc/localtime /etc/localtime.bak  &&\
    ln -s /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
    
RUN mkdir $WORK && \
    chmod 0777 $WORK
WORKDIR $WORK

ENTRYPOINT ["ansible-playbook"]