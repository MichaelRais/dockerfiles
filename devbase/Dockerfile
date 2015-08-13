############################################################
# Dockerfile to build basic development image
# Based on Ubuntu
#
# Example Usage: sudo docker build -t tmp_devbase
# REF:  https://docs.docker.com/articles/dockerfile_best-practices/
############################################################

# Set the base image to Ubuntu 
FROM ubuntu
# NOTE: FROM directive defines the base image to use to start the build process. If a FROM image is not found on the host, docker will try to find it and download from the docker image index. It needs to be the first command declared inside a Dockerfile.

# File Author / Maintainer
MAINTAINER Mike Rais

# Update the repository sources list
RUN apt-get update

# Install newer versions of packages (as repo sources list was just updated by apt-get update)
RUN apt-get upgrade -y

# Timezone change if necessary - see neighboring tzdata_preseed.txt file
ENV DEBIAN_FRONTEND noninteractive
ENV DEBCONF_NONINTERACTIVE_SEEN true
COPY tzdata_preseed.txt /tmp/tzdata_preseed.txt
RUN debconf-set-selections /tmp/tzdata_preseed.txt
RUN dpkg-reconfigure tzdata


################## BEGIN UTILITIES ######################
RUN TERM=linux apt-get -y install man-db
RUN apt-get install wget
RUN apt-get install curl
RUN apt-get install tree
RUN apt-get -y install netcat
RUN TERM=linux apt-get install screen
RUN TERM=linux apt-get -y install mysql-client
RUN TERM=linux apt-get -y install git
RUN TERM=linux apt-get -y install iptables
RUN apt-get install strace
RUN TERM=linux apt-get -y install openssh-server
# REF: ssh hardening before starting!  https://help.ubuntu.com/community/SSH/OpenSSH/Configuring#disable-password-authentication


################## BEGIN ROOT ALIASES ######################
RUN touch /root/.bash_aliases
RUN echo "alias llh='ls -lh'" >> /root/.bash_aliases
RUN echo "alias find.0='find ./ -type f -size 0'" >> /root/.bash_aliases
RUN echo "alias find.large='find ./ -type f -size +1M -exec ls -lh {} \;'" >> /root/.bash_aliases


################## BEGIN USER ######################
RUN mkdir -p /home/developer
RUN chown 1000:1000 /home/developer

# Replace 1000 with your user / group id
RUN export uid=1000 gid=1000 && \
    echo "developer:x:${uid}:${gid}:Developer,,,:/home/developer:/bin/bash" >> /etc/passwd && \
    echo "developer:x:${uid}:" >> /etc/group && \
    echo "developer ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/developer && \
    chmod 0440 /etc/sudoers.d/developer && \
    chown ${uid}:${gid} -R /home/developer  


################## BEGIN VOLUMES ######################
VOLUME /home
#VOLUME /data


################## BEGIN ACCESS ######################
# Expose a port
#EXPOSE 22 80 443

# Set default container command
#ENTRYPOINT Any command/executable to run at start


