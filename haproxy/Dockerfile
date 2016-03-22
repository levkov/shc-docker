FROM centos:7
MAINTAINER levkov

RUN rm -f /etc/localtime && ln -sf /usr/share/zoneinfo/UTC /etc/localtime
RUN yum update -y
RUN rpm -iUvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm && \
    yum install -y python-pip openssh-server openssh-clients haproxy wget unzip && \
    pip install supervisor requests==2.5.3

RUN groupadd -r siteop && useradd -r -g siteop siteop && \
    echo 'root:ContaineR' | chpasswd

RUN rm -f /etc/ssh/ssh_host_ecdsa_key /etc/ssh/ssh_host_rsa_key && \
    ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_ecdsa_key && \
    ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key && \
    sed -i "s/#UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g" /etc/ssh/sshd_config && \
    sed -i "s/UsePAM.*/UsePAM yes/g" /etc/ssh/sshd_config

COPY conf/supervisord.conf /etc/supervisord.conf
# COPY conf/haproxy.cfg /etc/haproxy/haproxy.cfg
COPY conf/haproxy.json /etc/haproxy/haproxy.json
COPY conf/haproxy.ctmpl /etc/haproxy/haproxy.ctmpl

RUN cd /tmp && wget https://releases.hashicorp.com/consul-template/0.12.2/consul-template_0.12.2_linux_amd64.zip && \
    unzip consul-template_0.12.2_linux_amd64.zip && mv consul-template /usr/local/bin

EXPOSE 9001 22 80
CMD ["/usr/bin/supervisord"]
