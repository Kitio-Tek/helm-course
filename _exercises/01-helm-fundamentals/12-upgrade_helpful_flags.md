# Helm Upgrade: Helpful Flags and Best Practices

## Overview

Welcome back! In this guide, we will explore some important flags you can use with the `helm upgrade` command to ensure a smooth upgrade process in your Kubernetes deployments. Our focus is on preventing issues during upgrades by using specific options that help manage resources effectively. 🚀

Before we dive into the step-by-step guide, let's summarize what you'll be implementing in this exercise. You'll try to successfully upgrade a Helm release while incorporating helpful flags to manage potential failures. Here's a quick rundown of what you should aim to do:

1. Identify the service type you need to update for your deployment (change from LoadBalancer to NodePort).
2. Modify your custom values YAML file accordingly.
3. Execute the Helm upgrade command with the relevant flags to handle potential errors.
4. Validate the upgrade and check Helm's revision history to ensure everything is functioning correctly.

Take some time to attempt the implementation on your own before checking out the detailed guide below. Ready? Let's do this! 😉

---

## Step-by-Step Guide

### 1. Delete Existing Service (if needed)
Begin by removing any existing LoadBalancer type service:
```bash
kubectl delete service <your-service-name>
```

### 2. Update Service Type
Modify your values file to change the service type to NodePort. You can use [ArtifactHub](https://artifacthub.io) to reference the correct field if needed.

Example in `custom-values.yaml`:
```yaml
service:
  type: NodePort
```

### 3. Prepare and Run Helm Upgrade Command

Use the following command with helpful flags for a safe upgrade:
```bash
helm upgrade local-wordpress bitnami/wordpress \
  --reuse-values \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --values setting-values/custom-values.yaml \
  --atomic \
  --cleanup-on-fail \
  --debug \
  --timeout 2m
```

#### Flag Explanations:

| Flag | Purpose |
|------|---------|
| `--reuse-values` | Reuses values from the previous release and merges with new values files |
| `--values` | Specifies values files to apply (can be used multiple times) |
| `--atomic` | Automatically rolls back changes if the upgrade fails |
| `--cleanup-on-fail` | Deletes new resources created during a failed upgrade |
| `--debug` | Enables verbose output for troubleshooting |
| `--timeout 2m` | Sets maximum wait time for the upgrade (2 minutes) |

### 4. Monitor the Upgrade
Execute your Helm upgrade command from the terminal and monitor its output for any messages or errors. The `--debug` flag will provide detailed information about what's happening.

### 5. Review the Results
Once the command completes, check the status of your deployment:
```bash
# Check pod status
kubectl get pods

# Check services
kubectl get services

# Review Helm release history
helm history local-wordpress

# Get current values
helm get values local-wordpress
```

### 6. Handle Failed Upgrades
If the upgrade fails:

- The `--atomic` flag will automatically rollback to the previous working version
- The `--cleanup-on-fail` flag will remove any partially created resources
- Review the release history to identify issues:
```bash
helm history local-wordpress
helm get values local-wordpress --revision <previous-revision-number>
```

### 7. Clean Up Resources (if needed)
Manually check for any leftover resources and clean them up as necessary:
```bash
# Check for old replica sets
kubectl get replicasets

# Check for persistent volume claims
kubectl get pvc

# Delete specific resources if needed
kubectl delete pvc <pvc-name>
```

---

## Best Practices

1. **Always use `--atomic` and `--cleanup-on-fail` together** for production upgrades
2. **Test upgrades in a non-production environment** first
3. **Use `--dry-run` flag** to preview changes before applying:
```bash
   helm upgrade local-wordpress bitnami/wordpress \
     --values setting-values/custom-values.yaml \
     --dry-run
```
4. **Keep backups** of your values files and PVCs
5. **Document changes** between revisions for easier troubleshooting

---

## Troubleshooting

### Common Issues:

**Timeout errors:**
- Increase `--timeout` value (e.g., `--timeout 5m`)
- Check pod logs: `kubectl logs <pod-name>`

**Image pull errors:**
- Ensure bitnamilegacy override is included
- Verify image availability in Docker Hub

**Rollback needed:**
```bash
helm rollback local-wordpress <revision-number>
```

---

## Conclusion

In this guide, we learned how to effectively utilize Helm upgrade flags to streamline the deployment process and make it more resilient to failures. By leveraging options like `--atomic` and `--cleanup-on-fail`, you can maintain a clean state in your Kubernetes environment, even when things don't go according to plan. 

The combination of these flags creates a safety net that automatically handles failures, making your Helm upgrades more reliable and production-ready. Keep practicing these concepts to enhance your Helm skills! 🚀