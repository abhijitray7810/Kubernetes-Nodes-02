# Persistent Storage for Kubernetes

## Overview

This guide explains the persistent storage architecture in Kubernetes, illustrating how data persists beyond the lifecycle of individual pods and containers.
![image](https://github.com/abhijitray7810/Kubernetes-Notes-02/blob/49cfcb2faa1d022a0078fc17d6494623a3733eff/Persistent%20Storage%20for%20Kubernetes/Screenshot%202025-10-14%20005214.png)

## Storage Types in Kubernetes

### 1. Temporary Filesystem (emptyDir)

Temporary storage that exists only for the lifetime of a pod. When a pod is deleted, this data is lost.

**Use Cases:**
- Cache data
- Temporary processing files
- Scratch space for computations

**Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tmp-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /usr/nginx/html
      name: tmp-storage
  volumes:
  - name: tmp-storage
    emptyDir: {}
```

### 2. Persistent Volumes (PV)

Persistent storage that exists independently of pod lifecycle. Data remains available even after pods are deleted or rescheduled.

**Key Characteristics:**
- Survives pod restarts and deletions
- Can be shared across multiple pods
- Backed by physical storage on the host machine or cloud storage
- Requires explicit deletion to remove data

## Architecture Components

### Pods and Containers

Pods are the smallest deployable units in Kubernetes that can contain one or more containers. Each container can mount storage volumes at specific paths.

### Volumes

Volumes are mounted into containers at specified mount paths (e.g., `/var/logs`). Multiple containers within the same pod can share access to the same volume.

### Persistent Volume (PV)

A piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.

**Example PV Definition:**
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: storage-512gb
spec:
  capacity:
    storage: 512Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data
```

### Persistent Volume Claim (PVC)

A request for storage by a user. It's similar to a pod consuming node resources - PVCs consume PV resources.

**Example PVC Definition:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: storage-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

### Using PVC in a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: container1
    image: nginx
    volumeMounts:
    - mountPath: /var/logs
      name: shared-storage
  - name: container2
    image: busybox
    volumeMounts:
    - mountPath: /var/logs
      name: shared-storage
  volumes:
  - name: shared-storage
    persistentVolumeClaim:
      claimName: storage-claim
```

## Storage Flow

1. **Administrator** provisions a Persistent Volume (e.g., 512GB storage on host machine)
2. **User** creates a Persistent Volume Claim requesting specific storage (e.g., 2GB)
3. **Kubernetes** binds the PVC to an available PV that meets the requirements
4. **Pod** references the PVC in its volume definition
5. **Containers** within the pod mount the volume at specified paths

## Access Modes

- **ReadWriteOnce (RWO)**: Volume can be mounted as read-write by a single node
- **ReadOnlyMany (ROX)**: Volume can be mounted as read-only by many nodes
- **ReadWriteMany (RWX)**: Volume can be mounted as read-write by many nodes

## Best Practices

1. **Use PVCs instead of directly referencing PVs** in pod specifications for better abstraction
2. **Choose appropriate reclaim policies** (Retain, Delete, Recycle) based on data importance
3. **Monitor storage usage** to prevent pods from running out of disk space
4. **Use Storage Classes** for dynamic provisioning when possible
5. **Set resource requests and limits** to prevent storage exhaustion
6. **Back up critical persistent data** regularly
7. **Use appropriate access modes** based on your application architecture

## Common Storage Backends

- **Local Storage**: HostPath, Local Persistent Volumes
- **Network Storage**: NFS, iSCSI, Ceph, GlusterFS
- **Cloud Storage**: AWS EBS, Google Persistent Disk, Azure Disk
- **Container Storage**: Portworx, StorageOS, Longhorn

## Troubleshooting

### PVC Stuck in Pending State
```bash
kubectl describe pvc <pvc-name>
```
Check for: insufficient resources, no matching PV, or storage class issues

### Pod Cannot Mount Volume
```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```
Verify: PVC exists and is bound, correct volume name referenced, mount path permissions

### Data Loss Prevention
- Always use Persistent Volumes for critical data
- Implement regular backup strategies
- Set appropriate reclaim policies
- Test disaster recovery procedures

## Additional Resources

- [Kubernetes Storage Documentation](https://kubernetes.io/docs/concepts/storage/)
- [Persistent Volumes Guide](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
- [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
