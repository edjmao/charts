# Restic Rest Server

[Restic](https://restic.net) is a backup program that fast, secure, and efficient. [Restic Rest Server](https://github.com/restic/rest-server) is a high performance HTTP server that implements restic's REST backend API.

## Introduction

## Prerequisites

## Installing the Chart

Make sure you have the repository enabled, then install the `rest-server` chart.

```shell script
helm repo add edjmao https://edjmao.github.io/charts/pkg
helm repo update
helm install edjmao/rest-server
```

## Configuration

By default, this chart does not provide persistent storage, so any backups made to the system will be saved to an `emptyDir` volume. It is recommended that you enable persistent storage to ensure that your data is backed up safely.

The following table lists common configurable parameters of the chart and their default values. See values.yaml for all available options.

| Parameter    | Description    | Default     |
| --------------------------------------- | --------------------------------------------------------------------------- | ------------------------------------ |
| `image.pullPolicy`                      | Container pull policy                                                       | `IfNotPresent`                       |
| `image.repository`                      | Container image to use                                                      | `restic/rest-server`                 |
| `image.tag`                             | Container image tag to deploy                                               | `0.9.7`                              |
| `persistence.enabled`                   | Whether to use a PVC for persistent storage                                 | `false`                              |
| `persistence.existingClaim`             | The name of a PVC to use                                                    | `""`                                 |
| `persistence.subPath`                   | If set, mount the subdirectory as the root for backups. Useful for PVCs handling multiple instances.| `""`                              |
| `service.type`                          | Kubernetes Service type                                                     | `ClusterIP`                          |
| `service.clusterIP`                     | Static clusterIP or None for headless services                              | `nil`                                |
| `options.disableAuthentication`         | Disable HTTP basic authentication                                           | `false`                |
| `ingress.enabled`                       | Enable ingress controller resource                                          | `false`                              |
| `ingress.annotations`                   | Ingress annotations                                                         | `[]`                                 |
| `ingress.labels`                        | Ingress labels                                                              | `[]`                                 |
| `ingress.hosts[0].host`                 | Hostname for the ingress                                                    | ``                                   |
| `ingress.hosts[0].path`                 | Path within the url structure                                               | ``                                   |
| `ingress.tls[0].secretName`             | Name of the secret for the TLS cert                                         | ``                                   |
| `ingress.tls[0].hosts`                  | List of hostnames for the TLS cert                                          | `[]`                                 |

## Authentication and User Accounts

HTTP Basic Auth is enabled by default. This requires a user to be created to be able to access the server. Create the user by executing the following:

```shell script
$ kubectl exec -it restic-rest-server-xxxxxxxxxx-xxxxx create_user <username>
```

This will prompt you for a new password. To run restic with basic auth, include it between the protocol and URL.

```shell script
$ restic init -r rest:https://username:password@my-restic-host/my-repo
```

The user shall be saved in the `.htpasswd` file in the PVC and will persist across deployments (if persistence is enabled).

To disable basic auth, set `options.disableAuthentication` to `true`.
