# Helm chart to onboard a set of authorization-based isolated namespaces to Knative when using Istio

## Usage

### Prerequisite

- [Follow setup OpenShift Serverless with Service Mesh](https://openshift-knative.github.io/docs/docs/latest/serverless-eventing/service-mesh/eventing-service-mesh-setup.html)

### Setup Authorization policies for Knative system components

1. Deny all traffic by default
   ```shell
   // TODO RETO: FIX THOSE
   kubectl apply -f setup/deny-all-by-default.yaml
   ```

2. Allow Knative Serving components to send requests to the Activator
   ```shell
   kubectl apply -f setup/allow-traffic-to-activator.yaml
   ```

3. Allow Knative Kafka controller to probe Knative Kafka data plane for resource readiness

   ```shell
   kubectl apply -f setup/allow-probe-kafka-controller.yaml
   ```
   
4. Allow Brokers with class `MTChannelBasedBroker` to communicate with the underlying channels:

   ```shell
   kubectl apply -f setup/allow-mt-channel-based-broker-to-channels.yaml
   ```

### Onboarding a set of namespaces

Imaging we want to onboard a new project `order-service` to Knative composed of two namespaces: `ns1` and `ns2`.

1. Create the project's namespaces by running the following commands:
   ```shell
   kubectl create ns ns1
   kubectl create ns ns2
   ```

2. Install the Helm chart
   ```shell
   helm install oci://quay.io/openshift-knative/knative-istio-authz-onboarding --version 1.31.0 --set "name=order-service" --set "namespaces={ns1, ns2}"
   ```
   or, view the resources you would need to onboard the project:
   ```shell
   helm template oci://quay.io/openshift-knative/knative-istio-authz-onboarding --version 1.31.0 --set "name=order-service" --set "namespaces={ns1, ns2}"
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

Push the chart by running the following command:

```shell
helm push knative-istio-authz-onboarding-0.1.0.tgz oci://quay.io/pierdipi
```
