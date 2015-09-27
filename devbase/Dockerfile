############################################################
# Dockerfile to build basic development image
# Based on Ubuntu
#
# Example Usage: sudo docker build -t tmp_devbase ./
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
# WARNING: ssh hardening before starting!  https://help.ubuntu.com/community/SSH/OpenSSH/Configuring#disable-password-authentication



################## BEGIN SERVICE CONFIG: SSHD ######################
# NOTE: For ssh to run, '/var/run/sshd' directory needs to be created.  
RUN mkdir /var/run/sshd 
# INSTRUCTION: To run sshd uncomment the following
# EXPOSE 22
# CMD ["/usr/sbin/sshd", "-D"]



################## BEGIN ROOT ALIASES ######################
RUN touch /root/.bash_aliases
RUN echo "alias llh='ls -lh'" >> /root/.bash_aliases
RUN echo "alias find.0='find ./ -type f -size 0'" >> /root/.bash_aliases
RUN echo "alias find.large='find ./ -type f -size +1M -exec ls -lh {} \;'" >> /root/.bash_aliases
RUN echo "alias ssh.show.keyhash='ssh-keygen -l -f /etc/ssh/ssh_host_rsa_key.pub'" >> /root/.bash_aliases
RUN echo "alias osha1='openssl sha1'" >> /root/.bash_aliases



################## BEGIN USER: developer ######################
# NOTE: User has no password until one is assigned

RUN mkdir -p /home/developer
RUN chown 1000:1000 /home/developer

# Replace 1000 with your user / group id
RUN export uid=1000 gid=1000 && \
    echo "developer:x:${uid}:${gid}:Developer,,,:/home/developer:/bin/bash" >> /etc/passwd && \
    echo "developer:x:${uid}:" >> /etc/group && \
    echo "developer ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/developer && \
    chmod 0440 /etc/sudoers.d/developer && \
    chown ${uid}:${gid} -R /home/developer  



################## BEGIN USER: ansible ######################
# NOTE: This user has a password which must be changed or disabled.  
# NOTE: Unlike generic *Nix developer creation above, here using some native commands to demo/do it. Also added ssh steps

RUN useradd ansible
# WARNING: Generic abc123 password needs change/removal in production
RUN echo 'ansible:abc123' | chpasswd -c SHA512
RUN echo 'ansible ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers.d/ansible && \
	echo 'Defaults:deploy !requiretty' >> /etc/sudoers.d/ansible
RUN mkdir -p /home/ansible/.ssh/
# INSTRUCTION: Include your 'id_rsa.pub' public key in the 'authorized_keys' file here.  Or create an ansible key
# ADD authorized_keys /home/ansible/.ssh/
RUN chown -R ansible:ansible /home/ansible/ && chmod 700 /home/ansible/.ssh/ 
RUN	( [ -f /home/ansible/.ssh/authorized_keys ] && chmod 600 /home/ansible/.ssh/authorized_keys ) || \
	echo "\033[0;31m >>>>>Don't forget to create an authorized key file<<<<< \033[0m"
# NOTE: We need a good exit code or docker build halts - the "Don't forget..." text could be anything.  Note ANSI color code text (red, then uncolored)


################## BEGIN VOLUMES ######################
VOLUME /home
#VOLUME /data



################## BEGIN ACCESS RELATED ######################
# Create MOTD
COPY MOTD.txt /etc/motd

# Expose a port
#EXPOSE 80 443

# Set default container command
#ENTRYPOINT Any command/executable to run at start

