# Setting Custom Values with YAML Files in Helm

> ⚠️ **IMPORTANT: Bitnami Breaking Changes**
> Bitnami has removed older image tags from Docker Hub. You must include the bitnamilegacy override file alongside your custom values. See [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md) for full details.

## Overview

Welcome to our exploration of using YAML files for setting custom values in Helm releases! In this lecture, we'll learn how to manage our WordPress release better by leveraging YAML file configurations, providing a cleaner and more maintainable solution. 👩‍💻

In this exercise, you will implement a system that allows you to set custom values for your WordPress release using a YAML file. Before diving into the step-by-step guide, I encourage you to try and accomplish the following on your own:

1. Install your WordPress release with the bitnamilegacy override file.
2. Create a YAML file to define custom values for WordPress.
3. Securely manage WordPress credentials by using Kubernetes secrets.
4. Increase the replica count for your WordPress deployment.

Give it a shot! Once you've tried it, you can follow the detailed guide below.

---

## ⚠️ Before vs After: Bitnami Workaround

### ❌ OLD Command (No longer works - Image Pull Error):
```bash
helm install local-wordpress bitnami/wordpress --values custom-values.yaml --version 23.1.20
```

### ✅ NEW Command (Working - Multiple Values Files):
```bash
helm install local-wordpress bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --values setting-values/custom-values.yaml
```

> **Note:** When using multiple `--values` files, the **bitnamilegacy override must come first**, then your custom values file. Later files override earlier ones.

---

## Step-by-Step Guide

Here's how to set custom values for your WordPress release using YAML files:

1. **Install WordPress Release**:

   - Check if there are any releases installed with `helm list`.
   - Use the CLI to install your WordPress release using the bitnamilegacy override:
     ```bash
     helm install local-wordpress bitnami/wordpress \
       --values setting-values/wp-bitnami-legacy-override.yaml
     ```

2. **Create a YAML File**:

   - Navigate to the `setting-values` directory (already contains the override files).
   - The `custom-values.yaml` file should contain your custom values (username, password reference, and replica count).

   Example `custom-values.yaml`:
   ```yaml
   wordpressUsername: lauro
   existingSecret: custom-wp-credentials
   service:
     type: NodePort
   autoscaling:
     enabled: true
     minReplicas: 2
     maxReplicas: 10
   ```

3. **Manage Sensitive Values**:

   - Create a Kubernetes secret for your WordPress credentials using `kubectl create secret`:
     ```bash
     kubectl create secret generic custom-wp-credentials \
       --from-literal=wordpress-password=mySecretPassword
     ```
   - Ensure the secret contains the key `wordpress-password`.

4. **Update Your Deployment**:

   - Use the `helm install` command with **both** values files:
     ```bash
     helm install local-wordpress bitnami/wordpress \
       --values setting-values/wp-bitnami-legacy-override.yaml \
       --values setting-values/custom-values.yaml
     ```
   - Confirm that your deployment created the correct number of pod replicas.

5. **Verify the Installation**:
   - Monitor your pods using `kubectl get pods`.
   - Expose your service and access your WordPress dashboard using your custom credentials.

---

## Understanding Values File Order

When using multiple `--values` files, order matters:

```bash
helm install release-name chart-name \
  --values first-file.yaml \    # Applied first
  --values second-file.yaml \   # Overrides first-file
  --values third-file.yaml      # Overrides both previous files
```

For Bitnami charts, always put the bitnamilegacy override **first**:
```bash
helm install local-wordpress bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \  # Image overrides
  --values setting-values/custom-values.yaml                  # Your customizations
```

---

## Troubleshooting

### If you see `ErrImagePull` or `ImagePullBackOff`:

1. **Uninstall the broken release:**
   ```bash
   helm uninstall local-wordpress
   ```

2. **Clean up PVCs:**
   ```bash
   kubectl delete pvc --selector=app.kubernetes.io/instance=local-wordpress
   ```

3. **Reinstall with both values files:**
   ```bash
   helm install local-wordpress bitnami/wordpress \
     --values setting-values/wp-bitnami-legacy-override.yaml \
     --values setting-values/custom-values.yaml
   ```

---

## Conclusion

In this lecture, we learned how to set custom values for a WordPress release with YAML files, emphasizing the importance of managing sensitive information securely. 

**Key takeaway:** Always include `--values setting-values/wp-bitnami-legacy-override.yaml` **before** your custom values file to ensure the bitnamilegacy images are used.

By implementing these practices, you enhance the maintainability and security of your Helm charts. Keep experimenting with different configurations and stay curious! 🌟
