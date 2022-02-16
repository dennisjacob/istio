
#### Distributed tracing
- Monitoring the microservices apps
- Envoy generates a unique reuquestID and tracing information, and stores it as part of HTTP header when request enter the service mesh
- Distributed trace is a collection of spans
  - span 
    - As request flow through different system, each component contributes a span
    - Span has a name, start/finish timestamp, set of key-value pairs called tags, logs and a span context
