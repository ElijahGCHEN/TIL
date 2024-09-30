# what is istio
istio is a service mesh that layers transparently onto existing distributed applications and provides the following benefits:
- load balance
- service-to-service authentication
- monitering, configuring

# How it works
It provides a **control plane** and a **data plane**.
- data plane: the part of the mesh that directly handles and routes traffic between workload instances. In sidecar mode, istio's data plane uses Envoy proxies deployed as sidecars to mediate and control all traffic that your mesh services send and receive.
- control plane: a set of system services that configures the mesh or a subset of the mesh to manage the communication between the workload instances within.
