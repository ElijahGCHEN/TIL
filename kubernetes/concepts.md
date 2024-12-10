# pods

A pod is a group of one or more application containers that share the same volumn, IP and information about how to run them.

> Containers should only be scheduled together in a single pod if they're tightly coupled and need to share resources such as disk

# nodes

A node of Kubernetes is a working machine and may be a physical or virtual machine. Multiple nodes can run on one node.

# service

A Kubernetes Service is an abstraction layer which defines a logical set of pods and enable external traffic exposure, load balancing and service discovery for those pods.  
