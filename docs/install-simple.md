# OpenShift Install Configurations

## Simple 3 Node Install with basic networking

``` yaml title="install-config.yaml"
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
  - cidr: 10.1.0.0/24
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  baremetal:
    apiVIP: 10.1.0.9
    ingressVIP: 10.1.0.10
pullSecret: '<redacted>'
sshKey: '<redacted>'
```

``` yaml title="agent-config.yaml"
apiVersion: v1alpha1
kind: AgentConfig
metadata:
  name: dev
rendezvousIP: 10.1.0.11
hosts:
  - hostname: control-plane-01
    role: master
    interfaces:
      - name: eno1
        macAddress: 06:00:00:00:00:01
    rootDeviceHints:
      deviceName: "/dev/sda"
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:01
          ipv4:
            enabled: true
            address:
              - ip: 10.1.0.11
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 10.1.0.2
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.1.0.1
            next-hop-interface: eno1
            table-id: 254
  - hostname: control-plane-02
    role: master
    interfaces:
      - name: eno1
        macAddress: 06:00:00:00:00:02
    rootDeviceHints:
      deviceName: "/dev/sda"
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:02
          ipv4:
            enabled: true
            address:
              - ip: 10.1.0.12
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 10.1.0.2
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.1.0.1
            next-hop-interface: eno1
            table-id: 254
  - hostname: control-plane-03
    role: master
    interfaces:
      - name: eno1
        macAddress: 06:00:00:00:00:03
    rootDeviceHints:
      deviceName: "/dev/sda"
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:03
          ipv4:
            enabled: true
            address:
              - ip: 10.1.0.13
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 10.1.0.2
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.1.0.1
            next-hop-interface: eno1
            table-id: 254
```

## OpenShift AgentConfig with LACP Bond Interface Configuration

``` yaml title="agent-config.yaml"
apiVersion: v1alpha1
kind: AgentConfig
metadata:
  name: ocp
rendezvousIP: 10.1.0.11
hosts:
  - hostname: control-plane-01
    role: master
    interfaces:
      - name: eno1
        macAddress: 06:00:00:00:00:11
      - name: eno2
        macAddress: 06:00:00:00:00:12
    rootDeviceHints:
      deviceName: "/dev/sda"
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:11
        - name: eno2
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:12
        - name: bond0
          type: bond
          state: up
          ipv4:
            enabled: true
            address:
              - ip: 10.1.0.11
                prefix-length: 24
            dhcp: false
          link-aggregation:
            mode: 802.3ad
            port:
              - eno1
              - eno2
            options:
              miimon: '100'
              lacp_rate: 'fast'
              xmit_hash_policy: 'layer3+4'
      dns-resolver:
        config:
          server:
            - 10.1.0.2
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.1.0.1
            next-hop-interface: bond0
            table-id: 254

  - hostname: control-plane-02
    role: master
    interfaces:
      - name: eno1
        macAddress: 06:00:00:00:00:21
      - name: eno2
        macAddress: 06:00:00:00:00:22
    rootDeviceHints:
      deviceName: "/dev/sda"
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:21
        - name: eno2
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:21
        - name: bond0
          type: bond
          state: up
          ipv4:
            enabled: true
            address:
              - ip: 10.1.0.12
                prefix-length: 24
            dhcp: false
          link-aggregation:
            mode: 802.3ad  # LACP - Link Aggregation Control Protocol
            port:
              - eno1
              - eno2
            options:
              miimon: '100'
              lacp_rate: 'fast'
              xmit_hash_policy: 'layer3+4'
      dns-resolver:
        config:
          server:
            - 10.1.0.2
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.1.0.1
            next-hop-interface: bond0
            table-id: 254

  - hostname: control-plane-03
    role: master
    interfaces:
      - name: eno1
        macAddress: 06:00:00:00:00:31
      - name: eno2
        macAddress: 06:00:00:00:00:32
    rootDeviceHints:
      deviceName: "/dev/sda"
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:31
        - name: eno2
          type: ethernet
          state: up
          mac-address: 06:00:00:00:00:32
        - name: bond0
          type: bond
          state: up
          ipv4:
            enabled: true
            address:
              - ip: 10.1.0.13
                prefix-length: 24
            dhcp: false
          link-aggregation:
            mode: 802.3ad
            port:
              - eno1
              - eno2
            options:
              miimon: '100'
              lacp_rate: 'fast'
              xmit_hash_policy: 'layer3+4'
      dns-resolver:
        config:
          server:
            - 10.1.0.2
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.1.0.1
            next-hop-interface: bond0
            table-id: 254
```