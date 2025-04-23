# Kubernetes Scaling and Scheduling: Probes

Kubernetes probes are diagnostic tools that help determine the health and readiness of containers within pods. They're crucial for properly managing application lifecycles, enabling intelligent scheduling, and ensuring reliable service delivery.

## Types of Probes

Kubernetes provides three types of probes:

### 1. Liveness Probes

**Purpose**: Determine if a container is running properly. If the liveness probe fails, Kubernetes restarts the container.

**Use cases**:
- Detecting application deadlocks
- Identifying hung processes
- Recovering from states where the application is running but unresponsive

### 2. Readiness Probes

**Purpose**: Determine if a container is ready to receive traffic. If the readiness probe fails, the container's Pod IP is removed from Service endpoints.

**Use cases**:
- Ensuring dependent services are available before accepting traffic
- Preventing traffic to pods still initializing
- Managing rolling updates safely

### 3. Startup Probes

**Purpose**: Determine when an application has started. Until the startup probe succeeds, other probes are disabled.

**Use cases**:
- Applications with slow startup times
- Legacy applications that require additional startup time
- Preventing premature liveness checks

## Probe Methods

Each probe can use one of the following methods:

1. **HTTP GET**: Performs an HTTP GET request to a specified path and port. Success is determined by HTTP response code (200-399).

2. **TCP Socket**: Attempts to establish a TCP connection to a specified port. If the connection succeeds, the probe succeeds.

3. **Exec**: Executes a command inside the container. If the command exits with status code 0, the probe succeeds.

## Common Configuration Parameters

- `initialDelaySeconds`: Time to wait after container startup before probes begin
- `periodSeconds`: How often to perform the probe
- `timeoutSeconds`: Time to wait for a probe response before timing out
- `successThreshold`: Minimum consecutive successes for the probe to be considered successful
- `failureThreshold`: Number of consecutive failures before giving up

## Examples

### HTTP Liveness Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
spec:
  containers:
  - name: web-server
    image: nginx:latest
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /healthz
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 10
      timeoutSeconds: 5
      failureThreshold: 3
```

### TCP Readiness Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-database
spec:
  containers:
  - name: redis
    image: redis:latest
    ports:
    - containerPort: 6379
    readinessProbe:
      tcpSocket:
        port: 6379
      initialDelaySeconds: 5
      periodSeconds: 10
```

### Exec Startup Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-startup-check
spec:
  containers:
  - name: app-container
    image: myapp:latest
    startupProbe:
      exec:
        command:
        - cat
        - /tmp/startup-complete
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 30  # Allow up to 5 minutes (30 * 10s) for startup
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
```

### Combined Probe Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-application
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web-app
        image: my-web-app:v1
        ports:
        - containerPort: 8080
        startupProbe:
          httpGet:
            path: /startup
            port: 8080
          failureThreshold: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 15
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

## Best Practices

1. **Use appropriate probe types** for their intended purposes
2. **Set realistic timeouts** based on your application's behavior
3. **Implement health endpoints** that check critical dependencies
4. **Use startup probes** for applications with variable startup times
5. **Configure proper thresholds** to avoid premature restarts
6. **Keep health checks lightweight** to avoid resource contention
7. **Gradually refine probe parameters** based on real-world behavior
8. **Implement graceful shutdown** mechanisms in your applications
