# ManDan TinyWebsite

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/kubernetes/dashboard/blob/master/LICENSE)

## TL;DR

```console
# Add mandan-site repository
helm repo add mandan-site https://thawtran.github.io/my-charts

# Deploy a Helm Release named "tinyweb" using the mandan-site chart
helm upgrade --install tinyweb mandan-site/mandan-site --create-namespace --namespace mandan-web-ns
```

## Pre-requisite

This chart requires the `ingress-nginx` installed in your Kubernetes cluster if the value `{{.Values.ingress.enabled }}` is set.

The `mandandb.k8s.local` and `mandanweb.k8s.local` need to be set in the `/etc/hosts` file.
```console
cat /etc/hosts
127.0.0.1       mandandb.k8s.local
127.0.0.1       mandanweb.k8s.local
```

## Introduction

This chart create a **TinyWebsite** and **MongoDB** as well as **MongoExpress** deployments on a [Kubernetes](https://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

<img src="https://raw.githubusercontent.com/thawtran/my-charts/master/photos/manucian.png" alt="Manucian" width="50%">
<img src="https://raw.githubusercontent.com/thawtran/my-charts/master/photos/mongoexp.png" alt="MongoExp" width="50%">

## Work with chart

### Install

To install the [Chart](https://helm.sh/docs/intro/using_helm/#three-big-concepts) with the [Release](https://helm.sh/docs/intro/using_helm/#three-big-concepts) name `tinyweb` in the namespace `mandan-web-ns`:

```console
helm repo add mandan-site https://thawtran.github.io/my-charts
helm upgrade --install tinyweb mandan-site/mandan-site --create-namespace --namespace mandan-web-ns
```

The command deploys **ManDan TinyWebsite** on the Kubernetes cluster in the `mandan-web-ns` namespace with default
configuration.
The [configuration](#configuration) section lists the parameters that can be configured during installation.

### Test

### Uninstall

To uninstall/delete the `tinyweb` deployment:

```console
helm delete tinyweb --namespace mandan-web-ns
kubectl delete ns mandan-web-ns
```

The command removes all the **ManDan TinyWebsite** components associated with the chart and deletes the release.


### Access

**If ingress is enabled:**
  - Access Mongo Express: `http://mandandb.k8s.local`
  - Access ManDan website: `http://mandandb.k8s.local`

## Handle TLS cert

This is the fake cert `templates/secret/local-tls.yaml` covers the main `*.k8s.local`.

### Generate

```sh
➜  …/Works/Tmp
➜  …/Works/Tmp openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout my-k8s-tls.key -out my-k8s-tls.crt -subj "/CN=*.k8s.local" -days 365
..+++++++++++++++++++++++++++++++++++++++++++++*..+......+.....+.......+..................+.....+.+......+..+.+......+...+..+.........+......+...+...+...+.......+..+......+.......+...+.....+.......+..+++++++++++++++++++++++++++++++++++++++++++++*.............+......+...+..........+++++
..+......+......+.......+...+..+..........+......+..+....+......+...+..+....+.....+.+..+.............+..+...+...+......+.+...............+++++++++++++++++++++++++++++++++++++++++++++*...+.+..+...+.+........+......+.+...........+.............+......+++++++++++++++++++++++++++++++++++++++++++++*........+...................+..+...+.+.....+....+...............+++++
-----
➜  …/Works/Tmp
➜  …/Works/Tmp ll -h
-rw-------@  1 thaovtran  KMS\Domain Users   3.2K Jan 14 17:01 my-k8s-tls.key
-rw-r--r--@  1 thaovtran  KMS\Domain Users   1.8K Jan 14 17:01 my-k8s-tls.crt
➜  …/Works/Tmp
```

### Show

```sh
➜  …/Works/Tmp openssl x509 -in my-k8s-tls.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            62:3b:ce:90:75:60:82:dc:91:35:be:6f:e6:92:2b:36:03:f3:db:2e
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN=*.k8s.local
        Validity
            Not Before: Jan 14 10:01:53 2026 GMT
            Not After : Jan 14 10:01:53 2027 GMT
        Subject: CN=*.k8s.local
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:b1:7e:05:19:d3:54:0e:7c:c3:56:e6:ac:ac:9d:
                    38:0c:ac:d4:6b:b8:43:ac:97:5b:40:96:55:e1:39:
                    ...
                    ...
                    b9:7a:af:3d:42:99:e2:9d:5a:bf:48:f9:ba:e6:51:
                    3b:ab:10:4e:41:65:42:a2:71:93:6f:a9:a3:a2:b8:
                    e8:a1:dd
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                3C:27:BF:DE:22:99:C3:E2:B5:43:9D:83:70:22:50:AD:3B:72:9C:79
            X509v3 Authority Key Identifier:
                3C:27:BF:DE:22:99:C3:E2:B5:43:9D:83:70:22:50:AD:3B:72:9C:79
            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        5b:53:6d:95:46:2b:36:a7:da:d5:6b:4f:80:0f:a0:c7:e9:d0:
        9d:6e:e4:8b:0d:c1:f1:dd:1e:fe:0e:05:05:9e:8e:ec:ed:3d:
        ...
        ...
        ...
        b0:73:7b:ea:54:e7:d8:3f:1a:44:e1:3d:53:36:9e:5f:4d:8e:
        45:90:2b:19:ab:c6:d3:2a
➜  …/Works/Tmp
```

### Apply

Use the `cat` and `base64` commands to decrypt content of the `.key` and `.cert` files then copying to the `values.yaml` file.

```sh
➜  …/Works/Tmp
➜  …/Works/Tmp cat my-k8s-tls.crt | base64
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUZEVENDQXZXZ0F3SUJBZ0lVWWp2T2tIVmdndHlSTmI1......UMVROcDVmVFk1RmtDc1pxOGJUCktnPT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
➜  …/Works/Tmp
➜  …/Works/Tmp cat my-k8s-tls.key | base64
LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCk1JSUpSQUlCQURBTkJna3Foa2lHOXcwQkFRRUZBQVNDQ1M0......Mk05UnUwTlNYREhkMW9ReXBmbGJaWVlBVjF4MGN4R3lyQTdqV01UQzlNdTZVN1FzRXd6eVV3eHc3d3VMSnQKZ3BIcEd6MktiUkg4MFdNVmN0UG5TaTRHeEE0MGI3aU8KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQo=
➜  …/Works/Tmp
```
