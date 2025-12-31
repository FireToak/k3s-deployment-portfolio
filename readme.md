# 🎓 Portfolio - Architecture Conteneurisée (K3s)

Ce dépôt contient le code source et les manifestes "Infrastructure as Code" (IaC) de mon portfolio professionnel. Le projet est conçu pour être déployé sur un cluster **K3s** avec une approche **GitOps** simplifiée pour les mises à jour automatiques.

L'application est une refonte complète basée sur **PHP 8.2**, sans framework lourd, optimisée pour la conteneurisation et le SEO.

## 🏗️ Architecture Technique

Le déploiement orchestre les composants suivants :

* **Application :** PHP 8.2 + Apache (Image Docker optimisée).
* **Frontend :** TailwindCSS (Compilé via pipeline CI).
* **Orchestration :** Cluster Kubernetes léger (K3s).
* **Routing (Ingress) :** Traefik via CRD `IngressRoute` (Gestion avancée du trafic).
* **CD / Auto-update :** **Keel** (Surveillance du registre et mise à jour automatique des Pods sans intervention via annotations).

## 📂 Structure du dépôt

* `/assets`, `/data`, `/includes` : Code source de l'application (Architecture MVC simplifiée).
* `portfolio.yaml` : Définition du **Deployment** (avec annotations Keel) et du **Service** ClusterIP.
* `ingress.yaml` : Configuration du routage Traefik (`IngressRoute`) pour exposer le service sur `louis.loutik.fr`.
* `Dockerfile` : Recette de construction de l'image.

## 🚀 Prérequis

* Un cluster **K3s** fonctionnel avec **Traefik** activé.
* L'opérateur **Keel** installé sur le cluster (pour le déploiement continu).
* Un registre d'images accessible (GitHub Container Registry).

## 🛠️ Installation & Déploiement

1.  **Préparation du Namespace :**
    Nous isolons le projet dans son propre espace logique nommé `site-internet`.
    ```bash
    kubectl create namespace site-internet
    ```

2.  **Configuration des Manifestes :**
    *Assurez-vous que le champ `namespace: site-internet` est bien présent dans les métadonnées de `ingress.yaml` et `portfolio.yaml` avant d'appliquer.*

3.  **Déploiement de l'application :**
    Crée les Pods et le Service interne.
    ```bash
    kubectl apply -f portfolio.yaml -n site-internet
    ```

4.  **Configuration du Routage (Traefik) :**
    Expose le service via l'IngressRoute Traefik.
    ```bash
    kubectl apply -f ingress.yaml -n site-internet
    ```

5.  **Vérification :**
    Vérifiez que les pods sont en statut `Running` et que le service est détecté.
    ```bash
    kubectl get pods -n site-internet
    ```

## 🔄 Cycle de vie (CI/CD)

Le projet utilise le pattern **Watcher** :
1.  Un **Push** sur la branche `main` déclenche une GitHub Action.
2.  L'image Docker est construite et poussée sur **GHCR**.
3.  **Keel** (dans le cluster) détecte le changement de hash de l'image (Polling toutes les 5 min).
4.  Le cluster met à jour le déploiement automatiquement (Rolling Update).

## 👤 Auteur

**Louis MEDO** - Étudiant BTS SIO (SISR)
*Projet réalisé pour valider les compétences de déploiement conteneurisé et d'administration Kubernetes.*