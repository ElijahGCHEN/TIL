# Introduction 

Cloud NAT provides network address translation for outbound traffic to the internet. 

# Stateful connection

firewalls and other network assets that allow ingress traffic also allow the corresponding egress traffic (responses from the server). This is because most firewalls are stateful.

**Stateful Inspection:** Stateful firewalls keep track of the connections that pass through them. When a request (ingress) is allowed, the firewall remembers this and automatically allows the related response (egress) to pass back through.   

**Connection Tracking:** The firewall maintains information about the connection, such as source/destination IP addresses and ports. This allows it to identify the response as part of the initial request and allow it through without further checks.   

# Cloud NAT v.s. load balancer

While Cloud NAT enables instances without public IP addresses to access the internet, it's not designed to function as a proxy for incoming traffic to your web application server. Here's why:   

**Outbound Connections Only:** Cloud NAT primarily focuses on facilitating outbound connections from your private instances to the internet. It translates their private IP addresses to public IP addresses, allowing them to initiate requests and receive responses.

**No Incoming Connection Handling:** Cloud NAT doesn't have the capability to receive incoming connections from the internet and forward them to your internal web server. It lacks features like load balancing, SSL termination, and request routing that are essential for web applications.   
