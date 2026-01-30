# Installing Your First Helm Chart

> ⚠️ **IMPORTANT: Bitnami Breaking Changes**
> Bitnami has removed older image tags from Docker Hub. You must use the override file when installing WordPress charts. See [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md) for full details.

## Overview

Welcome to our practical guide on installing your first Helm chart! This exercise is a great opportunity to dive into the Helm ecosystem and learn how to deploy applications in a Kubernetes cluster seamlessly. Let's get started!

In this exercise, we'll be installing a WordPress application using a Helm chart from the Bitnami repository. To give you a head start before we jump into the step-by-step guide, here's an outline of what you'll be doing:

1. Ensure your Kubernetes cluster is running. Check the status using `kubectl version`.
2. Add the Bitnami repository to your Helm repositories (if not yet done so).
3. Search for the WordPress chart within this repository.
4. Install the WordPress chart using the `helm install` command **with the bitnamilegacy override file**.
5. Verify the installation by checking the pods and services created.
6. Expose the application to access it from your browser.

Remember, try to implement these steps yourself first! This is a fantastic way to strengthen your learning. Once you're ready, check out the detailed guide below! 🚀

---

## ⚠️ Before vs After: Bitnami Workaround

### ❌ OLD Command (No longer works - Image Pull Error):
```bash
helm install local-wp bitnami/wordpress --version 23.1.20
```

### ✅ NEW Command (Working):
```bash
helm install local-wp bitnami/wordpress --values setting-values/wp-bitnami-legacy-override.yaml
```

> **Note:** We remove the `--version` flag because the legacy override uses the latest available images from `bitnamilegacy/`. You can still add chart-specific versions if needed, but image versions are controlled by the override file.

---

## Step-by-Step Guide

1. Ensure your Kubernetes cluster is up and running. You can do this by running:
   ```bash
   kubectl version
   ```

2. If your cluster is running fine, add the Bitnami Helm repository:
   ```bash
   helm repo add bitnami https://charts.bitnami.com/bitnami
   helm repo update
   ```

3. Search for the WordPress chart to find its exact name:
   ```bash
   helm search repo wordpress
   ```

4. **Install WordPress using the bitnamilegacy override file:**

   ```bash
   helm install local-wp bitnami/wordpress --values setting-values/wp-bitnami-legacy-override.yaml
   ```

   > 💡 **Why the override file?** The `wp-bitnami-legacy-override.yaml` file redirects Docker image pulls from `bitnami/wordpress` and `bitnami/mariadb` to `bitnamilegacy/wordpress` and `bitnamilegacy/mariadb` respectively.

5. Check the status of the pods to ensure they are running:
   ```bash
   kubectl get pods
   ```

   You should see something like:
   ```
   NAME                              READY   STATUS    RESTARTS   AGE
   local-wp-wordpress-xxx            1/1     Running   0          2m
   local-wp-mariadb-0                1/1     Running   0          2m
   ```

6. To expose the WordPress application, change the service type to `NodePort`:
   ```bash
   kubectl expose deploy local-wp-wordpress --type=NodePort --name=local-wp
   ```

7. Get the service URL to access your application:
   ```bash
   minikube service local-wp --url
   ```
   Copy the provided URL to your browser, and you should see your WordPress site up and running! 🎉

---

## Troubleshooting

### If you see `ErrImagePull` or `ImagePullBackOff`:

1. **Uninstall the broken release:**
   ```bash
   helm uninstall local-wp
   ```

2. **Clean up PVCs (Persistent Volume Claims):**
   ```bash
   kubectl delete pvc data-local-wp-mariadb-0
   kubectl delete pvc --selector=app.kubernetes.io/instance=local-wp
   ```

3. **Reinstall with the override file:**
   ```bash
   helm install local-wp bitnami/wordpress --values setting-values/wp-bitnami-legacy-override.yaml
   ```

### Verify the correct images are being used:
```bash
kubectl describe pod -l app.kubernetes.io/instance=local-wp | grep "Image:"
```

You should see:
```
Image: docker.io/bitnamilegacy/wordpress:...
Image: docker.io/bitnamilegacy/mariadb:...
```

---

## Conclusion

Congratulations on successfully deploying your first application using Helm! This exercise has hopefully shown you how straightforward it is to manage applications with Helm charts. 

**Key takeaway:** Due to Bitnami's breaking changes, always remember to use the `--values setting-values/wp-bitnami-legacy-override.yaml` flag when installing WordPress charts.

Continuing to practice your Helm commands and experimenting with different configurations will further deepen your understanding. Keep up the great work!
