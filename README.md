# FTP Server Helm Chart

This Helm chart deploys an FTP server on Kubernetes, based on  `vsftpd` which is a highly secure and fast FTP server, and the `fauria/vsftpd` docker image.

For more details about the `vsftpd` server and its features, visit [vsftpd - Secure, fast FTP server for UNIX-like systems](https://security.appspot.com/vsftpd.html).

The source code and Dockerfile for the `fauria/vsftpd` image can be found in the GitHub repository here: [fauria/docker-vsftpd GitHub repository](https://github.com/fauria/docker-vsftpd).



## Prerequisites

- Kubernetes 1.12+ with a LoadBalancer support
- Helm 3.0+
- PV provisioner support in the underlying infrastructure (if persistence is needed)
- Access to the Bitnami Helm repository for dependencies

## Installing the Chart

First, ensure your Helm repository is up-to-date:

    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo update

To install the chart with the release name `my-ftp-server`:

    helm install my-ftp-server path/to/this/chart

This command deploys the FTP server on the Kubernetes cluster with the default configuration. The [Configuration](#configuration) section lists the parameters that can be configured during installation.



## Configuration

The following table lists the configurable parameters of the FTP server chart and their default values.

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `service.type` | Type of service to expose FTP (e.g., ClusterIP, NodePort, LoadBalancer) | `LoadBalancer` |
| `service.port` | Port for the FTP service command connection | `21` |
| `service.dataPort` | Port for the FTP service data connection (active mode) | `20` |
| `service.pasvMinPort` | Minimum port range for passive mode connections | `21100` |
| `service.pasvMaxPort` | Maximum port range for passive mode connections | `21110` |
| `service.pasvAddress` | External IP address for passive mode responses | `Your External IP` |

> **Note:** You can use the provided `values.yaml` file to set any specific configurations for your deployment. 


## Accessing the FTP Server

After deploying the FTP server, you can access it as follows:

### For LoadBalancer service type:

1. **Get the FTP server's external IP address:**

   It may take a few minutes for the LoadBalancer IP to be available. You can watch the status by running:

       kubectl get svc --namespace <namespace> -w <release-name>

   Then, execute the following command to get the external IP:

       kubectl get svc --namespace <namespace> <release-name> -o jsonpath='{.status.loadBalancer.ingress[0].ip}'

2. **Connect to the FTP server:**

   Use the external IP address obtained above and the port configured (`21` by default) to connect using an FTP client of your choice.

  ```
  ftp <external-ip>
  ```
 


### For other service types:

Your service type (`ClusterIP`, `NodePort`) requires accessing the FTP server using internal Kubernetes DNS or port-forwarding, depending on your setup and requirements.

For port-forwarding:

    kubectl port-forward svc/<release-name> 21:21 --namespace <namespace>

Then, connect to the server using 'localhost' as the host and port `21`.

## Persistence

This chart allows you to enable persistence for the FTP server. This is facilitated through Kubernetes Persistent Volumes and a StorageClass. Refer to the `values.yaml` file and customize it as per your persistence requirements.
