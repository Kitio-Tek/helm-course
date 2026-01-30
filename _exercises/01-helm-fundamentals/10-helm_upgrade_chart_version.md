# Helm Upgrade Chart Version

> ⚠️ **IMPORTANT: Bitnami Breaking Changes**
> Bitnami has removed older image tags from Docker Hub. Version pinning with `--version` is no longer reliable for older chart versions. See [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md) for full details.

## Overview

In this exercise, we'll explore how to upgrade Helm releases to different chart versions. This is useful when you want to take advantage of new features or bug fixes in updated charts.

Before diving into the step-by-step guide, try to accomplish the following on your own:

1. Search for available versions of a chart
2. Understand the differences between chart versions
3. Upgrade to a newer chart version
4. Verify the upgrade was successful

---

## ⚠️ Important Notice: Version Pinning

### The Problem with --version

Previously, you could pin specific chart versions:

```bash
# ❌ OLD - This worked before but now causes Image Pull errors
helm search repo bitnami/wordpress --versions
helm upgrade [RELEASE_NAME] bitnami/wordpress --version 23.1.28
```

The issue is that specific chart versions reference specific Docker image tags that **no longer exist** in the main Bitnami repository.

### The New Approach

Instead of pinning chart versions, use the **latest chart** with the bitnamilegacy override:

```bash
# ✅ NEW - Use latest chart with bitnamilegacy images
helm upgrade local-wordpress bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml
```

---

## Step-by-Step Guide

### 1. Search for Chart Versions (For Reference)

You can still search for available versions:
```bash
helm search repo bitnami/wordpress --versions
```

This will show available versions, but remember that only the latest versions will have working image tags in the main Bitnami repository.

### 2. Understanding Your Options

| Approach | Pros | Cons |
|----------|------|------|
| Latest chart + bitnamilegacy | Works reliably | May have different features than course videos |
| Specific version + bitnamilegacy | Closer to course content | bitnamilegacy may be removed eventually |

### 3. Upgrading with bitnamilegacy Override

**Recommended approach:**
```bash
helm upgrade local-wordpress bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --values setting-values/custom-values.yaml
```

### 4. If You Must Use a Specific Version

If a specific chart version is required and you're using the bitnamilegacy images:
```bash
helm upgrade local-wordpress bitnami/wordpress \
  --version 23.1.28 \
  --values setting-values/wp-bitnami-legacy-override.yaml
```

> ⚠️ Note: This may still fail if the chart references images not available in bitnamilegacy.

### 5. Verify the Upgrade

```bash
# Check release status
helm list

# Check release history
helm history local-wordpress

# Check pods
kubectl get pods

# Check images being used
kubectl describe pod -l app.kubernetes.io/instance=local-wordpress | grep "Image:"
```

---

## Working with Chart Versions in the New Reality

### Listing Installed Chart Information
```bash
# See what chart version is installed
helm list

# Get detailed release info
helm get all local-wordpress
```

### Checking What Would Change
```bash
# Dry run to see what would happen
helm upgrade local-wordpress bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --dry-run
```

---

## Troubleshooting

### If you see `ErrImagePull` or `ImagePullBackOff`:

1. **Rollback to previous working version:**
   ```bash
   helm rollback local-wordpress
   ```

2. **Upgrade again with bitnamilegacy override:**
   ```bash
   helm upgrade local-wordpress bitnami/wordpress \
     --values setting-values/wp-bitnami-legacy-override.yaml
   ```

### Checking revision differences:
```bash
# Compare current with previous revision
helm get values local-wordpress --revision 1
helm get values local-wordpress --revision 2
```

---

## Best Practices for the Current Situation

1. **Always include the bitnamilegacy override** when working with Bitnami charts
2. **Test upgrades in a non-production environment** first
3. **Document the chart version and override used** for reproducibility
4. **Consider migrating to alternative charts** for long-term stability

---

## Conclusion

Chart version management has become more complex due to Bitnami's image policy changes. The key takeaway is:

**Instead of relying on `--version` for version control, use the bitnamilegacy override file to ensure image availability.**

This approach will let you complete the course exercises while the bitnamilegacy images remain available. For production use, consider the long-term alternatives mentioned in [BITNAMI-WORKAROUND.md](../../BITNAMI-WORKAROUND.md).

Keep learning and adapting to these changes in the ecosystem! 🚀
