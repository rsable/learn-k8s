# Persistent Storage

* Persistent Volume :
    * A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.
    * It is a resource in the cluster just like a node is a cluster resource.
    * PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV.
    * This API object captures the details of the implementation of the storage, be that NFS, iSCSI, or a cloud-provider-specific storage system.
    * *A PV has to be created before the pod that uses it starts.*
* Persistent Volume Claim :
    * A PersistentVolumeClaim (PVC) is a request for storage by a user.
    * It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory).
    * Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany).
* Storage Class :
    * A StorageClass provides a way for administrators to describe the "classes" of storage they offer.
    * Different classes might map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by the cluster administrators.
    * Kubernetes itself is unopinionated about what classes represent.
    * This concept is sometimes called "profiles" in other storage systems.