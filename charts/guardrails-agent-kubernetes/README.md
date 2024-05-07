# Guardrails Agent for Kubernetes

This Guardrails agent is used to send Kubernetes cluster details to a [Turbot Guardrails](https://turbot.com/guardrails) workspace using osquery.

Please see [Guardrails Kubernetes Integration](https://turbot.com/guardrails/docs/integrations/kubernetes) for more information.

## Get Repository Info

```sh
helm repo add turbot https://turbot.github.io/helm-charts
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

Generate an osquery enrollment secret in your Guardrails workspace:

```graphql
{
  osquery(resource: "[FOLDER_RESOURCE_ID]" resourceTypeUri:"tmod:@turbot/kubernetes#/resource/types/cluster") {
    enrollSecret
  }
}
```

Create a namespace and secret:

```sh
ENROLL_SECRET=eyJh...
kubectl create namespace guardrails
kubectl create secret generic guardrails-agent-kubernetes-secret --from-literal enroll-secret=$ENROLL_SECRET --namespace guardrails
```

Set Guardrails values in `guardrails-values.yaml`:
```sh
HOSTNAME=myworkspace.turbot.com
echo "guardrails:
  hostname: $HOSTNAME" > guardrails-values.yaml
```

To install the chart with the release name `guardrails-agent-kubernetes`:

```sh
helm install guardrails-agent-kubernetes turbot/guardrails-agent-kubernetes --namespace guardrails -f guardrails-values.yaml
```

View the logs:

```sh
kubectl logs -f sts/guardrails-agent-kubernetes
```

## Uninstalling the Chart

To uninstall the `guardrails-agent-kubernetes` deployment:

```sh
helm delete guardrails-agent-kubernetes
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Pod affinity |
| autoscaling.enabled | bool | `false` | Enables autoscaling |
| autoscaling.maxReplicas | int | `100` | Maximum number of replicas |
| autoscaling.minReplicas | int | `1` | Minimum number of replicas |
| autoscaling.targetCPUUtilizationPercentage | int | `80` | Target CPU utilization percentage |
| extraEnv | list | `[]` | Extra environment variables to pass to the agent |
| fullnameOverride | string | `""` | Override the full name for resources |
| guardrails.tlsHostname | string | `""` | Hostname to a Guardrails server |
| image.pullPolicy | string | `IfNotPresent` | Image pull policy |
| image.repository | string | `ghcr.io/turbot/guardrails-agent-kubernetes` | Image repository |
| image.tag | string | `""` | Overrides the image tag whose default is the chart appVersion |
| imagePullSecrets | list | `[]` | Specify image pull secrets |
| livenessProbe.httpGet.path | string | `/` | HTTP GET path for liveness probe |
| livenessProbe.httpGet.port | string | `http` | HTTP GET port for liveness probe |
| nameOverride | string | `""` | Override the default name for resources |
| namespaceOverride | string | `""` | Override the namespace for deployed resources |
| nodeSelector | object | `{}` | Node labels for pod assignment |
| persistentVolume.accessModes | list | `[ReadWriteOnce]` | Access modes for the persistent volume |
| persistentVolume.annotations | object | `{}` | Annotations for the persistent volume |
| persistentVolume.capacity | string | `"10Gi"` | Capacity of the persistent volume |
| persistentVolume.create | bool | `true` | Specifies whether a persistent volume should be created |
| persistentVolume.reclaimPolicy | string | `Retain` | Reclaim policy for the persistent volume |
| persistentVolume.selector | object | `{}` | Selector for the persistent volume |
| persistentVolume.storageClassName | string | `standard` | Storage class for the persistent volume |
| persistentVolume.volumeName | string | `""` | Name of the persistent volume |
| podAnnotations | object | `{}` | Annotations to be added to pods |
| podLabels | object | `{}` | Labels to be added to pods |
| podSecurityContext.allowPrivilegeEscalation | bool | `false` | Whether to allow privilege escalation |
| podSecurityContext.readOnlyRootFilesystem | bool | `true` | Mount the root filesystem as read-only |
| podSecurityContext.runAsNonRoot | bool | `true` | Ensure that the container is run as a non-root user |
| podSecurityContext.runAsUser | int | `1000` | UID to run the entrypoint of the container process |
| readinessProbe.httpGet.path | string | `/` | HTTP GET path for readiness probe |
| readinessProbe.httpGet.port | string | `http` | HTTP GET port for readiness probe |
| replicaCount | int | `1` | Number of replicas to deploy |
| resources | object | `{}` | CPU/memory resource requests/limits |
| role.annotations | object | `{}` | Annotations to add to the role |
| roleBinding.annotations | object | `{}` | Annotations to add to the role binding |
| securityContext | object | `{}` | Security settings for the pod |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.automount | bool | `true` | Whether to automatically mount the service account's API credentials |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `""` | The name of the service account to use |
| statefulSet.annotations | object | `{}` | Annotations to add to the stateful set |
| tolerations | list | `[]` | Tolerations for pod assignment |
| volumes | list | `[]` | Additional volumes for the StatefulSet |
| volumeMounts | list | `[]` | Mount paths for the additional volumes |
