# Amazon EC2
FROM amazonlinux:latest

# install OpenSSh
RUN yum -y install openssh-server

# create ssh keys
RUN ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa
RUN ssh-keygen -f /etc/ssh/ssh_host_ecdsa_key -N '' -t ecdsa

# setup for root login
RUN sed -ri 's/^#PermitEmptyPasswords no/PermitEmptyPasswords yes/' /etc/ssh/sshd_config

# select the root password
RUN echo "root:" | chpasswd

EXPOSE 22

# sshd start
CMD ["/usr/sbin/sshd", "-D"]
