# Valkey cluster on Minikube

This is a simple demonstration to set up a Valkey cluster and get it running in Kubernetes (minikube).

## Steps to run the cluster

1. Start minikube with enough resources:

   ```bash
   minikube start --memory=4096 --cpus=4
   ```

2. Create the service and statefulset:

   ```bash
   kubectl apply -f valkey-service.yaml
   kubectl apply -f valkey-statefulset.yaml
   ```

   Wait for pods to spin up. Then run:

   ```bash
   kubectl get pods -l app=valkey
   ```

   This will return the set of pods running (6 as per the configuration).

## Create the Valkey cluster

Choose one of the pods (for example `valkey-0`) and exec into it:

```bash
kubectl exec -it valkey-0 -- sh
```

Inside the pod run the cluster create command:

```bash
valkey-cli --cluster create \
  valkey-0.valkey:6379 \
  valkey-1.valkey:6379 \
  valkey-2.valkey:6379 \
  valkey-3.valkey:6379 \
  valkey-4.valkey:6379 \
  valkey-5.valkey:6379 \
  --cluster-replicas 1
```

✅ Cluster is now created.

## Verify the cluster

```bash
kubectl exec -it valkey-0 -- valkey-cli cluster info
kubectl exec -it valkey-0 -- valkey-cli cluster nodes
```

(This should show 3 master nodes and 3 slave nodes.)

You can view the cluster in the minikube dashboard:

```bash
minikube dashboard
```

## Deleting everything

```bash
kubectl delete statefulset valkey
kubectl delete svc valkey
```