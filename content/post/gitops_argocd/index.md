---
title: "GitOps with ArgoCD: The Future of Continuous Delivery"
date: 2025-02-18T10:00:00+01:00
tags: ["gitops", "argocd", "kubernetes", "ci/cd", "devops", "automation"]
image: argocd_gitops.png
draft: false
---

In the world of Kubernetes and cloud-native development, **GitOps** has emerged as the gold standard for continuous delivery. It shifts the paradigm from manual deployments and imperative scripts to a declarative, version-controlled approach where the Git repository is the single source of truth.

At the heart of this movement is **ArgoCD**, a declarative, GitOps continuous delivery tool for Kubernetes. This article explores the core principles of GitOps, why ArgoCD is the industry leader, and how you can leverage it to build robust, automated delivery pipelines.

## What is GitOps?

GitOps is an operational framework that takes DevOps best practices used for application development—such as version control, collaboration, compliance, and CI/CD tools—and applies them to infrastructure automation.

The four key principles of GitOps are:

1.  **Declarative**: The entire system is described declaratively (e.g., using YAML manifests).
2.  **Versioned & Immutable**: The desired state is stored in Git, providing a complete history of changes and easy rollbacks.
3.  **Automated Delivery**: Approved changes in Git are automatically applied to the system.
4.  **Software Agents**: Agents ensure correctness and alert on divergence between the desired state (Git) and the actual state (Cluster).

## Why ArgoCD?

While there are other GitOps tools like Flux, **ArgoCD** stands out for its visual UI, rich feature set, and deep integration with the Kubernetes ecosystem.

### Key Benefits

-   **Visibility**: ArgoCD provides a real-time view of your application status, showing sync status, health, and diffs between Git and the cluster.
-   **Multi-Cluster Management**: You can manage deployments across multiple Kubernetes clusters from a single ArgoCD instance.
-   **SSO Integration**: Seamlessly integrates with OIDC providers (GitHub, GitLab, Okta) for secure access control.
-   **Drift Detection**: ArgoCD constantly monitors the cluster. If someone manually changes a resource (e.g., `kubectl edit`), ArgoCD detects the drift and can automatically correct it.

## How ArgoCD Works

ArgoCD follows a **Pull Model**. Unlike traditional CI/CD pipelines (Push Model) where the CI server (like Jenkins or GitLab CI) runs `kubectl apply` to push changes to the cluster, ArgoCD runs *inside* the cluster and *pulls* changes from Git.

![ArgoCD Architecture](argocd_architecture.png)

1.  **Repo Server**: Caches and manages the Git repository state.
2.  **Application Controller**: Compares the live state in the cluster with the desired state in Git.
3.  **API Server**: Exposes the API for the Web UI, CLI, and CI/CD integration.

When a developer commits a change to the manifest repo (e.g., updating a Docker image tag), ArgoCD detects the change and syncs the cluster to match the new state.

## Getting Started with ArgoCD

Setting up ArgoCD is straightforward. Here is a quick guide to getting your first application running.

### 1. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Access the UI

By default, the ArgoCD API server is not exposed with an external IP. You can access it via port-forwarding:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now open `https://localhost:8080` in your browser. The initial admin password is stored in a secret:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 3. Create an Application

You can create an app via the UI or using a declarative YAML file. Here is an example `Application` manifest:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: guestbook
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Applying this manifest tells ArgoCD to sync the `guestbook` folder from the example repo to the `guestbook` namespace in your cluster.

## Best Practices for Production

To get the most out of ArgoCD in a production environment, consider these strategies:

### The "App of Apps" Pattern

Managing hundreds of microservices individually is tedious. The **App of Apps** pattern involves creating one root ArgoCD Application that points to a folder containing other Application manifests. This allows you to bootstrap an entire cluster configuration with a single sync.

### Separate Config from Source Code

Keep your application source code (Java, Go, Python) in one repository and your Kubernetes manifests (Helm charts, Kustomize files) in a separate **config repository**. This ensures that the CI pipeline builds the artifact, and the CD pipeline (GitOps) only triggers when the configuration changes.

### Secret Management

Don't store raw secrets in Git. Use tools integrated with ArgoCD:
-   **Sealed Secrets**: Encrypts secrets into `SealedSecret` resources that can be safely committed to Git.
-   **External Secrets Operator**: Fetches secrets from external vaults (AWS Secrets Manager, HashiCorp Vault) and injects them into the cluster.

### Sync Waves and Hooks

Use **Sync Waves** to control the order of deployment (e.g., ensure the database is up before the application starts). Use **Resource Hooks** (PreSync, PostSync) to run database migrations or smoke tests during the deployment process.

## Conclusion

GitOps with ArgoCD transforms Kubernetes operations. It brings the discipline of software engineering—code reviews, versioning, and automation—to infrastructure management. By adopting ArgoCD, teams can deploy faster, recover from failures instantly, and maintain a secure, auditable delivery pipeline.

If you are looking to modernize your delivery stack, ArgoCD is the tool that will take your DevOps maturity to the next level.
