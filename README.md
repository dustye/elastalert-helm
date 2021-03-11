
# Helm Chart for Kubernetes

An Elastalert chart is available in the Codesim LLC Helm repository, and can be installed into an existing Kubernetes cluster by following the instructions below.

## Installing the Chart

Add the Codesim repository to your Helm configuration:

```console
helm repo add codesim https://helm.codesim.com
```

Next, install the chart with a release name, such as _elastalert_:

```console
helm install elastalert codesim/elastalert
```

The command deploys Elastalert on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

See the comment in the default `values.yaml` for specifying a `writebackIndex` for ES 5.x.

If necessary, open Dev Tools on Kibana and send the below request to avoid errors like `RequestError: TransportError(400, u'search_phase_execution_exception', u'No mapping found for [alert_time] in order to sort on')`

```
PUT /elastalert/_mapping/elastalert
{
  "properties": {
      "alert_time": {"type": "date"}
  }
}
```

## Uninstalling the Chart

To uninstall/delete the elastalert deployment:

```console
helm delete elastalert --purge
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

| Parameter                                    | Description                                                                                                                   | Default                         |
|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| `image.repository`                           | docker image                                                                                                                  | jertel/elastalert-docker        |
| `image.tag`                                  | docker image tag                                                                                                              | 0.2.4                           |
| `image.pullPolicy`                           | image pull policy                                                                                                             | IfNotPresent                    |
| `image.pullSecrets`                          | image pull secrets                                                                                                            | []                              |
| `podAnnotations`                             | Annotations to be added to pods                                                                                               | {}                              |
| `command`                                    | command override for container                                                                                                | `NULL`                          |
| `args`                                       | args override for container                                                                                                   | `NULL`                          |
| `replicaCount`                               | number of replicas to run                                                                                                     | 1                               |
| `elasticsearch.host`                         | elasticsearch endpoint to use                                                                                                 | elasticsearch                   |
| `elasticsearch.port`                         | elasticsearch port to use                                                                                                     | 9200                            |
| `elasticsearch.useSsl`                       | whether or not to connect to es_host using SSL                                                                                | False                           |
| `elasticsearch.username`                     | Username for ES with basic auth                                                                                               | `NULL`                          |
| `elasticsearch.password`                     | Password for ES with basic auth                                                                                               | `NULL`                          |
| `elasticsearch.credentialsSecret`            | Specifies an existing secret to be used for the ES username/password auth                                                     | `NULL`                          |
| `elasticsearch.credentialsSecretUsernameKey` | The key in elasticsearch.credentialsSecret that stores the ES password auth                                                   | `NULL`                          |
| `elasticsearch.credentialsSecretPasswordKey` | The key in elasticsearch.credentialsSecret that stores the ES username auth                                                   | `NULL`                          |
| `elasticsearch.verifyCerts`                  | whether or not to verify TLS certificates                                                                                     | True                            |
| `elasticsearch.clientCert`                   | path to a PEM certificate to use as the client certificate                                                                    | /certs/client.pem               |
| `elasticsearch.clientKey`                    | path to a private key file to use as the client key                                                                           | /certs/client-key.pem           |
| `elasticsearch.caCerts`                      | path to a CA cert bundle to use to verify SSL connections                                                                     | /certs/ca.pem                   |
| `elasticsearch.certsVolumes`                 | certs volumes, required to mount ssl certificates when elasticsearch has tls enabled                                          | `NULL`                          |
| `elasticsearch.certsVolumeMounts`            | mount certs volumes, required to mount ssl certificates when elasticsearch has tls enabled                                    | `NULL`                          |
| `extraConfigOptions`                         | Additional options to propagate to all rules, cannot be `alert`, `type`, `name` or `index`                                    | `{}`                            |
| `secretConfigName`                         | name of the secret which holds the Elastalert config. **Note:** this will overwrite the generated config and must contain a full working config                                      | `NULL`                            |
| `secretRulesName`                            | name of the secret which holds the Elastalert rules. **Note:** this will overwrite the generated rules. Must contain all rules in separate data fields (see values.yaml for an example) or `secretRulesMountsAsDirectory` must be enabled                                   | `NULL`                            |
| `secretRulesList`                         | a list of rules to enable from the secret                                  | []                            |
| `secretRulesMountsAsDirectory`               | Mount all rules from the secret. **Note:** this will ignore the secretRulesList                                               | `false`                         |
| `optEnv`                                     | Additional pod environment variable definitions                                                                               | []                              |
| `extraVolumes`                               | Additional volume definitions                                                                                                 | []                              |
| `extraVolumeMounts`                          | Additional volumeMount definitions                                                                                            | []                              |
| `serviceAccount.create`                      | Specifies whether a service account should be created.                                                                        | `true`                          |
| `serviceAccount.name`                        | Service account to be used. If not set and `serviceAccount.create` is `true`, a name is generated using the fullname template |                                 |
| `serviceAccount.annotations`                 | ServiceAccount annotations                                                                                                    |                                 |
| `podSecurityPolicy.create`                   | Create pod security policy resources                                                                                          | `false`                         |
| `resources`                                  | Container resource requests and limits                                                                                        | {}                              |
| `rules`                                      | Rule and alert configuration for Elastalert                                                                                   | {} example shown in values.yaml |
| `runIntervalMins`                            | Default interval between alert checks, in minutes                                                                             | 1                               |
| `realertIntervalMins`                        | Time between alarms for same rule, in minutes                                                                                 | `NULL`                          |
| `alertRetryLimitMins`                        | Time to retry failed alert deliveries, in minutes                                                                             | 2880 (2 days)                   |
| `bufferTimeMins`                             | Default rule buffer time, in minutes                                                                                          | 15                              |
| `oldQueryLimitMins`                          | The maximum time between queries for ElastAlert to start at the most recently run query, in minutes                           | 10080 (1 week)                  |
| `writebackIndex`                             | Name or prefix of elastalert index(es)                                                                                        | elastalert                      |
| `nodeSelector`                               | Node selector for deployment                                                                                                  | {}                              |
| `affinity`                                   | Affinity specifications for the deployed pod(s)                                                                               | {}                              |
| `tolerations`                                | Tolerations for deployment                                                                                                    | []                              |
