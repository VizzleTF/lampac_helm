# Lampac Helm Chart

Purpose: Deploy Lampac (torrent streaming service) with Kubernetes best practices.
Source: https://github.com/immisterio/Lampac

## Features
- Configurable image, resources, and replicas
- Optional Ingress with TLS
- Optional persistence (PVC)
- Optional initContainer to bootstrap persistent data
- Optional autoscaling (HPA)
- Standard Kubernetes labels and ServiceAccount support

## Installation
```bash
# From this repository root
helm upgrade --install lampac ./charts/lampac \
  --namespace lampac --create-namespace \
  -f ./charts/lampac/values.yaml
```

## Uninstall
```bash
helm uninstall lampac -n lampac
```

## Values
Key configuration options (non-exhaustive):
- replicaCount: number (default: 1)
- image.repository: string (default: immisterio/lampac)
- image.tag: string (default: uses .Chart.AppVersion if empty)
- image.pullPolicy: string (IfNotPresent | Always | Never)
- service.type: string (default: ClusterIP)
- service.port: number (default: 9118)
- ingress.enabled: boolean (default: false)
- ingress.className: string
- ingress.annotations: map
- ingress.hosts: list of { host, paths: [{ path, pathType }] }
- ingress.tls: list of { secretName, hosts }
- resources: object (requests/limits)
- autoscaling.enabled: boolean
- autoscaling.minReplicas/maxReplicas/targetCPUUtilizationPercentage
- persistence.enabled: boolean
- persistence.storageClass: string
- persistence.accessMode: ReadWriteOnce | ReadWriteMany
- persistence.size: string (e.g., 1Gi)
- persistence.mountPath: string (default: /home)
- initContainer.enabled: boolean
- initContainer.image.repository/tag: strings
- initContainer.command: string array
- env: list of Kubernetes EnvVar
- volumes / volumeMounts: additional volumes and mounts

## Examples
### Example: Ingress with TLS
```yaml
ingress:
  enabled: true
  className: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-issuer"
    kubernetes.io/tls-acme: "true"
  hosts:
    - host: lampac.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: lampac-tls
      hosts:
        - lampac.example.com
```

### Example: Persistence with initContainer
```yaml
persistence:
  enabled: true
  storageClass: ""
  accessMode: ReadWriteOnce
  size: 1Gi
  mountPath: /home

initContainer:
  enabled: true
  image:
    repository: alpine
    tag: latest
  command:
    - /bin/sh
    - -c
    - |
      if [ ! -f /data/current.conf ]; then
        echo "Initializing default config";
        cp -r /home/* /data/;
      fi
volumeMounts:
  - name: data
    mountPath: /data
volumes:
  - name: data
    persistentVolumeClaim:
      claimName: lampac-data
```

## Notes
- By default the container listens on the same port as service.port.
- Health probes check path "/" on the HTTP port.
- When enabling persistence, ensure your container image supports the chosen mountPath.# lampac_helm