# docker build -t samplejenkinsonawsecs/workers/jenkinsexample/ubuntu1404 --file src/docker/jenkins-workers/ubuntu1404/Dockerfile .
# docker tag samplejenkinsonawsecs/workers/jenkinsexample/ubuntu1404:latest 762845314743.dkr.ecr.eu-central-1.amazonaws.com/samplejenkinsonawsecs/workers/jenkinsexample/ubuntu1404:latest
# docker push 762845314743.dkr.ecr.eu-central-1.amazonaws.com/samplejenkinsonawsecs/workers/jenkinsexample/ubuntu1404:latest

FROM ubuntu:14.04

# TODO: hier weg, unten evtl. Namen als Standard hin, dann Parameter reingeben wie im tool-suite-Skript
ARG SERVER=server
# TODO: hier weg, stattdessen nach unten, damit nicht alles neu gebaut werden muss
ARG CMAKE_PKG=cmake-3.10.0-trusty-x86_64.deb

ENV LANG C.UTF-8

## # Must have same uid as on host to access files
## RUN useradd --uid `id --user` -ms /bin/bash user
RUN useradd user

#gperf is needed to build zlib in thirdparty
# libpng-dev, libjpeg-dev, libtiff-dev are needed for ctest seq_examples_libwebp-0_4_1
# python-dev is required for crosstool-ng in thirdparty
# software-properties-common provides add-apt-repository
RUN apt-get update && \
    apt-get install -y sudo ccache maven wget unzip \
    g++ gcc-multilib g++-multilib lib32z1-dev libelf-dev \
    libbz2-dev libxml2-utils m4 lsb librsvg2-bin \
    graphviz ninja-build gperf \
    bison flex texinfo \
    help2man gawk libncurses-dev python3-dev python \
    g++-mingw-w64 libpng-dev libjpeg-dev libtiff-dev distcc \
    python-dev software-properties-common

ARG SERVER=10.251.251.10
RUN wget --quiet http://$SERVER/share/cmake-packages/$CMAKE_PKG && dpkg -i ./$CMAKE_PKG && rm $CMAKE_PKG

RUN wget --quiet https://github.com/ninja-build/ninja/releases/download/v1.8.2/ninja-linux.zip && unzip -o ninja-linux.zip -d /usr/bin && rm ninja-linux.zip

RUN add-apt-repository ppa:git-core/ppa && \
  apt-get update && \
  apt-get install -y git



# siehe https://github.com/jenkinsci/docker-agent/blob/master/8/stretch/Dockerfile
RUN mkdir -p /usr/share/jenkins/ && wget --quiet -o /usr/share/jenkins/agent.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/4.5/remoting-4.5.jar
RUN wget --quiet -o /usr/local/bin/jenkins-agent https://raw.githubusercontent.com/jenkinsci/docker-inbound-agent/master/jenkins-agent \
  && chmod +x /usr/local/bin/jenkins-agent

# Alternative Quelle:
# COPY --from=jenkins/inbound-agent:4.3-7 /usr/share/jenkins/agent.jar /usr/share/jenkins/
# COPY --from=jenkins/inbound-agent:4.3-7 /usr/local/bin/jenkins-agent /usr/local/bin/

## RUN wget --quiet http://sampl-jenki-s2zqp149ey6x-163443079.eu-central-1.elb.amazonaws.com/jnlpJars/agent.jar
## java -jar agent.jar -jnlpUrl http://sampl-jenki-s2zqp149ey6x-163443079.eu-central-1.elb.amazonaws.com/computer/test/slave-agent.jnlp -workDir "/var/jenkins"


## RUN echo "user ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/user && \
##    chmod 0440 /etc/sudoers.d/user

USER user
WORKDIR /home/user
ENV HOME /home/user

ENTRYPOINT ["jenkins-agent"]

