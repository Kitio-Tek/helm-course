# Helm Upgrade with Values

> ⚠️ **IMPORTANT: Bitnami Breaking Changes**
> Bitnami has removed older image tags from Docker Hub. You must include the bitnamilegacy override file when upgrading. See [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md) for full details.

## Overview

In this exercise, we'll explore how to upgrade Helm releases with new values. Upgrading is a crucial skill that allows you to modify deployments without completely reinstalling them.

Before diving into the step-by-step guide, try to accomplish the following on your own:

1. Ensure you have an existing WordPress release installed
2. Identify the values you want to change
3. Use `helm upgrade` with the `--reuse-values` flag
4. Verify the upgrade was successful

---

## ⚠️ Before vs After: Bitnami Workaround

### ❌ OLD Command (No longer works - Image Pull Error):
```bash
helm upgrade local-wordpress bitnami/wordpress --reuse-values --values custom-values.yaml --version 23.1.20
```

### ✅ NEW Command (Working):
```bash
helm upgrade local-wordpress bitnami/wordpress \
  --reuse-values \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --values setting-values/custom-values.yaml
```

> **Note:** Even with `--reuse-values`, you should still include the bitnamilegacy override file to ensure images are correctly pulled.

---

## Step-by-Step Guide

1. **Verify Existing Release**: Check your current release:
   ```bash
   helm list
   helm get values local-wordpress
   ```

2. **Prepare Your Changes**: Identify what values you want to update in your `custom-values.yaml` file.

3. **Perform the Upgrade**:
   ```bash
   helm upgrade local-wordpress bitnami/wordpress \
     --reuse-values \
     --values setting-values/wp-bitnami-legacy-override.yaml \
     --values setting-values/custom-values.yaml
   ```

4. **Verify the Upgrade**:
   ```bash
   kubectl get pods
   helm get values local-wordpress
   helm history local-wordpress
   ```

---

## Understanding --reuse-values

The `--reuse-values` flag tells Helm to reuse the values from the previous release and merge them with any new values you provide:

```bash
helm upgrade release-name chart-name \
  --reuse-values \                            # Keep existing values
  --values setting-values/wp-bitnami-legacy-override.yaml \  # Image overrides
  --values setting-values/custom-values.yaml  # Your new customizations
```

**Important:** Even with `--reuse-values`, the bitnamilegacy override should be included to ensure image references are maintained.

---

## Using --set with Upgrades

You can also use `--set` flags during upgrades:

```bash
helm upgrade local-wordpress bitnami/wordpress \
  --reuse-values \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --set replicaCount=3 \
  --set service.type=LoadBalancer
```

---

## Troubleshooting

### If you see `ErrImagePull` or `ImagePullBackOff` after upgrade:

1. **Rollback to previous version:**
   ```bash
   helm rollback local-wordpress
   ```

2. **Re-upgrade with the override file:**
   ```bash
   helm upgrade local-wordpress bitnami/wordpress \
     --reuse-values \
     --values setting-values/wp-bitnami-legacy-override.yaml \
     --values setting-values/custom-values.yaml
   ```

### Checking what changed:
```bash
# View values from a specific revision
helm get values local-wordpress --revision 1

# Compare with current values
helm get values local-wordpress
```

---

## Conclusion

In this lecture, we learned how to upgrade Helm releases with new values. The `--reuse-values` flag is powerful for maintaining configuration while making targeted changes.

**Key takeaway:** Always include the bitnamilegacy override file (`--values setting-values/wp-bitnami-legacy-override.yaml`) when upgrading Bitnami charts, even when using `--reuse-values`.

Keep practicing these upgrade patterns to become more comfortable with managing Helm releases in production environments! 🚀
