# ⚠️ Bitnami Breaking Changes Workaround Guide

## What Happened?

Broadcom (owner of Bitnami) has made significant changes to their Docker image distribution:

1. **Most older image tags have been removed** from the main Bitnami Docker repositories
2. **Only `latest` tags remain** in the main `bitnami/` repository
3. **Legacy images moved** to the `bitnamilegacy/` repository (temporary availability)

This affects several Helm charts used in this course:
- `bitnami/wordpress`
- `bitnami/nginx`
- `bitnami/postgresql` (used as a subchart dependency)

## The Error You'll See

When installing charts that reference removed image tags:

```
NAME                  READY   STATUS             RESTARTS   AGE
local-wp-wordpress-0  0/1     ErrImagePull       0          30s
local-wp-mariadb-0    0/1     ImagePullBackOff   0          30s
```

## Solution Overview

We've created override files that redirect image pulls to `bitnamilegacy/` repositories. Use these with the `--values` flag when running Helm commands.

---

## Override Files Location

```
setting-values/
├── wp-bitnami-legacy-override.yaml    # For WordPress charts
├── nginx-bitnami-legacy-override.yaml # For NGINX charts
└── custom-values.yaml                 # Your existing custom values
```

---

## Quick Reference: Updated Commands

### Installing WordPress

**❌ Old (broken):**
```bash
helm install local-wp bitnami/wordpress --version 23.1.20
```

**✅ New (working):**
```bash
helm install local-wp bitnami/wordpress --values setting-values/wp-bitnami-legacy-override.yaml
```

### Installing NGINX

**❌ Old (broken):**
```bash
helm install nginx-release bitnami/nginx --version 18.0.0
```

**✅ New (working):**
```bash
helm install nginx-release bitnami/nginx --values setting-values/nginx-bitnami-legacy-override.yaml
```

### Upgrading with Custom Values

**❌ Old (broken):**
```bash
helm upgrade local-wordpress bitnami/wordpress --reuse-values --values custom-values.yaml --version 23.1.20
```

**✅ New (working):**
```bash
helm upgrade local-wordpress bitnami/wordpress --reuse-values --values setting-values/wp-bitnami-legacy-override.yaml --values setting-values/custom-values.yaml
```

### Using helm template

**❌ Old (broken):**
```bash
helm template bitnami/wordpress --version 23.1.20
```

**✅ New (working):**
```bash
helm template bitnami/wordpress --values setting-values/wp-bitnami-legacy-override.yaml
```

---

## Exercise-by-Exercise Guide

### Section 1: Helm Fundamentals

| Exercise | Notes |
|----------|-------|
| 02 - Exploring Repositories | No changes needed - just adds repos |
| 03 - Installing WordPress | Add `--values setting-values/wp-bitnami-legacy-override.yaml` |
| 07 - Setting Values CLI | Add image override `--set` options |
| 08 - Setting Values Files | Include override file alongside custom values |
| 09 - Helm Upgrade Values | Use override file with upgrades |
| 10 - Upgrade Chart Version | Work with latest version only (no `--version` flag) |

### Section 6: Advanced Topics

| Exercise | Notes |
|----------|-------|
| 14 - Helm Diff Plugin | Use `--values setting-values/nginx-bitnami-legacy-override.yaml` |

---

## Troubleshooting

### 1. Still Getting ErrImagePull?

First, uninstall the broken release:
```bash
helm uninstall local-wp
```

Clean up any leftover PVCs:
```bash
kubectl delete pvc data-local-wp-mariadb-0
kubectl delete pvc --selector=app.kubernetes.io/instance=local-wp
```

Then reinstall with the override file:
```bash
helm install local-wp bitnami/wordpress --values setting-values/wp-bitnami-legacy-override.yaml
```

### 2. Multiple Values Files

When using multiple values files, order matters - later files override earlier ones:
```bash
helm install local-wp bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --values setting-values/custom-values.yaml
```

### 3. Combining with --set

You can still use `--set` alongside values files:
```bash
helm install local-wp bitnami/wordpress \
  --values setting-values/wp-bitnami-legacy-override.yaml \
  --set wordpressUsername=admin
```

### 4. PostgreSQL Subchart (config-store)

The `subcharts/config-store/values.yaml` has been updated with bitnamilegacy overrides. If you need to update manually:
```yaml
postgresql:
  image:
    registry: docker.io
    repository: bitnamilegacy/postgresql
```

Then rebuild dependencies:
```bash
cd subcharts/config-store
helm dependency update
```

### 5. Checking Current Image

To see what image a pod is using:
```bash
kubectl describe pod <pod-name> | grep "Image:"
```

### 6. bitnamilegacy Repository May Be Removed

⚠️ The `bitnamilegacy` repository is temporary. If it's removed in the future:
- Consider using Bitnami Secure Images (requires subscription)
- Use alternative charts from other providers
- Build your own images

---

## What's in the Override Files?

### wp-bitnami-legacy-override.yaml
```yaml
image:
  registry: docker.io
  repository: bitnamilegacy/wordpress

mariadb:
  image:
    registry: docker.io
    repository: bitnamilegacy/mariadb
```

### nginx-bitnami-legacy-override.yaml
```yaml
image:
  registry: docker.io
  repository: bitnamilegacy/nginx
```

---

## Official Resources

- [Bitnami Legacy Docker Hub](https://hub.docker.com/u/bitnamilegacy)
- [Bitnami Secure Images](https://bitnami.com/enterprise)
- [Helm Documentation](https://helm.sh/docs/)

---

## Questions?

If you encounter issues not covered here, check:
1. The course Q&A section
2. The official Helm documentation
3. Bitnami's GitHub issues

Good luck with your Helm learning journey! 🚀
