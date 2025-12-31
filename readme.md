# ☸️ Infrastructure Kubernetes - Portfolio

Ce dépôt contient exclusivement les manifestes **"Infrastructure as Code" (IaC)** nécessaires au déploiement de mon portfolio professionnel sur un cluster **K3s**.

Il est découplé du code source de l'application pour respecter les bonnes pratiques GitOps et faciliter la gestion de l'infrastructure.

## 🏗️ Architecture Technique

Ces manifestes orchestrent les composants suivants :

* **Namespace dédié :** Isolation logique des ressources dans `site-internet`.
* **Deployment :** Gestion des Pods avec quotas de ressources (CPU/RAM) et stratégie de mise à jour automatique via **Keel** (Zero-Touch Deployment).
* **Service :** Exposition interne via ClusterIP.
* **Ingress Controller :** Routage HTTP via **Traefik** (`IngressRoute` CRD) pour le domaine `louis.loutik.fr`.

## 📂 Contenu du dépôt

* `namespace.yaml` : Création de l'espace de nom `site-internet`.
* `portfolio.yaml` : Définition du Deployment (Image GHCR) et du Service associé.
* `ingress.yaml` : Règle de routage Traefik pour exposer le service sur le port 80.

## 🚀 Prérequis

* Un cluster Kubernetes (K3s) fonctionnel.
* **Traefik** activé (par défaut sur K3s).
* **Keel** installé dans le cluster (nécessaire pour les annotations `keel.sh/*`).

## 🛠️ Installation & Déploiement

La structure des fichiers permet un déploiement en une seule commande ("Bulk Apply").

1.  **Récupération des manifestes :**
    Clonez ce dépôt dans votre dossier de gestion Kubernetes (exemple : `~/k3s/portfolio/`).
    ```bash
    git clone https://github.com/FireToak/k3s-manifests-portfolio.git ~/k3s/portfolio/
    ```

2.  **Application de la configuration :**
    Cette commande crée le namespace, le déploiement, le service et la route d'ingress en une seule fois.
    ```bash
    kubectl apply -f ~/k3s/portfolio/
    ```
    *Note : L'ordre est géré automatiquement par Kubernetes, mais le namespace sera créé en priorité.*

3.  **Vérification :**
    Assurez-vous que tous les objets sont créés dans le bon namespace.
    ```bash
    kubectl get all -n site-internet
    kubectl get ingressroute -n site-internet
    ```

## 🔄 Mise à jour automatique

Aucune action `kubectl` n'est requise pour mettre à jour l'application.
Le fichier `portfolio.yaml` contient les annotations suivantes :
```yaml
keel.sh/policy: force
keel.sh/pollSchedule: "@every 5m"
```

Dès qu'une nouvelle image est poussée sur le registre GHCR (via le pipeline CI du dépôt applicatif), le cluster met à jour les Pods automatiquement sous 5 minutes.

## 👤 Auteur

**Louis MEDO** - Étudiant BTS SIO (SISR)
*Projet réalisé pour valider les compétences de déploiement conteneurisé et d'administration Kubernetes.*