# Helm Chart Examples

## Introduction

This repository contains a collection of practical Helm chart examples. It is intended to help anyone who wants to get started with Helm charts and Kubernetes by providing simple, hands-on references.


## The `artifacts` directory

This directory contains packaged Helm charts that are ready to be deployed to a Kubernetes cluster.

## The `mandan-site` directory

This directory includes a Helm chart that defines a lightweight web service together with a **MongoDB** backend. The chart also provisions related Kubernetes resources such as `PersistentVolumes` and `Ingress` as part of the deployment.

## Helm packaging

```sh
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ ll -hrt
total 8
drwxr-xr-x@ 2 thaovtran  1497946264    64B Jan 19 16:04 redorblue-app
drwxr-xr-x@ 7 thaovtran  1497946264   224B Jan 19 23:59 mandan-site
drwxr-xr-x@ 7 thaovtran  1497946264   224B Jan 20 14:06 artifacts
-rw-r--r--@ 1 thaovtran  1497946264   120B Jan 20 14:42 README.md
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ helm package mandan-site -d artifacts
Successfully packaged chart and saved it to: artifacts/mandan-site-0.1.11.tgz
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ ll -hrt artifacts
total 120
-rw-r--r--@ 1 thaovtran  1497946264   8.7K Nov  7  2024 tinyweb-app-mongo-0.1.0.tgz
-rw-r--r--@ 1 thaovtran  1497946264   9.8K Nov 12  2024 mandan-site-0.1.8.tgz
-rw-r--r--@ 1 thaovtran  1497946264   9.8K Nov 19  2024 mandan-site-0.1.9.tgz
-rw-r--r--@ 1 thaovtran  1497946264   9.8K Jan 14 17:13 mandan-site-0.1.10.tgz
-rw-r--r--@ 1 thaovtran  1497946264    11K Jan 20 14:06 mandan-site-0.1.11.tgz
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
```

## Helm upgrading / installing
```sh
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ helm upgrade --install tinyweb mandan-site --create-namespace --namespace mandan-web-ns
Release "tinyweb" has been upgraded. Happy Helming!
NAME: tinyweb
LAST DEPLOYED: Tue Jan 20 14:44:41 2026
NAMESPACE: mandan-web-ns
STATUS: deployed
REVISION: 2
NOTES:
*** If ingress is enabled:
1. Access Mongo Express: http://mandandb.k8s.local
2. Access ManDan website: http://mandanweb.k8s.local
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
```

```sh
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ helm upgrade --install tinyweb ./artifacts/mandan-site-0.1.11.tgz --create-namespace --namespace mandan-web-ns
Release "tinyweb" has been upgraded. Happy Helming!
NAME: tinyweb
LAST DEPLOYED: Tue Jan 20 14:45:37 2026
NAMESPACE: mandan-web-ns
STATUS: deployed
REVISION: 3
NOTES:
*** If ingress is enabled:
1. Access Mongo Express: http://mandandb.k8s.local
2. Access ManDan website: http://mandanweb.k8s.local
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
```

## Helm list / history
```sh
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ helm list --namespace=mandan-web-ns
NAME   	NAMESPACE    	REVISION	UPDATED                             	STATUS  	CHART             	APP VERSION
tinyweb	mandan-web-ns	3       	2026-01-20 14:45:37.304339 +0700 +07	deployed	mandan-site-0.1.11	1.0.2
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
```

```sh
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗ helm history tinyweb --namespace=mandan-web-ns
REVISION	UPDATED                 	STATUS    	CHART             	APP VERSION	DESCRIPTION
1       	Tue Jan 20 11:45:53 2026	superseded	mandan-site-0.1.11	1.0.2      	Install complete
2       	Tue Jan 20 14:44:41 2026	superseded	mandan-site-0.1.11	1.0.2      	Upgrade complete
3       	Tue Jan 20 14:45:37 2026	deployed  	mandan-site-0.1.11	1.0.2      	Upgrade complete
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
➜  …/Kubernetes/my-done-helm-charts git:(dev) ✗
```
