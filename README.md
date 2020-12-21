# `gke-istio-ingress-L7-xlb`

This repo provide a sample implementation for using the GCP L7 Global load balancer for ingress into an `istio-ingressgateway`
## Prerequisites

 - Istio Installed on your GKE Cluster
 - `istio-ingressgateway` is configured as apart of the installation. 

## Getting Started

Apply the resources in`gke-istio-ingress.yaml` to the cluster `kubectl apply -f gke-istio-ingress.yaml`

There are several resources contained in this file:
 - Istio related resources:
	 - The `health` Virtual Service that allows the cluster to respond to [load-balancing health check](https://cloud.google.com/kubernetes-engine/docs/concepts/ingress#health_checks) requests by forwarding the requests to the status endpoint on the Istio ingress gateway. We are specfically forwarding all requests that contain the `User-Agent: Google HC` to the `/healthz/ready` endpoint.
	 -The  `istio-ingressgateway` Service is modified to include two annotations, these are used to enable container-native load balancing for the service and then also specific which backend service we want to use for port `80`:
		 - `cloud.google.com/neg: '{"ingress": true}'`
		 - `cloud.google.com/backend-config: '{"ports": {"80":"backend-config"}}'`
	- The `istio-ingressgateway` Gateway resource is just used to ensure the gateway is configured to allow any host (`*`) on port `80`.
- The `istio-ingress` this creates an [Ingress object for the External HTTP(S) Load Balancer](https://cloud.google.com/kubernetes-engine/docs/concepts/ingress-xlb) resource within GCP.
- The `backend-config` this creates a BackendConfig resource that allows us in this example to configure the [Backend service timeout](https://cloud.google.com/kubernetes-engine/docs/how-to/ingress-features#timeout) and [Connection draining timeout](https://cloud.google.com/kubernetes-engine/docs/how-to/ingress-features#draining_timeout) on the Load Balancer. 
