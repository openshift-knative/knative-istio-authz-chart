# Helm chart to onboard a set of authorization-based isolated namespaces to Knative when using Istio

## Usage

### Prerequisite

- [Follow setup OpenShift Serverless with Service Mesh](https://openshift-knative.github.io/docs/docs/latest/serverless-common/service-mesh/common-service-mesh-setup.html).
- [Follow use Service Mesh to isolate network traffic with OpenShift Serverless](https://openshift-knative.github.io/docs/docs/latest/serverless-common/service-mesh/common-service-mesh-network-isolation.html).

### Onboarding a set of namespaces

Imaging we want to onboard a new tenant named `tenant-1` to Knative composed of two namespaces: `ns1` and `ns2`.

1. Create the project's namespaces by running the following commands:
   ```shell
   kubectl create ns ns1
   kubectl create ns ns2
   ```

2. Install the Helm chart
   ```shell
   helm install oci://quay.io/openshift-knative/redhat-knative-istio-authz --version 1.31.0 --set "name=tenant-1" --set "namespaces={ns1, ns2}"
   ```
   or, view the resources you would need to onboard the project:
   ```shell
   helm template oci://quay.io/openshift-knative/redhat-knative-istio-authz --version 1.31.0 --set "name=tenant-1" --set "namespaces={ns1, ns2}"
   ```

## Development

Render the templates by running the following command:

```shell
helm template ./ --values tests/values.yaml
```

Package the chart by running the following command:

```shell
helm package ./
```

Push the chart to your own account by running the following command:

```shell
helm push redhat-knative-istio-authz-0.1.0.tgz oci://quay.io/<your-username>
```

The chart is automatically pushed to quay.io/openshift-knative on  `main` and `release-*` branches using CI.
