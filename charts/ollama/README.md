# Ollama Helm

![Version: 0.1.1](https://img.shields.io/badge/Version-0.1.1-informational?style=flat-square) ![AppVersion: 0.1.28](https://img.shields.io/badge/AppVersion-0.1.28-informational?style=flat-square)

Chart to deploy Ollama, a Client for running Large Language Models (LLM).

## TL;DR

```sh
helm repo add braveokafor https://braveokafor.github.io/helm-charts/
helm install ollama braveokafor/ollama
```

#### OR:

```sh
helm repo add braveokafor https://charts.braveokafor.com
helm install ollama braveokafor/ollama
```

## Introduction

This chart facilitates the deployment of [Ollama](https://github.com/jmorganca/ollama/) on Kubernetes clusters, offering a wide range of configuration options.

## Source Code

* <https://github.com/jmorganca/ollama/>
* <https://github.com/braveokafor/ollama-helm/>

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| braveokafor | <brave@braveokafor.com> | <https://braveokafor.com/> |

## Installing the Chart

To install the chart with the release name `ollama`:

```sh
helm install ollama braveokafor/ollama
```

The command deploys Ollama on the Kubernetes cluster in the default configuration.

The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `ollama` deployment:

```sh
helm delete ollama
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration and installation details

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
helm install ollama \
  --set ollama.gpu.enabled=true \
    braveokafor/ollama
```
The above command enables GPU support for Ollama.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example:

```console
helm install ollama -f values.yaml braveokafor/ollama
```

Example fully configured `values.yaml`:

```yaml
# values.yaml
ollama:
  gpu:
    enabled: "true"
    num: 1
  models:
    - mistral
    - gemma:2b
    - codellama:python
nodeSelector:
  cloud.google.com/gke-accelerator: "nvidia-tesla-t4"
resources:
  requests:
    memory: 8192Mi
    cpu: 4000m
ingress:
  enabled: true
  hostname: ollama.braveokafor.com
  annotations:
    kubernetes.io/ingress.global-static-ip-name: ollama
    ingressClassName: gce
  tls:
    enabled: true
    selfSigned: true
```

## Features
- **GPU Integration**: Enable GPU support (NVIDIA).
- **Model Management**: Pre-configure the Ollama container to pull specific models at startup.
- **Scalability**: Configure the number of replicas or leverage Horizontal Pod Autoscaler (HPA) to scale on CPU or memory usage.
- **Storage and Persistence**: Enable persistent storage to save models beyond the lifecycle of individual pods (NOTE: You'll need a supported Storage Class).

## Overrides

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| fullnameOverride | string | `""` | String to fully override `"ollama"` |
| nameOverride | string | `"ollama"` | Provide a name in place of `ollama` |

## Ollama parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| ollama.gpu.enabled | bool | `false` | Enable GPU integration |
| ollama.gpu.num | int | `1` | Specify the number of GPU |
| ollama.models | list | `["gemma:2b"]` | Models to pull at container startup |

## Image parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| image.imagePullPolicy | string | `"IfNotPresent"` | Image pull policy for Ollama |
| image.repository | string | `"ollama/ollama"` | Repository to use for Ollama |
| image.tag | string | `""` (defaults to Chart.AppVersion) | Tag to use for Ollama |
| imagePullSecrets | list | `[]` | Secrets with credentials to pull images from a private registry |

## General parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Assign custom [affinity] rules to the deployment |
| autoscaling.behavior | object | `{}` | Configures the scaling behavior of the target in both Up and Down directions. |
| autoscaling.enabled | bool | `false` | Enable Horizontal Pod Autoscaler ([HPA]) for Ollama |
| autoscaling.maxReplicas | int | `5` | Maximum number of replicas for Ollama [HPA] |
| autoscaling.metrics | list | `[]` | Configures custom HPA metrics for Ollama Ref: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/ |
| autoscaling.minReplicas | int | `1` | Minimum number of replicas for Ollama [HPA] |
| autoscaling.targetCPUUtilizationPercentage | int | `50` | Average CPU utilization percentage for Ollama [HPA] |
| autoscaling.targetMemoryUtilizationPercentage | int | `50` | Average memory utilization percentage for Ollama [HPA] |
| containerPorts.http | int | `11434` | ollama container port |
| containerSecurityContext | object | See [values.yaml] | Ollama container-level security context |
| deploymentAnnotations | object | `{}` | Annotations to be added to ollama Deployment |
| deploymentStrategy | object | `{}` | Deployment strategy to be added to the ollama Deployment |
| dnsConfig | object | `{}` | [DNS configuration] |
| dnsPolicy | string | `"ClusterFirst"` | Alternative DNS policy for Ollama pods |
| env | list | `[]` | Environment variables to pass to Ollama |
| envFrom | list | `[]` (See [values.yaml]) | envFrom to pass to Ollama |
| extraArgs | list | `[]` | Additional command line arguments to pass to Ollama |
| hostAliases | list | `[]` | Mapping between IP and hostnames that will be injected as entries in the pod's hosts files |
| hostNetwork | bool | `false` | Host Network for Ollama pods |
| lifecycle | object | `[]` (See deployment.yaml) | Specify postStart and preStop lifecycle hooks for your ollama container Specifying this will disable pulling models on container start-up |
| livenessProbe.failureThreshold | int | `3` | Minimum consecutive failures for the [probe] to be considered failed after having succeeded |
| livenessProbe.initialDelaySeconds | int | `10` | Number of seconds after the container has started before [probe] is initiated |
| livenessProbe.periodSeconds | int | `10` | How often (in seconds) to perform the [probe] |
| livenessProbe.successThreshold | int | `1` | Minimum consecutive successes for the [probe] to be considered successful after having failed |
| livenessProbe.timeoutSeconds | int | `1` | Number of seconds after which the [probe] times out |
| nodeSelector | object | `{}` | [Node selector] |
| pdb.annotations | object | `{}` | Annotations to be added to Ollama pdb |
| pdb.enabled | bool | `false` | Deploy a [PodDisruptionBudget] for Ollama |
| pdb.labels | object | `{}` | Labels to be added to Ollama pdb |
| pdb.maxUnavailable | string | `""` | Number of pods that are unavailable after eviction as number or percentage (eg.: 50%). |
| pdb.minAvailable | string | `""` (defaults to 0 if not specified) | Number of pods that are available after eviction as number or percentage (eg.: 50%) |
| podAnnotations | object | `{}` | Annotations to be added to ollama pods |
| podLabels | object | `{}` | Labels to be added to ollama pods |
| priorityClassName | string | `""` | Priority class for Ollama pods |
| readinessProbe.failureThreshold | int | `3` | Minimum consecutive failures for the [probe] to be considered failed after having succeeded |
| readinessProbe.initialDelaySeconds | int | `10` | Number of seconds after the container has started before [probe] is initiated |
| readinessProbe.periodSeconds | int | `10` | How often (in seconds) to perform the [probe] |
| readinessProbe.successThreshold | int | `1` | Minimum consecutive successes for the [probe] to be considered successful after having failed |
| readinessProbe.timeoutSeconds | int | `1` | Number of seconds after which the [probe] times out |
| replicas | int | `1` | The number of ollama pods to run |
| resources | object | `{}` | Resource limits and requests for Ollama |
| revisionHistoryLimit | int | `3` | Number of old deployment ReplicaSets to retain. The rest will be garbage collected. |
| securityContext | object | `{}` (See [values.yaml]) | Toggle and define pod-level security context. |
| terminationGracePeriodSeconds | int | `30` | terminationGracePeriodSeconds for container lifecycle hook |
| tolerations | list | `[]` | [Tolerations] for use with node taints |
| topologySpreadConstraints | list | `[]` | Assign custom [TopologySpreadConstraints] rules to Ollama # Ref: https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/ # If labelSelector is left out, it will default to the labelSelector configuration of the deployment |
| volumeMounts | list | `[]` | Additional volumeMounts to the ollama container |
| volumes | list | `[]` | Additional volumes to the ollama pod |

## Service parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| service.annotations | object | `{}` | ollama service annotations |
| service.externalIPs | list | `[]` | ollama service external IPs |
| service.externalTrafficPolicy | string | `""` | Denotes if this Service desires to route external traffic to node-local or cluster-wide endpoints |
| service.labels | object | `{}` | ollama service labels |
| service.loadBalancerIP | string | `""` | LoadBalancer will get created with the IP specified in this field |
| service.loadBalancerSourceRanges | list | `[]` | Source IP ranges to allow access to service from |
| service.nodePortHttp | int | `30080` | ollama service http port for NodePort service type (only if `ollama.service.type` is set to "NodePort") |
| service.servicePortHttp | int | `80` | ollama service http port |
| service.servicePortHttpName | string | `"http"` | ollama service http port name, can be used to route traffic via istio |
| service.sessionAffinity | string | `""` | Used to maintain session affinity. Supports `ClientIP` and `None` |
| service.type | string | `"ClusterIP"` | ollama service type |
| serviceAccount.annotations | object | `{}` | Annotations applied to created service account |
| serviceAccount.automountServiceAccountToken | bool | `true` | Automount API credentials for the Service Account |
| serviceAccount.create | bool | `true` | Create ollama service account |
| serviceAccount.labels | object | `{}` | Labels applied to created service account |
| serviceAccount.name | string | `"ollama"` | ollama service account name |

## Persistence parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| persistence.accessModes | list | `["ReadWriteOnce"]` | Persistent Volume Access Modes |
| persistence.annotations | object | `{}` | Persistent Volume Claim annotations |
| persistence.dataSource | object | `{}` | Custom PVC data source |
| persistence.enabled | bool | `true` | Create a PVC for Ollama (empty-dir will be used if not specified) |
| persistence.existingClaim | string | `""` | The name of an existing PVC to use for persistence |
| persistence.selector | object | `{}` | Selector to match an existing Persistent Volume for Ollama data PVC # If set, the PVC can't have a PV dynamically provisioned for it |
| persistence.size | string | `"30Gi"` | Size of data volume |
| persistence.storageClass | string | `""` | Storage class of backing PVC |

## Service account parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| serviceAccount.annotations | object | `{}` | Annotations applied to created service account |
| serviceAccount.automountServiceAccountToken | bool | `true` | Automount API credentials for the Service Account |
| serviceAccount.create | bool | `true` | Create ollama service account |
| serviceAccount.labels | object | `{}` | Labels applied to created service account |
| serviceAccount.name | string | `"ollama"` | ollama service account name |

## Ingress parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| ingress.annotations | object | `{}` | Additional ingress annotations |
| ingress.enabled | bool | `false` | Enable an ingress resource for Ollama |
| ingress.extraHosts | list | `[]` (See [values.yaml]) | The list of additional hostnames to be covered by ingress record |
| ingress.extraPaths | list | `[]` (See [values.yaml]) | Additional ingress paths |
| ingress.extraRules | list | `[]` (See [values.yaml]) | Additional ingress rules |
| ingress.extraTls | list | `[]` (See [values.yaml]) | Additional TLS configuration |
| ingress.hostname | string | `""` | Ollama hostname |
| ingress.ingressClassName | string | `""` | Defines which ingress controller will implement the resource |
| ingress.labels | object | `{}` | Additional ingress labels |
| ingress.path | string | `"/"` | The path to Ollama |
| ingress.pathType | string | `"Prefix"` | Ingress path type. One of `Exact`, `Prefix` or `ImplementationSpecific` |
| ingress.tls.enabled | bool | `false` | Enable TLS configuration for the hostname defined at `ingress.hostname` |
| ingress.tls.selfSigned | bool | `false` | Create a Self Signed TLS Certificate for Ollama |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs](https://github.com/norwoodj/helm-docs)
