# istio/Service Mesh -  Installation and various use cases

## Overview

### Why Istio/Service Mesh?

Microservices are quite complex
Debugging and tracing is very tough
Complex communication logic
Communication layer ( retries, timeout, controlling etc) normally a challenge for application team
How Service Mesh implemented?

Using Proxy Sidecars attached to the microservices
Service Mesh has a control plane that helps in controlling and shaping the traffic.
A service mesh is a secured and reliable way the services can communicate with observability capabilities.

### Applications of Service Mesh

Mutual TLS and automatic cert rotation
Identify performance and reliability issues using metrices
Visualize the metrices ( graffana )
Debug the services and tracing ( zipkin, jaeger )
Traffic routing and mirroring
Chaos testing
Circuit breakers


### What Istio does?
An opensource Service Mesh
Traffic Management
circuite breakers using timeout and retries
Tracing, Monitoring and Logging
Scurity ( Authnn, Authhz and encryption at proxy level )

### Components of Istio
Data Plane
Using Envoy Proxy ( developed in C++, and helps intercepts the traffic for the services)
Control Plane
Manage the Proxy ( using istiod, takes the YAML to envoy-readable configuration )
Service discovery ( using pilotd )
Certificate management
