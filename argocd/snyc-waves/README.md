# Sync waves in ArgoCD

The concept of Syncwaves is pretty straightforward. The desired resource is anno‐
tated with the order in which you wish Ago CD to apply your manifests using
argocd.argoproj.io/sync-wave key with an integer value denoted as a string.

```yaml
metadata:
 annotations:
 argocd.argoproj.io/sync-wave: "5"
```

By default, every resource gets assigned “wave 0”, unless otherwise specified via the
annotation. Numbers can be negative as well. So, for example, consider the following:

- Namespace as wave “-1”
- Service Account as wave “0”
- Deployment as wave “1”

The Namespace would be applied first, then the Service Account, and then finally the Deployment. A good use case for Syncwaves is to applyCustom Resource Definitions first before
the corresponding Custom Resource.

## Using Syncwaves within Hooks

Syncwaves can also be used within the confines of a Hook. Meaning you can have
resources within a PreSync Hook Phase be applied in a specific order, within that
order, without affecting other Hook Phases. In the following example, the Job will be
applied in wave “3” within the PreSync Hook Phase.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
 name: create-tables
 annotations:
 argocd.argoproj.io/sync-wave: "3"
 argocd.argoproj.io/hook: PreSync
```

Now, you can also have the following resource in a PostSync hook

```yaml
apiVersion: batch/v1
kind: Job
metadata:
 name: test-deployment
metadata:
 annotations:
 argocd.argoproj.io/sync-wave: "1"
 argocd.argoproj.io/hook: PostSync
```

In these two examples, the create-tables will be applied before the testdeployment even though test-deployment is a lower “wave. This is due to the fact
that the create-tables resource is in a different Hook Phase. The important thing to
note when considering Syncwaves with Hooks is that Syncwaves are scooped within
each Hook Phase.