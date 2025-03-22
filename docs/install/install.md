# Install Examples

```
apiVersion: v1
baseDomain: ocp.example.com
metadata:
  name: dev
compute:
- architecture: amd64
  hyperthreading: Enabled
  name: worker
  replicas: 0
controlPlane:
  architecture: amd64
  hyperthreading: Enabled
  name: master
  replicas: 3
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 10.3.0.0/24
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  baremetal:
    apiVIP: 10.3.0.9
    ingressVIP: 10.3.0.10
pullSecret: '<redacted>'
sshKey: '<redacted>'
```