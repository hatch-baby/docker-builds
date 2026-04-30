FROM 966878126306.dkr.ecr.us-west-2.amazonaws.com/java:java-25.0.3

LABEL maintainer="Hatch Engineering <ops@hatch.co>"
LABEL org.opencontainers.image.source="https://github.com/hatch-baby/docker-builds"
LABEL org.opencontainers.image.description="Container with Java, Jenkins, Maven, and Ansible"

# Version
# Look here for jenkins http://mirrors.jenkins.io/war-stable/
# Python https://docs.aws.amazon.com/linux/al2023/ug/python.html
# Ansible 10.2.0 requires Python 3.10 or greater
ENV MAVEN_VERSION=3.9.15 \
    ANSIBLE_VERSION=13.6.0 \
    JENKINS_VERSION=2.555.1 \
    PYTHON_VERSION=3.13 \
    UV_VERSION=0.11.8 \
    MAVEN_HOME=/usr/local/maven \
    MAVEN_TMP_FILE=/tmp/maven.tar.gz \
    JENKINS_INSTALL=/usr/local/jenkins/bin \
    JENKINS_HOME=/jenkins \
    JENKINS_TIMEZONE=America/Los_Angeles \
    GET_PIP_FILE=/tmp/get-pip.py \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8
    

# Timezone
RUN  mv /etc/localtime /etc/localtime.bak && ln -s /usr/share/zoneinfo/${JENKINS_TIMEZONE} /etc/localtime
    
# Install extra packages
RUN dnf -y install dejavu-sans-fonts fontconfig git wget gcc python${PYTHON_VERSION}-devel libffi-devel openssl-devel unzip net-tools procps-ng python${PYTHON_VERSION} libatomic &&\
    dnf clean all &&\
    rm -rf /var/cache/dnf /var/cache/yum

# Install Maven  
RUN mkdir -p $MAVEN_HOME  && \
    wget --quiet --output-document=$MAVEN_TMP_FILE \
        "https://dlcdn.apache.org/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz"  && \ 
    tar -xf "$MAVEN_TMP_FILE" --directory="$MAVEN_HOME" --strip-components 1  && \
    rm $MAVEN_TMP_FILE

# Install Ansible, AWS-CLI, Boto (AWS lib in Python), just
RUN curl "https://bootstrap.pypa.io/get-pip.py" -o "$GET_PIP_FILE" &&\
    python${PYTHON_VERSION} $GET_PIP_FILE &&\
    pip${PYTHON_VERSION} install ansible==$ANSIBLE_VERSION &&\
    pip${PYTHON_VERSION} install awscli &&\
    pip${PYTHON_VERSION} install boto boto3 botocore &&\
    pip${PYTHON_VERSION} install rust-just &&\
    rm -f $GET_PIP_FILE &&\
    rm -rf /tmp/pip_build_root

## Install AWS SAM CLI using zip file from the latest release
#RUN curl -Lo /tmp/aws-sam-cli-linux-x86_64.zip https://github.com/aws/aws-sam-cli/releases/latest/download/aws-sam-cli-linux-x86_64.zip && \
#    unzip /tmp/aws-sam-cli-linux-x86_64.zip -d /tmp/sam-install && \
#    /tmp/sam-install/install --update --bin-dir /usr/local/bin --install-dir /usr/local/aws-sam-cli && \
#    rm -rf /tmp/aws-sam-cli-linux-x86_64.zip /tmp/sam-install && \
#    sam --version

# Install uv
RUN curl -LsSf https://astral.sh/uv/$UV_VERSION/install.sh | UV_INSTALL_DIR=/usr/local/bin sh

# Install Jenkins
RUN mkdir -p $JENKINS_HOME &&\
    mkdir -p $JENKINS_INSTALL &&\
    wget --quiet --output-document=$JENKINS_INSTALL/jenkins.war "https://mirrors.jenkins.io/war-stable/$JENKINS_VERSION/jenkins.war" &&\
    chmod 0644 $JENKINS_INSTALL/jenkins.war

# Jenkins' home is in /jenkins

ENV PATH=$MAVEN_HOME/bin:$PATH

EXPOSE 8080

RUN useradd -r -u 201 -m -c "jenkins role account" -d /home/jenkins -s /bin/false jenkins
RUN chown jenkins $JENKINS_HOME
USER jenkins

CMD java -Xmx2048m -Dorg.apache.commons.jelly.tags.fmt.timeZone=$JENKINS_TIMEZONE -jar $JENKINS_INSTALL/jenkins.war
