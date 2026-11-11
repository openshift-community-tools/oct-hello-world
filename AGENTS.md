# AGENTS.md — Hello World (OCT extension)

This is **OpenShift Community Tools (OCT)**, a **community project**, not an official Red Hat supported product. Do not describe it as official Red Hat software.

This repository is the **Hello World** ConsolePlugin — a minimal template and reference for building new OCT extensions. It is **not** the OCT storefront. Catalog hubs live in `oct-storefront`.

## Identifiers

| | Value |
| --- | --- |
| Plugin ID / ConsolePlugin / `package.json` `consolePlugin.name` | **`oct-hello-world`** |
| Image | `quay.io/openshift-community-tools/oct-hello-world:1.0.0-ocp4.22` (`<semver>-ocp<major.minor>`) |
| i18n | `plugin__oct-hello-world` |
| Route | `/community-tools/management/hello-world` |

Display name is **Hello World**. No PVC or discovery sidecar.

**Current version:** `1.0.0` (package.json / consolePlugin.version).

## What this plugin owns

- A single page at `/community-tools/management/hello-world` showing the template info
- Reads `ClusterVersion` (`config.openshift.io/v1`) to display the cluster's OpenShift version
- Links to the GitHub repository and developer guide

Uses the signed-in user's console credentials (Console SDK). No extra ServiceAccount.

## Purpose

This is a **template/reference** for building new OCT extensions. Fork this repo to create your own OpenShift Community Tools module. It demonstrates:

- ConsolePlugin SDK integration with webpack
- PatternFly 6 component usage
- CommunityDisclaimer component
- i18n setup with `react-i18next`
- Kubernetes resource watching (`useK8sWatchResource`)
- OCT naming and versioning conventions
- Deploy manifests (Namespace, Deployment, Service, ConsolePlugin)

## OpenShift and extension versions

Two axes in the catalog: git tag **`v1.x.x`** (semver) and optional branch **`ocp-X.Y`** when PatternFly or APIs diverge. Image tags **always** `<semver>-ocp<major.minor>` (e.g. `1.0.0-ocp4.22`). **Always publish both** `<semver>-ocp4.21` and `<semver>-ocp4.22` (same digest if bits match). Catalog `versions[]` lists both minors when those tags exist.

- Git: `main` tracks the newest supported minor (currently **4.22**).
- PatternFly 6 on 4.22; do not mix PF majors on one branch.

## Navigation

This plugin **does not** register the Community Tools section or hubs. Open from the storefront **Management** tile or `/community-tools/management/hello-world`.

## No environment-specific hardcoding

Never bake in lab hostnames, StorageClasses, or similar. See `.cursor/rules/oct-no-env-hardcoding.mdc`.

## Security

Rule: `.cursor/rules/oct-security.mdc`; model: oct-storefront `docs/security-model.md`. This plugin is the `plugin` level: no backend, no RBAC, no API access. Keep the Deployment hardening (`readOnlyRootFilesystem` with emptyDir `/tmp` and `/var/cache/nginx`, `automountServiceAccountToken: false`, non-root, no capabilities). The image gives group 0 write access instead of `chmod 777`. `nginx.conf` sends no CORS headers: the console loads plugins through its own proxy. Report vulnerabilities privately (SECURITY.md). If you add a backend, start from oct-storefront `docs/extension-template/backend/`.

## Catalog tile

Storefront `catalog/community.yaml`: `metadata.name: oct-hello-world`, `consolePlugin: oct-hello-world`, `spec.href: /community-tools/management/hello-world`, `category: management`. **Do not list `versions[]` until the combined public image tag exists.**

## Add must go Ready

Follow **oct-storefront** `docs/extension-standard.md`. Confirm the plugin Deployment is Running. This plugin has no PVC.

## PatternFly 6

Import from `@patternfly/react-core` ^6. Do **not** import PatternFly CSS. Prefix new CSS `hw-`. Include `CommunityDisclaimer`.

## Do-not-break list

Do **not** change unless you are deliberately migrating a live cluster:

- Route: `/community-tools/management/hello-world`
- Plugin ID `oct-hello-world`

## Verify

```bash
yarn install
yarn verify   # ts-check, check-i18n, check-repo, build (same as CI)
```

`yarn check-i18n --fix` adds missing translation keys. Contributor workflow and the maintainer-only release steps: [CONTRIBUTING.md](CONTRIBUTING.md).

Do not `oc apply` or push images unless asked.
