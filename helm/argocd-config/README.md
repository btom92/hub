# argocd-config

> **IMPORTANT: Files in this folder are intended to be maintained by KNIME only. For customer overrides, see [values.yaml](../values.yaml).**

This folder contains the default ArgoCD Application configuration for all infrastructure and services related to KNIME Business Hub.

Configuration for each Helm chart that is deployed as an ArgoCD Application.
All chart configuration should be listed under the 'charts' key. User-defined (additional) charts are configured only via values under `additionalCharts` in [values.yaml](../values.yaml), not in this file.

## Configuring a helm chart

- **enabled (bool, optional)** - Determines whether the Helm chart is deployed. Useful for disabling charts.
- **targetRevision (string, recommended)** - The Helm chart version to deploy.
- **repoURL (string, optional)** - Overrides the Helm chart repository URL.
- **chartName (string, optional)** - Overrides the Helm chart name (defaults to the application name).
- **helmReleaseName (string, optional)** - Overrides the Helm release name.
- **syncWave (int, optional)** is used to control the order in which the Helm charts are deployed. Lower values are deployed earlier.
- **syncPolicy (object, optional)** is used to control the ArgoCD sync policy for the application.
- **namespace (string, optional)** is used to override the namespace that the Helm chart will be deployed to (defaults to the primary namespace configured above).
- **ignoreDifferences (nested mapping, optional)** is used to define any chart-specific ignoreDifferences (see https://argo-cd.readthedocs.io/en/stable/user-guide/diffing/).
- **suppressGlobalValues (bool, optional)** - When true, the chart's valuesObject does not include the KNIME global values; only the chart's overrides are passed. See [values.yaml](../values.yaml) CONFIGURABLE FIELDS.

## Example helm chart configuration

```yaml
charts:
  knime-hub-some-example-chart:
    enabled: true
    targetRevision: "2.7.5-stable"
    repoURL: https://registry.hub.knime.com/chartrepo/example/
    chartName: knime-hub-some-example-chart
    syncWave: 100
    syncPolicy:
      automated:
        enabled: true
        prune: true
        selfHeal: true
      syncOptions:
        - RespectIgnoreDifferences=true
      retry:
        limit: 5
        backoff:
          duration: 10s
          factor: 2
          maxDuration: 3m
    namespace: knime-hub
    ignoreDifferences:
      - group: apps
        kind: Deployment
        jsonPointers:
          - /spec/replicas
```
