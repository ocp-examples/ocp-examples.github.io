# OpenShift Install

## DNS



## Install CLIs

``` bash 
mkdir -p ~/bin
mkdir -p /tmp/ocp
curl -o /tmp/ocp/openshift-client-linux.tar.gz https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-client-linux.tar.gz
tar -xvf /tmp/ocp/openshift-client-linux.tar.gz -C ~/bin

curl -o /tmp/ocp/openshift-install-linux.tar.gz https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz
tar -xvf /tmp/ocp/openshift-install-linux.tar.gz -C ~/bin

sudo dnf install /usr/bin/nmstatectl -y
```

## Cluster Install Command Reference

### Create ISO
``` bash 
rm -rf install
mkdir install
cp agent-config.yaml install-config.yaml install
openshift-install agent create image --dir=install --log-level=debug
```

### Wait for Bootstrap
``` bash
openshift-install agent wait-for bootstrap-complete --dir=install --log-level=debug
```

### Wait for Install to Complete
``` bash
openshift-install agent wait-for install-complete --dir=install --log-level=debug
```
