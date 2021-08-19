# Kubernetes Cluster Monitoring

## Using:

- Prometheus
- Alert Manager
- Grafana

## Docker images version:

- Prometheus : prom/prometheus:v2.29.1
- Alert Manager: prom/alertmanager:v0.19.0
- Grafana: grafana/grafana:latest
- Kube State Metrics: k8s.gcr.io/kube-state-metrics/kube-state-metrics:v2.1.1

## Notes:

- This repository has been forked from Bibin Wilson's repository with some changes ([Repos link](https://github.com/bibinwilson/))
- Grafana uses dynamic local storage provisioner from Rancher Team ([Repo link](https://github.com/rancher/local-path-provisioner)) as persistent volume mounted at `/var/lib/grafana/`
- We did not (yet) use persistent volume for Prometheus

## How to install:

### Clone the repository

`git clone https://github.com/alphonse-rms/k8s-prometheus-grafana-alertmgr.git`

`cd k8s-prometheus-grafana-alertmgr`

### Install Kube State Metrics first

`kubectl apply -f kube-state-metrics/`

or install latest version from [here](https://github.com/kubernetes/kube-state-metrics/tree/master/examples/standard)

### Install Prometheus

Prometheus configurations are stored in the `kubernetes-prometheus/prometheus-configmap.yaml` Edit it to match your target configurations. Then apply all the related manifests:

`kubectl apply -f kubernetes-prometheus/`

### Install Alert Manager

Alert Manager configurations are stored in the `kubernetes-alert-manager/alertmanager-config-configmap.yaml`

By default, Alert Manager sends Mail alerts using the GMail SMTP Server. You can edit the ConfigMap to match your needs. See the documentation [here](https://prometheus.io/docs/alerting/latest/configuration/)

If you want to use the default GMail SMTP Server configurations, just replace the variables in the ConfigMap

|     |     |
| --- | --- |
| Variable | Value |
| $MAIL_DEST | Any mail address to send the alert message |
| $GMAIL_ACCOUNT | Sender's mail address |
| $GMAIL\_APP\_PASSWORD | Sender's generated app password. Learn how to create Gmail app password [here](https://support.google.com/accounts/answer/185833)<br>or<br>Sender's account password (\*\*\*Requires turning ON unsecure protocols in Google Account Settings, \*\*\***NOT Recommended**) |

Now you can install Alert Manager components

`kubectl apply -f kubernetes-alert-manager`

### Install Grafana

Install the dynamic local storage provisioner first. You can skip this step if you already use it

`kubectl apply -f local-path-storage/`

`kubectl -n local-path-storage get pod --watch`

Wait until the Pod is ready, then apply Grafana manifests

`kubectl apply -f kubernetes-grafana/`

## Accessing Web GUI

By default, Prometheus, Grafana and AlertManager services are reachable by service NodePort and Ingress Host rules.

You can edit each service manifest to change them.

- Prometheus: *:30800
- Alert Manager: *:31000
- Grafana: *:32000
