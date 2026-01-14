---
title: "GitOps mit ArgoCD: Die Zukunft der Continuous Delivery"
date: 2025-02-18T10:00:00+01:00
tags: ["gitops", "argocd", "kubernetes", "ci/cd", "devops", "automatisierung"]
image: argocd_gitops.png
draft: false
---

In der Welt von Kubernetes und Cloud-Native-Entwicklung hat sich **GitOps** als Goldstandard für Continuous Delivery etabliert. Es verlagert das Paradigma von manuellen Deployments und imperativen Skripten hin zu einem deklarativen, versionskontrollierten Ansatz, bei dem das Git-Repository die einzige Quelle der Wahrheit (Single Source of Truth) ist.

Im Zentrum dieser Bewegung steht **ArgoCD**, ein deklaratives Continuous-Delivery-Tool für Kubernetes. Dieser Artikel beleuchtet die Kernprinzipien von GitOps, warum ArgoCD branchenführend ist und wie Sie es nutzen können, um robuste, automatisierte Delivery-Pipelines aufzubauen.

## Was ist GitOps?

GitOps ist ein operatives Framework, das bewährte DevOps-Praktiken aus der Anwendungsentwicklung – wie Versionskontrolle, Zusammenarbeit, Compliance und CI/CD-Tools – auf die Infrastrukturautomatisierung überträgt.

Die vier Schlüsselprinzipien von GitOps sind:

1.  **Deklarativ**: Das gesamte System wird deklarativ beschrieben (z. B. durch YAML-Manifeste).
2.  **Versioniert & Unveränderlich**: Der gewünschte Zustand wird in Git gespeichert, was eine vollständige Änderungshistorie und einfache Rollbacks ermöglicht.
3.  **Automatisierte Bereitstellung**: Genehmigte Änderungen in Git werden automatisch auf das System angewendet.
4.  **Software-Agenten**: Agenten stellen die Korrektheit sicher und warnen bei Abweichungen zwischen dem gewünschten Zustand (Git) und dem tatsächlichen Zustand (Cluster).

## Warum ArgoCD?

Während es andere GitOps-Tools wie Flux gibt, zeichnet sich **ArgoCD** durch seine visuelle Benutzeroberfläche, seinen umfangreichen Funktionsumfang und die tiefe Integration in das Kubernetes-Ökosystem aus.

### Die wichtigsten Vorteile

-   **Transparenz**: ArgoCD bietet einen Echtzeit-Überblick über den Status Ihrer Anwendung und zeigt Sync-Status, Integrität (Health) und Unterschiede (Diffs) zwischen Git und dem Cluster an.
-   **Multi-Cluster-Management**: Sie können Deployments über mehrere Kubernetes-Cluster hinweg von einer einzigen ArgoCD-Instanz aus verwalten.
-   **SSO-Integration**: Nahtlose Integration mit OIDC-Anbietern (GitHub, GitLab, Okta) für eine sichere Zugriffskontrolle.
-   **Drift Detection**: ArgoCD überwacht den Cluster ständig. Wenn jemand eine Ressource manuell ändert (z. B. `kubectl edit`), erkennt ArgoCD die Abweichung (Drift) und kann sie automatisch korrigieren.

## Wie ArgoCD funktioniert

ArgoCD folgt einem **Pull-Modell**. Im Gegensatz zu herkömmlichen CI/CD-Pipelines (Push-Modell), bei denen der CI-Server (wie Jenkins oder GitLab CI) `kubectl apply` ausführt, um Änderungen in den Cluster zu pushen, läuft ArgoCD *innerhalb* des Clusters und *zieht* (pullt) Änderungen aus Git.

![ArgoCD Architektur](argocd_architecture.png)

1.  **Repo Server**: Verwaltet den Status des Git-Repositorys.
2.  **Application Controller**: Vergleicht den Live-Status im Cluster mit dem gewünschten Status in Git.
3.  **API Server**: Stellt die API für die Web-UI, CLI und CI/CD-Integration bereit.

Wenn ein Entwickler eine Änderung am Manifest-Repository vornimmt (z. B. Aktualisierung eines Docker-Image-Tags), erkennt ArgoCD die Änderung und synchronisiert den Cluster, um den neuen Status widerzuspiegeln.

## Erste Schritte mit ArgoCD

Die Einrichtung von ArgoCD ist unkompliziert. Hier ist eine Kurzanleitung, um Ihre erste Anwendung zum Laufen zu bringen.

### 1. ArgoCD installieren

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Zugriff auf die UI

Standardmäßig wird der ArgoCD API-Server nicht mit einer externen IP offengelegt. Sie können über Port-Forwarding darauf zugreifen:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Öffnen Sie nun `https://localhost:8080` in Ihrem Browser. Das initiale Admin-Passwort ist in einem Secret gespeichert:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 3. Eine Anwendung erstellen

Sie können eine App über die UI oder mithilfe einer deklarativen YAML-Datei erstellen. Hier ist ein Beispiel für ein `Application`-Manifest:

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

Das Anwenden dieses Manifests weist ArgoCD an, den Ordner `guestbook` aus dem Beispiel-Repository mit dem Namespace `guestbook` in Ihrem Cluster zu synchronisieren.

## Best Practices für die Produktion

Um das Beste aus ArgoCD in einer Produktionsumgebung herauszuholen, sollten Sie diese Strategien berücksichtigen:

### Das "App of Apps"-Muster

Hunderte von Microservices einzeln zu verwalten, ist mühsam. Das **App of Apps**-Muster beinhaltet das Erstellen einer Root-ArgoCD-Anwendung, die auf einen Ordner verweist, der weitere Application-Manifeste enthält. Dies ermöglicht das Bootstrapping einer gesamten Cluster-Konfiguration mit einem einzigen Sync.

### Trennung von Konfiguration und Quellcode

Bewahren Sie Ihren Anwendungsquellcode (Java, Go, Python) in einem Repository und Ihre Kubernetes-Manifeste (Helm Charts, Kustomize-Dateien) in einem separaten **Config-Repository** auf. Dies stellt sicher, dass die CI-Pipeline das Artefakt baut und die CD-Pipeline (GitOps) nur ausgelöst wird, wenn sich die Konfiguration ändert.

### Secrets Management

Speichern Sie keine unverschlüsselten Secrets in Git. Verwenden Sie Tools, die mit ArgoCD integriert sind:
-   **Sealed Secrets**: Verschlüsselt Secrets in `SealedSecret`-Ressourcen, die sicher in Git gespeichert werden können.
-   **External Secrets Operator**: Ruft Secrets aus externen Vaults (AWS Secrets Manager, HashiCorp Vault) ab und injiziert sie in den Cluster.

### Sync Waves und Hooks

Verwenden Sie **Sync Waves**, um die Reihenfolge des Deployments zu steuern (z. B. sicherstellen, dass die Datenbank läuft, bevor die Anwendung startet). Nutzen Sie **Resource Hooks** (PreSync, PostSync), um Datenbankmigrationen oder Smoke-Tests während des Deployment-Prozesses durchzuführen.

## Fazit

GitOps mit ArgoCD transformiert den Betrieb von Kubernetes. Es bringt die Disziplin der Softwareentwicklung – Code Reviews, Versionierung und Automatisierung – in das Infrastrukturmanagement. Durch die Einführung von ArgoCD können Teams schneller deployen, sich sofort von Fehlern erholen und eine sichere, auditierbare Delivery-Pipeline aufrechterhalten.

Wenn Sie Ihren Delivery-Stack modernisieren möchten, ist ArgoCD das Werkzeug, das Ihre DevOps-Reife auf die nächste Stufe hebt.
