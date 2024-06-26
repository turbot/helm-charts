# Guardrails Agent for Kubernetes

This Guardrails agent is used to send Kubernetes cluster details to a [Turbot Guardrails](https://turbot.com/guardrails) workspace using osquery.

Please see [Guardrails Kubernetes Integration](https://turbot.com/guardrails/docs/integrations/kubernetes) for more information.

## Get Repository Info

```sh
helm repo add turbot https://helm.turbot.com
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
kubectl logs -f sts/guardrails-agent-kubernetes --namespace guardrails
```

## Upgrading the Chart

To upgrade a release to a new chart version:

```sh
helm repo update
helm upgrade guardrails-agent-kubernetes turbot/guardrails-agent-kubernetes --namespace guardrails --reuse-values
```

To update the values, pass in the new values with the `-f` flag:

```sh
helm upgrade guardrails-agent-kubernetes turbot/guardrails-agent-kubernetes --namespace guardrails --reuse-values -f guardrails-values.yaml
```

Only values included in `guardrails-values.yaml` will be updated, with priority being given to new values.

## Uninstalling the Chart

To uninstall the `guardrails-agent-kubernetes` deployment:

```sh
helm delete guardrails-agent-kubernetes --namespace guardrails
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Pod affinity |
| containerSecurityContext | object | `{ readOnlyRootFilesystem: true, runAsNonRoot: true, runAsUser: 2000, allowPrivilegeEscalation: false }` | Security settings for the agent container running in the pod |
| extraEnv | list | `[]` | Extra environment variables to pass to the agent |
| fullnameOverride | string | `""` | Override the full name for resources |
| guardrails.hostname | string | `""` | Hostname to a Guardrails server |
| image.pullPolicy | string | `IfNotPresent` | Image pull policy |
| image.repository | string | `ghcr.io/turbot/guardrails-agent-kubernetes` | Image repository |
| image.tag | string | `""` | Overrides the image tag whose default is the chart appVersion |
| nameOverride | string | `""` | Override the default name for resources |
| namespaceOverride | string | `""` | Override the namespace for deployed resources |
| nodeSelector | object | `{}` | Node labels for pod assignment |
| persistence.accessModes | list | `[ReadWriteOnce]` | Access modes for the persistent volume |
| persistence.annotations | object | `{}` | Annotations for the persistent volume |
| persistence.capacity | string | `"10Gi"` | Capacity of the persistent volume |
| persistence.enabled | bool | `true` | Enable persistence on osquery.db using a PVC. If false, use emptyDir |
| persistence.selector | object | `{}` | Selector for the persistent volume |
| persistence.storageClassName | string | `` | Storage class for the persistent volume |
| persistence.volumeName | string | `""` | Name of the persistent volume |
| podAnnotations | object | `{}` | Annotations to be added to pods |
| podLabels | object | `{}` | Labels to be added to pods |
| podSecurityContext | object | `{}` | Security settings for the the pod |
| readinessProbe.httpGet.path | string | `/` | HTTP GET path for readiness probe |
| readinessProbe.httpGet.port | string | `http` | HTTP GET port for readiness probe |
| resources | object | `{}` | CPU/memory resource requests/limits |
| role.annotations | object | `{}` | Annotations to add to the role |
| roleBinding.annotations | object | `{}` | Annotations to add to the role binding |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.automount | bool | `true` | Whether to automatically mount the service account's API credentials |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `""` | The name of the service account to use |
| statefulSet.annotations | object | `{}` | Annotations to add to the stateful set |
| tolerations | list | `[]` | Tolerations for pod assignment |
| volumes | list | `[]` | Additional volumes for the StatefulSet |
| volumeMounts | list | `[]` | Mount paths for the additional volumes |
