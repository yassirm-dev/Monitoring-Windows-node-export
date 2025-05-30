
# Monitoring Stack on Minikube with Windows Exporter

This project deploys a full monitoring stack (Prometheus, Grafana, Alertmanager) using the [`prometheus-community/kube-prometheus-stack`](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) Helm chart in a **Minikube** environment. It also integrates **Windows Exporter** to monitor metrics from an external Windows machine. Same implementation can be used for other infrastrastructure such as Databases,Windows server, Ubuntu server, etc using node exporters.

## Features

- Prometheus monitoring for Kubernetes and external nodes
- Grafana dashboards
- Ingress exposure via `prometheus.example.com` and `grafana.example.com`
- Windows Exporter for remote Windows metrics

---

## Prerequisites

Ensure the following are installed:

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- An external Windows machine with [Windows Exporter](https://github.com/prometheus-community/windows_exporter) installed and running

---

## Deployment Steps


### 1. Start Minikube

```bash
minikube start --driver=docker
```

> Make sure Minikube ingress addon is enabled:

```bash
minikube addons enable ingress
```

---

### 2. Add Prometheus Helm Repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

---

### 3. Create Monitoring Namespace

```bash
kubectl create namespace monitoring
```

---

### 4. Create Custom `values.yaml`(Already in the Repo)

- Enable Ingress
- Add `windows_exporter` as a target as directed in the values.yaml file


> Replace `<WINDOWS_IP>` with the actual IP address of your Windows machine.

---

### 5. Install the Monitoring Stack

```bash
helm install monitoring prometheus-community/kube-prometheus-stack   -n monitoring -f values.yaml
```

---

### 6. Update `/etc/hosts` for Local Testing

Add DNS to the lines to your `/etc/hosts` file:

```plaintext
<minikube_ip/cluster_ip> prometheus.monitoring.com grafana.monitoring.com
```

---

### 7. Access the Dashboards

- **Prometheus**: (http://prometheus.monitoring.com)
- **Grafana**: (http://grafana.monitoring.com)

Use:
- **Username**: `admin`
- **Password**: `Prom-operator`

---

## Verify Windows Exporter
1. Prometheus, access all targets through http://prometheus.monitoring.com/targets
2. You should see your Windows machine as a target in Prometheus with name: windows-exporter as described in the values.yaml as a job_name.

## Grafana: Add Windows Dashboard

1. Go to Grafana > Dashboards > New> Import
2. Use Dashboard ID like `10467, 11074 or others` (Windows Node Dashboard). You can as well use the json-model.json file to create for you the dashboard in grafana.
3. Select `Prometheus` as the data source
4. Import and tweak the dashboard the way you like.

---

-----------------------------------Happy monitoring ;)-----------------------------------------



## Uninstall

```bash
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```

---

## References

- [kube-prometheus-stack Helm Chart](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
- [Windows Exporter](https://github.com/prometheus-community/windows_exporter)
- [Minikube Ingress Guide](https://minikube.sigs.k8s.io/docs/handbook/ingress/)

---

## Author

Yassir Ndegwa Mohammed
