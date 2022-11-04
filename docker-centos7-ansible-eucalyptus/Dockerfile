FROM geerlingguy/docker-centos7-ansible:python3

RUN yum makecache fast \
 && yum install -y https://downloads.eucalyptus.cloud/software/eucalyptus/5/rhel/7/x86_64/eucalyptus-release-5-1.11.as.el7.noarch.rpm \
 && yum install -y eucalyptus-ansible \
 && yum install -y openssh-clients \
 && yum clean all
