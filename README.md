<!-- Warning: Do not manually edit this file. See notes on gluon + helm-docs at the end of this file for more information. -->
# mattermost

![Version: 11.3.0-bb.3](https://img.shields.io/badge/Version-11.3.0--bb.3-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 11.3.0](https://img.shields.io/badge/AppVersion-11.3.0-informational?style=flat-square) ![Maintenance Track: bb_integrated](https://img.shields.io/badge/Maintenance_Track-bb_integrated-green?style=flat-square)

Deployment of mattermost

## Upstream Release Notes

This package has no upstream release note links on file. Please add some to [chart/Chart.yaml](chart/Chart.yaml) under `annotations.bigbang.dev/upstreamReleaseNotesMarkdown`.
Example:
```yaml
annotations:
  bigbang.dev/upstreamReleaseNotesMarkdown: |
    - [Find our upstream chart's CHANGELOG here](https://link-goes-here/CHANGELOG.md)
    - [and our upstream application release notes here](https://another-link-here/RELEASE_NOTES.md)
```

## Learn More

- [Application Overview](docs/overview.md)
- [Other Documentation](docs/)

## Pre-Requisites

- Kubernetes Cluster deployed
- Kubernetes config installed in `~/.kube/config`
- Helm installed

Kubernetes: `>=1.12.0-0`

Install Helm

https://helm.sh/docs/intro/install/

## Deployment

- Clone down the repository
- cd into directory

```bash
helm install mattermost chart/
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| domain | string | `"bigbang.dev"` |  |
| istio.enabled | bool | `false` | Toggle istio integration |
| istio.injection | string | `"disabled"` | Istio sidecar injection mode (enabled, disabled, or empty for no label) |
| istio.mtls | object | `{"mode":"STRICT"}` | Mutual TLS configuration |
| istio.mtls.mode | string | `"STRICT"` | STRICT = Allow only mutual TLS traffic, PERMISSIVE = Allow both plain text and mutual TLS traffic |
| istio.sidecar | object | `{"enabled":true,"outboundTrafficPolicyMode":"REGISTRY_ONLY"}` | Sidecar configuration for Istio |
| istio.sidecar.enabled | bool | `true` | Enable/disable Istio Sidecar resource (restricts outbound traffic) |
| istio.sidecar.outboundTrafficPolicyMode | string | `"REGISTRY_ONLY"` | Outbound traffic policy mode (REGISTRY_ONLY or ALLOW_ANY) |
| istio.serviceEntries | object | `{"custom":[{"enabled":true,"name":"mattermost-external","spec":{"exportTo":["."],"hosts":["securityupdatecheck.mattermost.com","customers.mattermost.com","notices.mattermost.com","api.integrations.mattermost.com","pdat.matterlytics.com","api.github.com"],"location":"MESH_EXTERNAL","ports":[{"name":"https","number":443,"protocol":"TLS"}]}}]}` | Service Entries Configuration |
| istio.serviceEntries.custom | list | `[{"enabled":true,"name":"mattermost-external","spec":{"exportTo":["."],"hosts":["securityupdatecheck.mattermost.com","customers.mattermost.com","notices.mattermost.com","api.integrations.mattermost.com","pdat.matterlytics.com","api.github.com"],"location":"MESH_EXTERNAL","ports":[{"name":"https","number":443,"protocol":"TLS"}]}}]` | List of custom Istio ServiceEntry resources |
| istio.serviceEntries.custom[0] | object | `{"enabled":true,"name":"mattermost-external","spec":{"exportTo":["."],"hosts":["securityupdatecheck.mattermost.com","customers.mattermost.com","notices.mattermost.com","api.integrations.mattermost.com","pdat.matterlytics.com","api.github.com"],"location":"MESH_EXTERNAL","ports":[{"name":"https","number":443,"protocol":"TLS"}]}}` | Mattermost external services (update checks, notices, integrations, analytics) |
| istio.authorizationPolicies | object | `{"additionalPolicies":{"minio-operator-policy":{"enabled":true,"spec":{"action":"ALLOW","rules":[{"from":[{"source":{"namespaces":["minio-operator"],"principals":["cluster.local/ns/minio-operator/sa/minio-operator"]}}]}],"selector":{"matchLabels":{"app":"minio"}}}}},"custom":[],"enabled":true,"generateFromNetpol":true}` | Authorization Policies Configuration |
| istio.authorizationPolicies.enabled | bool | `true` | Enable/disable the generation of Istio AuthorizationPolicies |
| istio.authorizationPolicies.generateFromNetpol | bool | `true` | Generate AuthorizationPolicies from NetworkPolicy configurations |
| istio.authorizationPolicies.custom | list | `[]` | Custom authorization policies - additional policies added via additionalPolicies |
| istio.authorizationPolicies.additionalPolicies | object | `{"minio-operator-policy":{"enabled":true,"spec":{"action":"ALLOW","rules":[{"from":[{"source":{"namespaces":["minio-operator"],"principals":["cluster.local/ns/minio-operator/sa/minio-operator"]}}]}],"selector":{"matchLabels":{"app":"minio"}}}}}` | Additional authorization policies (map format) |
| istio.authorizationPolicies.additionalPolicies.minio-operator-policy | object | `{"enabled":true,"spec":{"action":"ALLOW","rules":[{"from":[{"source":{"namespaces":["minio-operator"],"principals":["cluster.local/ns/minio-operator/sa/minio-operator"]}}]}],"selector":{"matchLabels":{"app":"minio"}}}}` | Allow minio-operator to manage minio tenant |
| istio.hardened | object | `{"clusterAuditor":{"enabled":false,"namespace":"cluster-auditor"},"customAuthorizationPolicies":[],"customServiceEntries":[],"enabled":false,"kyvernoReporter":{"enabled":false,"namespace":"kyverno-reporter"},"minioOperator":{"enabled":true,"namespaces":["minio-operator"],"principals":["cluster.local/ns/minio-operator/sa/minio-operator"]},"monitoring":{"enabled":true,"namespaces":["monitoring"],"principals":["cluster.local/ns/monitoring/sa/monitoring-grafana","cluster.local/ns/monitoring/sa/monitoring-monitoring-kube-alertmanager","cluster.local/ns/monitoring/sa/monitoring-monitoring-kube-operator","cluster.local/ns/monitoring/sa/monitoring-monitoring-kube-prometheus","cluster.local/ns/monitoring/sa/monitoring-monitoring-kube-state-metrics","cluster.local/ns/monitoring/sa/monitoring-monitoring-prometheus-node-exporter"]},"outboundTrafficPolicyMode":"REGISTRY_ONLY"}` | Legacy configuration for backwards compatibility |
| routes | object | `{"inbound":{"chat":{"enabled":true,"gateways":["istio-gateway/public-ingressgateway"],"hosts":["chat.{{ .Values.domain }}"],"port":8065,"selector":{"app":"mattermost"},"service":"{{ .Release.Name }}"}}}` | Routes configuration for bb-common |
| routes.inbound | object | `{"chat":{"enabled":true,"gateways":["istio-gateway/public-ingressgateway"],"hosts":["chat.{{ .Values.domain }}"],"port":8065,"selector":{"app":"mattermost"},"service":"{{ .Release.Name }}"}}` | Inbound routes (creates VirtualService, ServiceEntry, NetworkPolicy, AuthorizationPolicy) |
| ingress | object | `{"annotations":{},"enabled":false,"host":"","tlsSecret":""}` | Specification to configure an Ingress with Mattermost |
| monitoring.enabled | bool | `false` |  |
| monitoring.namespace | string | `"monitoring"` |  |
| monitoring.serviceMonitor.scheme | string | `"http"` |  |
| monitoring.serviceMonitor.tlsConfig | object | `{}` |  |
| networkPolicies.enabled | bool | `false` |  |
| networkPolicies.controlPlaneCidr | string | `"0.0.0.0/0"` | CIDR range for control plane (used for kubeAPI egress) |
| networkPolicies.ingress.to.mattermost:8067 | object | `{"enabled":"{{ .Values.monitoring.enabled }}","from":{"k8s":{"monitoring/prometheus":true}},"podSelector":{"matchLabels":{"app":"mattermost"}}}` | Mattermost metrics ingress from monitoring |
| networkPolicies.ingress.to.minio:9000 | object | `{"enabled":"{{ .Values.minio.install }}","from":{"k8s":{"minioOperator/*":true}},"podSelector":{"matchLabels":{"app":"minio"}}}` | Minio ingress from minio-operator |
| networkPolicies.ingress.to.minio-metrics | object | `{"enabled":"{{ and .Values.minio.install .Values.monitoring.enabled .Values.minio.upstream.tenant.metrics.enabled }}","from":{"k8s":{"monitoring/*":true}},"podSelector":{"matchLabels":{"app":"minio","v1.min.io/tenant":"mattermost-minio"}}}` | Minio metrics ingress from monitoring |
| networkPolicies.egress.from.mattermost | object | `{"podSelector":{"matchLabels":{"app":"mattermost"}},"to":{"cidr":{"0.0.0.0/0":true}}}` | Mattermost app egress to anywhere (for external integrations, updates, etc.) |
| networkPolicies.egress.from.mattermost-elasticsearch | object | `{"enabled":"{{ .Values.elasticsearch.enabled }}","podSelector":{"matchLabels":{"app":"mattermost"}},"to":{"k8s":{"logging/elasticsearch:9200":{"podSelector":{"matchLabels":{"common.k8s.elastic.co/type":"elasticsearch"}}}}}}` | Mattermost to Elasticsearch |
| networkPolicies.egress.from.wait-job | object | `{"enabled":"{{ .Values.waitJob.enabled }}","podSelector":{"matchLabels":{"job-name":"mattermost-wait-job"}},"to":{"definition":{"kubeAPI":true}}}` | Wait job egress to kubeAPI |
| networkPolicies.egress.from.minio-operator | object | `{"enabled":"{{ .Values.minio.install }}","podSelector":{"matchLabels":{"app":"minio"}},"to":{"k8s":{"minioOperator/*:4222":true}}}` | Minio egress to minio-operator |
| networkPolicies.egress.from.minio | object | `{"enabled":"{{ .Values.minio.install }}","podSelector":{"matchLabels":{"app":"minio"}},"to":{"cidr":{"0.0.0.0/0":true}}}` | Minio egress to anywhere (for external S3-compatible storage) |
| networkPolicies.egress.from.update-check | object | `{"podSelector":{"matchLabels":{"app":"mattermost-update-check"}},"to":{"cidr":{"0.0.0.0/0":true}}}` | Update check job egress |
| networkPolicies.egress.from.test | object | `{"enabled":"{{ .Values.bbtests.enabled }}","podSelector":{"matchLabels":{"helm-test":"enabled"}},"to":{"cidr":{"{{ .Values.networkPolicies.controlPlaneCidr }}":true}}}` | Test egress (for cypress tests) |
| networkPolicies.egress.from.tempo | object | `{"enabled":"{{ eq .Values.istio.injection \"enabled\" }}","to":{"k8s":{"tempo/tempo:9411":true}}}` | Tempo egress (when istio injection is enabled) |
| networkPolicies.additionalPolicies | list | `[]` |  |
| sso.enabled | bool | `false` |  |
| sso.client_id | string | `"platform1_a8604cc9-f5e9-4656-802d-d05624370245_bb8-mattermost"` |  |
| sso.client_secret | string | `"nothing"` |  |
| sso.auth_endpoint | string | `"https://login.dso.mil/auth/realms/baby-yoda/protocol/openid-connect/auth"` |  |
| sso.token_endpoint | string | `"https://login.dso.mil/auth/realms/baby-yoda/protocol/openid-connect/token"` |  |
| sso.user_api_endpoint | string | `"https://login.dso.mil/auth/realms/baby-yoda/protocol/openid-connect/userinfo"` |  |
| sso.enable_sign_up_with_email | bool | `false` |  |
| sso.enable_sign_in_with_email | bool | `false` |  |
| sso.enable_sign_in_with_username | bool | `false` |  |
| image.name | string | `"registry1.dso.mil/ironbank/opensource/mattermost/mattermost"` |  |
| image.tag | string | `"11.3.0"` |  |
| image.imagePullPolicy | string | `"IfNotPresent"` |  |
| global.imagePullSecrets[0].name | string | `"private-registry"` |  |
| replicaCount | int | `1` |  |
| users | string | `nil` |  |
| enterprise.enabled | bool | `false` |  |
| enterprise.license | string | `""` |  |
| nameOverride | string | `""` |  |
| updateJob.disabled | bool | `true` | Must be disabled when Istio injected |
| updateJob.labels | object | `{}` |  |
| updateJob.annotations | object | `{}` |  |
| resources.limits.cpu | int | `2` |  |
| resources.limits.memory | string | `"4Gi"` |  |
| resources.requests.cpu | int | `2` |  |
| resources.requests.memory | string | `"4Gi"` |  |
| affinity | object | `{}` |  |
| nodeSelector | object | `{}` |  |
| tolerations | object | `{}` |  |
| mattermostEnvs | object | `{}` |  |
| existingSecretEnvs | object | `{}` |  |
| volumes | object | `{}` |  |
| volumeMounts | object | `{}` |  |
| podLabels | object | `{}` | Pod labels for Mattermost server pods |
| podAnnotations | object | `{}` | Pod annotations for Mattermost server pods |
| securityContext | object | `{"runAsGroup":2000,"runAsNonRoot":true,"runAsUser":2000}` | securityContext for Mattermost server pods |
| containerSecurityContext | object | `{"capabilities":{"drop":["ALL"]},"runAsGroup":2000,"runAsNonRoot":true,"runAsUser":2000}` | containerSecurityContext for Mattermost server containers |
| minio.install | bool | `false` |  |
| minio.bucketCreationImage | string | `"registry1.dso.mil/ironbank/opensource/minio/mc:RELEASE.2025-08-13T08-35-41Z"` |  |
| minio.service.nameOverride | string | `"minio.mattermost.svc.cluster.local"` |  |
| minio.upstream.tenant.name | string | `"mattermost-minio"` |  |
| minio.upstream.tenant.pools[0].name | string | `"pool-0"` |  |
| minio.upstream.tenant.pools[0].labels.app | string | `"minio"` |  |
| minio.upstream.tenant.pools[0].labels."app.kubernetes.io/name" | string | `"minio"` |  |
| minio.upstream.tenant.configSecret.name | string | `"minio-creds-secret"` |  |
| minio.upstream.tenant.configSecret.accessKey | string | `"minio"` |  |
| minio.upstream.tenant.configSecret.secretKey | string | `"minio123"` |  |
| minio.upstream.tenant.metrics.enabled | bool | `false` |  |
| minio.upstream.tenant.metrics.port | int | `9000` |  |
| minio.upstream.tenant.buckets[0].name | string | `"mattermost"` |  |
| minio.waitJob.enabled | bool | `false` |  |
| postgresql.install | bool | `false` |  |
| postgresql.image.registry | string | `"registry1.dso.mil/ironbank"` |  |
| postgresql.image.repository | string | `"opensource/postgres/postgresql"` |  |
| postgresql.image.tag | string | `"17.6"` |  |
| postgresql.image.pullSecrets[0] | string | `"private-registry"` |  |
| postgresql.auth.username | string | `"mattermost"` |  |
| postgresql.auth.password | string | `"bigbang"` |  |
| postgresql.auth.database | string | `"mattermost"` |  |
| postgresql.fullnameOverride | string | `"mattermost-postgresql"` |  |
| postgresql.securityContext.fsGroup | int | `26` |  |
| postgresql.containerSecurityContext.runAsUser | int | `26` |  |
| postgresql.containerSecurityContext.runAsNonRoot | bool | `true` |  |
| postgresql.containerSecurityContext.capabilities.drop[0] | string | `"ALL"` |  |
| postgresql.volumePermissions.enabled | bool | `false` |  |
| postgresql.volumePermissions.securityContext.capabilities.drop[0] | string | `"ALL"` |  |
| postgresql.postgresqlConfiguration.listen_addresses | string | `"*"` |  |
| postgresql.pgHbaConfiguration | string | `"local all all md5\nhost all all all md5"` |  |
| postgresql.connParams | string | `""` |  |
| postgresql.sslMode | string | `"disable"` |  |
| database.secret | string | `""` |  |
| database.readinessCheck.disableDefault | bool | `true` |  |
| database.readinessCheck.image | string | `"registry1.dso.mil/ironbank/opensource/postgres/postgresql:18.1"` |  |
| database.readinessCheck.command[0] | string | `"/bin/sh"` |  |
| database.readinessCheck.command[1] | string | `"-c"` |  |
| database.readinessCheck.command[2] | string | `"until pg_isready --dbname=\"$DB_CONNECTION_CHECK_URL\"; do echo waiting for database; sleep 5; done;"` |  |
| database.readinessCheck.env[0].name | string | `"DB_CONNECTION_CHECK_URL"` |  |
| database.readinessCheck.env[0].valueFrom.secretKeyRef.key | string | `"DB_CONNECTION_CHECK_URL"` |  |
| database.readinessCheck.env[0].valueFrom.secretKeyRef.name | string | `"{{ .Values.database.secret \| default (printf \"%s-dbcreds\" (include \"mattermost.fullname\" .)) }}"` |  |
| fileStore.secret | string | `""` |  |
| fileStore.url | string | `""` |  |
| fileStore.bucket | string | `""` |  |
| fileStore.roleARN | string | `""` |  |
| elasticsearch.enabled | bool | `false` |  |
| elasticsearch.connectionurl | string | `"https://logging-ek-es-http.logging.svc.cluster.local:9200"` |  |
| elasticsearch.username | string | `""` |  |
| elasticsearch.password | string | `""` |  |
| elasticsearch.enableindexing | bool | `true` |  |
| elasticsearch.indexprefix | string | `"mm-"` |  |
| elasticsearch.skiptlsverification | bool | `true` |  |
| elasticsearch.bulkindexingtimewindowseconds | int | `3600` |  |
| elasticsearch.sniff | bool | `false` |  |
| elasticsearch.enablesearching | bool | `true` |  |
| elasticsearch.enableautocomplete | bool | `true` |  |
| openshift | bool | `false` |  |
| resourcePatch | object | `{}` |  |
| bbtests.enabled | bool | `false` |  |
| bbtests.cypress.artifacts | bool | `true` |  |
| bbtests.cypress.envs.cypress_url | string | `"http://mattermost.mattermost.svc.cluster.local:8065"` |  |
| bbtests.cypress.envs.cypress_mm_email | string | `"test@bigbang.dev"` |  |
| bbtests.cypress.envs.cypress_mm_user | string | `"bigbang"` |  |
| bbtests.cypress.envs.cypress_mm_password | string | `"Bigbang#123"` |  |
| bbtests.cypress.envs.cypress_waittime | string | `"5000"` |  |
| bbtests.cypress.envs.cypress_tnr_username | string | `"cypress"` |  |
| bbtests.cypress.envs.cypress_tnr_password | string | `"tnr_w!G33ZyAt@C8"` |  |
| bbtests.cypress.resources.requests.cpu | string | `"2"` |  |
| bbtests.cypress.resources.requests.memory | string | `"1500M"` |  |
| bbtests.cypress.resources.limits.cpu | string | `"2"` |  |
| bbtests.cypress.resources.limits.memory | string | `"1500M"` |  |
| waitJob.enabled | bool | `true` |  |
| waitJob.permissions.apiGroups[0] | string | `"installation.mattermost.com"` |  |
| waitJob.permissions.resources[0] | string | `"mattermosts"` |  |

## Contributing

Please see the [contributing guide](./CONTRIBUTING.md) if you are interested in contributing.

---

_This file is programatically generated using `helm-docs` and some BigBang-specific templates. The `gluon` repository has [instructions for regenerating package READMEs](https://repo1.dso.mil/big-bang/product/packages/gluon/-/blob/master/docs/bb-package-readme.md)._

