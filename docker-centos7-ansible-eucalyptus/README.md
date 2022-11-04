## Build image
```
docker build -t centos7-ansible-eucalyptys .
```

## Requirements
> :warning: although not said in the documentation you **must have a DNS server available in your network**, for cloud-in-a-box you should point /etc/resolv.conf to that server e.g. pxe.server.lan

Bellow you may find a sample configuration for dnsmasq

```
server=10.4.40.1

server=/ciab-10-4-40-127.euca.me/10.4.40.127
server=/128.254.168.192.in-addr.arpa/10.4.40.127
```

## Usage
```
cd eucalyptus-ansible
docker run -it --rm -v $HOME/.ssh:/root/.ssh -v $PWD:/ansible -w /usr/share/eucalyptus-ansible/ --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro  centos7-ansible-eucalyptys:latest bash

eval $(ssh-agent)
ssh-add

ansible-playbook --extra-vars cloud_instances_state_dir=/home/eucalyptus/instances --extra-vars cloud_instances_conf_work_size=21000 --inventory /ansible/inventory_cloud_in_a_box_remote.yml playbook_vpcmido.yml -vvvv
```

## Config *after install*

### To enable eucalyptus/admin web console access run:

```
euare-useraddloginprofile --as-account eucalyptus -u admin -p PASSWORD
```

### To generate/import an SSH keypair run:

```
[ -f ~/.ssh/id_rsa ] || ssh-keygen -N '' -f ~/.ssh/id_rsa
euca-import-keypair -f ~/.ssh/id_rsa.pub KEYNAME
```

### To enable instance SSH access in the default security group run:

```
euca-authorize -P tcp -p 22 default
```

### To add machine images (ami/emi) to your cloud run:

```
eucalyptus-images
```

> :warning: at the time of writing ubuntu images were hosted in a site that didn't had a valid cert. Follow the bellow steps to workaround the problem

```
vi /usr/local/bin/eucalyptus-images
---
sed -i 's/download_path]):/download_path, "--no-check-certificate"]):/' "${TEMP_DIR}/overlay/eucalyptus/images.py"
```

#### To make an image public

```
euca-modify-image-attribute -l -a all IMAGE_ID
```

## Copy eucalyptus-ansible playbook (comes in RPM) to local machine
### on docker
```
tar -czvf /ansible/eucalyptus-ansible.tgz /usr/share/eucalyptus-ansible/
```
### on local machine
```
tar -xzvf ~/Documents/Personal/docker-centos7-ansible/docker-centos7-ansible-eucalyptus/eucalyptus-ansible/eucalyptus-ansible-5.0.102-0.tgz --strip-components 3 -C ~/Documents/Personal/docker-centos7-ansible/docker-centos7-ansible-eucalyptus/eucalyptus-ansible/
```