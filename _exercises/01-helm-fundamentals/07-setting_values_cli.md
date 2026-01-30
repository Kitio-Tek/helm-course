# Setting Values via CLI in Helm

> ⚠️ **IMPORTANT: Bitnami Breaking Changes**
> Bitnami has removed older image tags from Docker Hub. You must include the bitnamilegacy override when setting values. See [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md) for full details.

## Overview

Welcome to the guide on how to set custom values for your WordPress deployment using Helm! In this exercise, we're going to dive into how to override the default values provided in the WordPress Helm chart by providing our own values through the CLI. This is an important skill that will help ensure your applications run smoothly and securely.

In this exercise, we'll be focusing on the following main steps to implement custom configuration values for your WordPress deployment using Helm:

1. **Explore the Default Values**: Review the default configuration values in the WordPress chart documentation.
2. **Identify Key Parameters**: Identify the specific parameters you want to set (like root and user passwords for MariaDB).
3. **Use CLI to Set Values**: Execute the Helm install command using the `--set` flag to specify your own values for the identified parameters.
4. **Verify Deployment**: Check the deployment status and logs to ensure everything is running correctly.
5. **Inspect Secrets**: Verify that the passwords set in your command are correctly reflected in the Kubernetes secrets.

Now, why not give it a shot and see if you can implement this on your own before checking out the detailed steps below? 🛠️

---

## ⚠️ Before vs After: Bitnami Workaround

### ❌ OLD Command (No longer works - Image Pull Error):
```bash
helm install local-wordpress bitnami/wordpress \
  --set mariadb.auth.rootPassword=myAwesomePassword \
  --set mariadb.auth.password=myUserPassword \
  --version 23.1.20
```

### ✅ NEW Command (Working):
```bash
helm install local-wordpress bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --set mariadb.auth.rootPassword=myAwesomePassword \
  --set mariadb.auth.password=myUserPassword
```

> **Note:** The `--values` flag with the override file must be included to redirect images to `bitnamilegacy/`. You can still use all your `--set` flags after it.

---

## Step-by-Step Guide

Here's a clear step-by-step guide to help you through the process:

1. **Open the Helm Chart Documentation**: Start by navigating to the WordPress Helm chart documentation page.

2. **Locate Default Values**: Scroll down to find the default values and identify parameters you'll need to override (such as `mariadb.auth.rootPassword` and `mariadb.auth.password`).

3. **Prepare Your Terminal**: Open your terminal and prepare to run the Helm install command.

4. **Construct Your Helm Install Command**:
   - Begin with the base command: `helm install local-wordpress bitnami/wordpress`
   - **Add the bitnamilegacy override file:** `--values setting-values/wp-bitnami-legacy-override.yaml`
   - Add the `--set` flags for your custom values:
     - `--set mariadb.auth.rootPassword=myAwesomePassword`
     - `--set mariadb.auth.password=myUserPassword`

5. **Run the Command**: Execute the constructed command:
   ```bash
   helm install local-wordpress bitnami/wordpress \
     --values setting-values/wp-bitnami-legacy-override.yaml \
     --set mariadb.auth.rootPassword=myAwesomePassword \
     --set mariadb.auth.password=myUserPassword
   ```

6. **Monitor the Deployment**: Check the status of your pods and logs using `kubectl` to ensure they are up and running correctly:
   ```bash
   kubectl get pods
   kubectl logs -l app.kubernetes.io/instance=local-wordpress
   ```

7. **Verify Secrets**: Use `kubectl get secret` to confirm that your passwords were set as expected:
   ```bash
   kubectl get secret local-wordpress-mariadb -o jsonpath='{.data.mariadb-root-password}' | base64 -d
   ```

Congratulations on getting through your first custom configuration! Remember, this process will help you avoid issues that arise from randomly generated values being used repeatedly. 🎉

---

## Troubleshooting

### If you see `ErrImagePull` or `ImagePullBackOff`:

You likely forgot to include the override file. Fix it by:

1. **Uninstall the broken release:**
   ```bash
   helm uninstall local-wordpress
   ```

2. **Clean up PVCs:**
   ```bash
   kubectl delete pvc --selector=app.kubernetes.io/instance=local-wordpress
   ```

3. **Reinstall with the override file:**
   ```bash
   helm install local-wordpress bitnami/wordpress \
     --values setting-values/wp-bitnami-legacy-override.yaml \
     --set mariadb.auth.rootPassword=myAwesomePassword \
     --set mariadb.auth.password=myUserPassword
   ```

---

## Conclusion

In this lecture, we learned how to override default configuration values in the WordPress Helm chart using the CLI. By setting specific parameters like the root and user passwords for MariaDB, we can maintain consistency and ensure smoother deployments.

**Key takeaway:** Always include `--values setting-values/wp-bitnami-legacy-override.yaml` before your `--set` flags to ensure the bitnamilegacy images are used.

As you continue learning, keep practicing this skill to deepen your understanding of Helm and its value in managing Kubernetes applications.
