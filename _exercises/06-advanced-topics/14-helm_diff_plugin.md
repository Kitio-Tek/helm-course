# Helm Diff Plugin Guide

> ⚠️ **IMPORTANT: Bitnami Breaking Changes**
> Bitnami has removed older image tags from Docker Hub. You must use the bitnamilegacy override file when working with NGINX and other Bitnami charts. See [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md) for full details.

Welcome to the Helm Diff Plugin guide! In this session, we'll explore how to use the Helm Diff plugin, a powerful tool that helps visualize changes before you apply updates to your Kubernetes applications. Let's dive into the exciting world of Helm! 🚀

## Overview

Before we jump into a step-by-step guide, why not give it a shot on your own? Here's a quick summary of what we're going to cover:

1. Open your Helm dashboard and ensure the Helm Diff plugin is listed.
2. Install the NGINX chart from the Bitnami repository **with the bitnamilegacy override**.
3. Install the Helm Diff plugin using your terminal.
4. Use the Helm Diff command to compare changes before upgrading.
5. Explore the diff output to understand the impact of the changes.
6. Upgrade the NGINX chart and see how the revisions are reflected in the Helm dashboard.

Give these steps a try before checking the detailed guide below!

---

## ⚠️ Before vs After: Bitnami Workaround

### ❌ OLD Command (No longer works - Image Pull Error):
```bash
helm install nginx-release bitnami/nginx --version 18.0.0
helm diff upgrade nginx-release bitnami/nginx
```

### ✅ NEW Command (Working):
```bash
helm install nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml

helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml
```

---

## Step-by-Step Guide

Let's go through the process together to use the Helm Diff plugin effectively:

### 1. Open Helm Dashboard
Begin by accessing your Helm dashboard in your browser. Make sure the Helm Diff plugin is visible in the plugins list.

### 2. Install NGINX Chart with bitnamilegacy Override

```bash
helm install nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml
```

Verify the installation:
```bash
kubectl get pods -l app.kubernetes.io/instance=nginx-release
```

### 3. Install Helm Diff Plugin
Open a terminal window and install the Helm Diff plugin:

```bash
helm plugin install https://github.com/databus23/helm-diff
```

### 4. Check Installed Plugins
Confirm that both the Helm dashboard and Helm Diff plugins are installed correctly:

```bash
helm plugin list
```

### 5. Run Helm Diff Command
Compare what would change if you upgrade:

```bash
helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml
```

### 6. Review Changes
Examine the output to see a colored diff, indicating what changes will occur if you proceed with the upgrade.

- **Green (+)**: Lines that will be added
- **Red (-)**: Lines that will be removed
- **Yellow (~)**: Lines that will be modified

### 7. Diff with Value Changes
Test with different values to see how they would impact the deployment:

```bash
helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml \
  --set replicaCount=3
```

### 8. Upgrade NGINX
If you're satisfied with the diff, go ahead and perform the upgrade:

```bash
helm upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml
```

### 9. Explore Revisions
Use `helm diff revision` to review differences between various revisions of your release:

```bash
# Compare revision 1 with revision 2
helm diff revision nginx-release 1 2

# Compare current release with a specific revision
helm diff revision nginx-release 1
```

### 10. Experiment with Settings
Feel free to adjust settings, like replica counts, and observe how they impact your deployments:

```bash
# See what would change with 5 replicas
helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml \
  --set replicaCount=5
```

---

## Useful Helm Diff Options

```bash
# Show only changed files
helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml \
  --no-hooks

# Output in a specific format
helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml \
  --output simple

# Suppress common information
helm diff upgrade nginx-release bitnami/nginx \
  --values setting-values/nginx-bitnami-legacy-override.yaml \
  --suppress-secrets
```

---

## Troubleshooting

### If you see `ErrImagePull` or `ImagePullBackOff`:

1. **Uninstall the broken release:**
   ```bash
   helm uninstall nginx-release
   ```

2. **Reinstall with the override file:**
   ```bash
   helm install nginx-release bitnami/nginx \
     --values setting-values/nginx-bitnami-legacy-override.yaml
   ```

### Plugin not found:
```bash
# Reinstall the plugin
helm plugin uninstall diff
helm plugin install https://github.com/databus23/helm-diff
```

---

## Conclusion

Congratulations on taking the initiative to learn about the Helm Diff plugin! 🌟 

Understanding the differences between Helm revisions and upgrades can significantly boost your confidence and efficiency when managing your Kubernetes applications. 

**Key takeaway:** Always include `--values setting-values/nginx-bitnami-legacy-override.yaml` when using `helm diff upgrade` with Bitnami's NGINX chart.

Keep practicing, and embrace the journey of learning and exploring Helm functionalities!
