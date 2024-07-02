FROM ubuntu:18.04

#update  package repository 
RUN apt-get update && \
    apt-get install -qy git && \
# Install  SSH server
    apt-get install -qy openssh-server && \
    sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd && \
    mkdir -p /var/run/sshd && \
# Install JDK 17
    apt-get install -qy openjdk-17-jdk openjdk-17-jre && \
# Install maven and curl
    apt-get install -qy maven && \
    apt-get install -y curl && \
# Cleanup old packages
    apt-get -qy autoremove && \
# Add user jenkins to the image
    adduser --quiet jenkins && \
# Set password for the jenkinsi
    echo "jenkins:Slave2020" | chpasswd && \
    mkdir /home/jenkins/.m2

# Copy authorized keys
COPY .ssh/authorized_keys /home/jenkins/.ssh/authorized_keys
COPY .ssh/id_rsa /home/jenkins/.ssh/id_rsa

RUN chown -R jenkins:jenkins /home/jenkins/.m2/ && \
    chown -R jenkins:jenkins /home/jenkins/.ssh/

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
