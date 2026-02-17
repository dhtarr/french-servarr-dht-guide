# 1. Installation

Cette section pose les **fondations techniques** du guide.  
À la fin de cette étape, vous aurez un environnement prêt à **télécharger, seed et gérer des torrents publics** de manière propre et reproductible.

**Objectifs de cette partie** :
- Avoir **Docker et Docker Compose** fonctionnels
- Avoir un **accès réseau sécurisé (VPN)**
- Avoir un **client torrent (qBittorrent)** opérationnel
- Avoir une **stack Servarr** prête à l’emploi


> ⚠️ **Important – à lire avant de commencer**
>
> Cette partie contient **beaucoup de jargon technique** et peut sembler dense si vous n’êtes pas à l’aise avec l’informatique ou l’auto-hébergement.  
> C’est **de loin la partie la plus difficile du guide pour un public non technique**.
>
> Bonne nouvelle : **une fois cette étape passée, tout le reste sera beaucoup plus simple**.  
> Les sections suivantes seront principalement du paramétrage, de l’automatisation et de l’usage quotidien.
>
> 👉 Ne vous découragez pas, prenez votre temps, et **n’hésitez pas à demander à ChatGPT ou à une autre ressource** pour clarifier chaque notion ou chaque étape avant de continuer.

---

## 1.1 Prérequis matériels et réseau

Avant d’aller plus loin, assurez-vous d’avoir :
- Une machine allumée en permanence (PC, serveur, VPS, NAS)
- Une connexion Internet stable
- Suffisamment d’espace disque pour les téléchargements et le seed
- Les droits administrateur sur la machine

---

## 1.2 Pourquoi Docker (et Docker Compose)

**Docker**, en résumé :
- permet de lancer des applications dans des conteneurs isolés
- évite les conflits de dépendances
- facilite les mises à jour et la maintenance

**Docker Compose** permet :
- de décrire toute une stack dans un seul fichier
- de lancer / arrêter tous les services d’un coup
- de reproduire la même installation sur n’importe quelle machine

👉 Vulgarisation volontairement simplifiée :
Docker permet, entre autres, de faire tourner des logiciels Linux sur Windows (ou macOS) sans se compliquer la vie, souvent avec une seule commande.


👉 C’est pour ces raisons que tout le guide s’appuie sur Docker.
Vous trouverez, dans chaque partie, une configuration Docker Compose prête à être copiée et utilisée.

<details>
<summary><strong>📚 Bonus — Comprendre Docker (lecture optionnelle)</strong></summary>

Les ressources ci-dessous sont **purement informatives**.
Elles ne sont **pas nécessaires** pour suivre le guide, mais peuvent être utiles si vous souhaitez comprendre :

* ce qu’est réellement Docker
* comment fonctionnent les conteneurs
* ce que fait Docker “sous le capot”

Ressources recommandées :

* [https://blog.stephane-robert.info/docs/conteneurs/moteurs-conteneurs/docker/](https://blog.stephane-robert.info/docs/conteneurs/moteurs-conteneurs/docker/)
* [https://www.datacamp.com/fr/tutorial/docker-tutorial](https://www.datacamp.com/fr/tutorial/docker-tutorial)


</details>

---

## 1.3 Installation de Docker et Docker Compose

Dans cette section :
- Installation de Docker selon votre OS
- Vérification que Docker fonctionne
- Activation de Docker Compose

> Le guide privilégie Docker natif. Les étapes exactes peuvent légèrement varier selon la distribution ou la version de l’OS.

---

<details>
<summary><strong>Linux</strong></summary>

### Prérequis
- Distribution Linux récente
- Accès administrateur (sudo)

### Démarche générale
- Installer Docker Engine via le gestionnaire de paquets de la distribution
  - Fedora : https://www.linux-fra.com/?p=6385
  - Ubuntu / Debian : https://doc.ubuntu-fr.org/docker#installation
- Ajouter votre utilisateur au groupe `docker` (recommandé) afin d’éviter l’usage de `sudo` à chaque commande :

```bash
sudo usermod -aG docker $USER
````

> ⚠️ Après cette commande, **déconnectez-vous puis reconnectez-vous** (ou redémarrez la machine) pour que le changement soit pris en compte.

* Vérifier que Docker est bien accessible sans `sudo` :

```bash
docker version
```

* Activer le démarrage automatique de Docker au boot :

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

* Vérifier que le service Docker est bien actif :

```bash
systemctl status docker
```

Vous devriez voir le service en état **active (running)**.

---

### Vérification Docker Compose

Docker Compose est aujourd’hui intégré directement à Docker.
Vérifiez qu’il est bien disponible :

```bash
docker compose version
```

Si une version s’affiche, Docker Compose est correctement installé.
Si toutes ces étapes fonctionnent, Docker est prêt et vous pouvez passer à la suite du guide.

### Vérifications
- Docker est accessible sans sudo (optionnel mais recommandé)
- Docker Compose est reconnu par Docker

</details>

---

<details>
<summary><strong>Windows</strong></summary>

### Prérequis
- Windows 10/11 64 bits
- Virtualisation activée dans le BIOS

### Démarche générale
- Télécharger et installer **Docker Desktop** :  
  https://www.docker.com/products/docker-desktop/
- Laisser les options par défaut (WSL2 recommandé)
- Redémarrer Windows si demandé
- Lancer Docker Desktop et attendre l’indication **“Docker is running”**

Lancer un terminal et taper:
```bash
docker version
docker compose version
````
Si ces commandes affichent une version, Docker est correctement installé et fonctionnel.

</details>

---

<details>
<summary><strong>macOS</strong></summary>

### Prérequis
- macOS récent (Intel ou Apple Silicon)
- Accès administrateur

### Démarche générale
- Télécharger et installer **Docker Desktop** :  
  https://www.docker.com/products/docker-desktop/
- Autoriser les composants système demandés
- Lancer Docker Desktop

### Vérifications
- Docker Engine est actif
- Docker Compose est fonctionnel

</details>

---

### Point important à retenir

- Si Docker fonctionne ici, **tout le reste du guide fonctionnera** 🎉

---

## 1.4 Organisation des dossiers

Avant de lancer le moindre conteneur, il est important de **poser une structure de dossiers claire et cohérente**.
Une mauvaise organisation au départ rend vite la maintenance compliquée (perte de données, migrations pénibles, volumes cassés).

👉 **Objectifs de cette section** :

* séparer clairement **les données applicatives** et **les données de contenu**
* garantir la **persistance des données** même si un conteneur est supprimé
* faciliter les sauvegardes et les migrations
* utiliser une structure réutilisable dans tout le guide

---

### 1.4.1 Données persistantes vs données temporaires

Avec Docker, il est essentiel de comprendre la différence entre :

* **Données persistantes**

  * paramètres des applications
  * bases de données
  * fichiers de configuration
  * doivent survivre aux redémarrages et mises à jour

* **Données temporaires**

  * caches
  * fichiers intermédiaires
  * peuvent être recréées sans impact

Dans ce guide, **toutes les données importantes seront stockées hors des conteneurs**, via des volumes ou des bind mounts.

---

### 1.4.2 Séparer configuration et téléchargements

Bonne pratique fondamentale :

* **Configuration** → petite taille, critique
* **Téléchargements / médias** → volumineux, évolutifs

Ne pas les mélanger permet :

* de déplacer les médias sans casser la stack
* de réinstaller un service sans perdre ses réglages
* d’éviter les erreurs de droits ou de chemins

---

### 1.4.3 Structure de dossiers recommandée

Exemple de structure simple et efficace :

```
/Serveur_Tuto/
├── config/
│   ├── qbittorrent/
│   ├── radarr/
│   ├── sonarr/
│   └── prowlarr/
│   
├── downloads/
│   ├── incomplete/
│   ├── complete/
│   └── torrents/
└── compose.yaml
```

* `config/`
  Contient **toutes les configurations persistantes** des services Docker.

* `downloads/`
  Contient les fichiers téléchargés et seedés par le client torrent.

* `compose.yaml`
  Fichier Docker Compose décrivant la stack.

👉 Cette structure est **un exemple**. L’important est la **cohérence**, pas le chemin exact.

---

### 1.4.4 Où sont stockés les fichiers (important)

Sans entrer dans les détails techniques :

- les fichiers utilisés par les applications sont stockés **dans des dossiers normaux de votre machine**
- ils ne sont **pas cachés** ni enfermés dans Docker
- vous pouvez les voir, les déplacer et les sauvegarder facilement

Dans ce guide :
- on utilise toujours des dossiers visibles et explicites
- vous gardez le contrôle total sur vos fichiers
- supprimer un conteneur ne supprime **pas** vos données

👉 Concrètement :  
si vous savez copier un dossier sur votre ordinateur, vous savez sauvegarder et restaurer toute votre installation.

---

### 1.4.5 Droits et permissions

Sur certains systèmes, notamment **Linux**, les applications ont besoin d’autorisations correctes pour lire et écrire des fichiers.  
Si ces droits sont mal configurés, les applications peuvent fonctionner… mais ne rien faire correctement.

👉 C’est une **cause très fréquente de bugs** dans les stacks *arr*.

---
#### Cas particulier : Windows

Bonne nouvelle :  
- sous **Windows**, la gestion des droits est **beaucoup plus permissive**
- la plupart des problèmes de permissions **n’existent tout simplement pas**
- Docker Desktop gère cela automatiquement dans la majorité des cas

👉 En pratique, si vous êtes sous Windows :
- vous n’avez **rien de spécial à faire**
- tant que vous utilisez les dossiers recommandés par le guide, tout fonctionnera

---
#### Ce que fait le guide pour éviter les problèmes

- utilisation d’un utilisateur standard
- dossiers clairs et accessibles
- pas de réglages exotiques ou risqués

👉 Si vous suivez le guide étape par étape, **vous ne devriez jamais avoir à “déboguer des permissions”**, surtout sous Windows.

---

## 1.5 Installer et configurer un VPN

Pourquoi un VPN est indispensable ici :
- protéger votre **adresse IP réelle**
- limiter votre **exposition réseau**
- réduire les risques liés à l’utilisation de **torrents publics**

> 🚨 **DISCLAIMER IMPORTANT — À NE PAS IGNORER**
>
> Je **déconseille absolument à quiconque de continuer ce guide sans VPN**.  
> Utiliser des torrents publics **sans VPN est une très mauvaise idée**, même pour des usages légitimes.
>
> 👉 Si vous n’avez pas de VPN fonctionnel **avant d’aller plus loin**, **arrêtez-vous ici**.

---

### Compatibilité avec Docker (Gluetun)

👉 Gluetun est un conteneur dédié qui :
- établit la connexion VPN
- agit comme un **kill switch**
- force le trafic des autres conteneurs à passer par le VPN

---

### Choix du VPN

Personnellement, j’utilise **Proton VPN**.  
Il fonctionne correctement avec Docker et propose des profils adaptés au P2P.

D’autres VPN sont souvent recommandés dans la communauté :
- **Mullvad**
- **AirVPN**
- Liste officielle des fournisseurs supportés :  
  https://github.com/qdm12/gluetun

Si votre VPN n’est **pas dans cette liste** :
- il ne fonctionnera pas correctement avec ce guide
- ou nécessitera des adaptations non couvertes ici

---

### 1.5.1 Exemple de configuration : Proton VPN

#### Étape 1 — Télécharger la configuration Proton VPN

Rendez-vous sur :  
👉 https://account.protonvpn.com/downloads

Téléchargez une configuration **Wireguard** adaptée à votre OS.

Lors de la génération de la configuration, utilisez les paramètres suivants :

- **Moderate NAT** : ❌ off  
- **NAT-PMP (Port Forwarding)** : ✅ on  
- **VPN Accelerator** : ✅ on  

Ces options sont importantes pour :
- permettre le **port forwarding**
- améliorer la connectivité P2P
- éviter certains problèmes de seed

---

#### Étape 2 — Choix des serveurs

Personnellement :
- j’utilise des **serveurs français**
- pour des raisons de latence et de stabilité

Cependant, certains utilisateurs recommandent :
- des pays avec une **législation plus permissive** concernant le partage de données
- ou historiquement plus tolérants sur le P2P

👉 Le choix du pays dépend :
- de votre tolérance au risque
- de vos performances réseau
- de votre propre analyse légale

Ce guide **ne recommande pas un pays spécifique** et vous laisse responsable de ce choix.

---

#### Étape 3 — Ce que vous devez obtenir à la fin

À ce stade, vous devez avoir fichier de ce style :
```
[Interface]
# Key for VPN
# Bouncing = 3
# NetShield = 1
# Moderate NAT = off
# NAT-PMP (Port Forwarding) = on
# VPN Accelerator = on
PrivateKey = *****
Address = xx.2.0.2/xx
DNS = xx.2.0.1

[Peer]
# FR#100
PublicKey = Z/l/+DxxxxxxxxxxxxxxQ/gw=
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint = xxx.xx.xx.xx:xxxxx
```
> 🚨 **DISCLAIMER IMPORTANT — À NE PAS IGNORER**
>
> Ce fichier contient des **informations hautement sensibles** (clé privée WireGuard, paramètres VPN).
> **Toute fuite permet à un tiers d’utiliser votre VPN et d’usurper votre connexion.**
>
> 👉 **Ne partagez jamais ce fichier**, ne le publiez pas et ne l’incluez pas dans un dépôt non protégé.
> 👉 Si ce fichier a été exposé **ne serait-ce qu’un instant**, **révoquez la clé immédiatement** et générez-en une nouvelle.

---

### 1.5.2 Autres fournisseurs VPN


#### Liste des fournisseurs supportés


La liste officielle est disponible ici :  
👉 https://github.com/qdm12/gluetun/tree/master/internal/provider

Sur ce lien, vous trouverez également le tutoriel et les instructions spécifiques pour chaque fournisseur VPN.

---

## 1.6 Servarr — Kezako
### Qu’est-ce que l’écosystème *arr* ?

Les services appelés *arr* (Radarr, Sonarr, etc.) sont des outils qui permettent **d’automatiser la gestion de contenus multimédias**.

De manière vulgarisée :

* vous indiquez **ce que vous voulez** (un film, une série),
* les services se chargent de **chercher les fichiers correspondants**,
* ils peuvent ensuite **les envoyer automatiquement vers un client de téléchargement**,
* et **les organiser** correctement dans votre bibliothèque.

Chaque service *arr* a un rôle précis, mais ils fonctionnent ensemble.




### Services utilisés dans ce guide

Dans ce guide, nous utiliserons uniquement les services suivants :

* **Prowlarr**
  Sert à gérer les **indexeurs** (sources de recherche).
  Il centralise la configuration et la partage avec les autres services *arr*.

* **Radarr**
  Gère les **films**.
  Il surveille les films souhaités, cherche automatiquement les fichiers disponibles et les transmet au client de téléchargement.

* **Sonarr**
  Gère les **séries TV**.
  Il fonctionne sur le même principe que Radarr, mais pour les épisodes et saisons.

👉 Ces services ne téléchargent rien eux-mêmes : ils **pilotent** un client de téléchargement.

### Déploiement

Tous les services *arr* utilisés dans ce guide sont **définis dans le fichier `compose.yaml`**.

Aucune installation manuelle n’est nécessaire 🎉:

* le déploiement se fait via Docker Compose,
* les services sont déjà reliés entre eux via les réseaux Docker,
* les ports et volumes sont préconfigurés.

---

## 1.7 Lancement et pop-corn 🍿

On y est.  
Si vous avez suivi les étapes précédentes, il ne reste plus qu’à **lancer la stack** et vérifier que tout fonctionne correctement.

---

## 1.7.1 Préparer le dossier serveur

Commencez par créer un dossier dédié, par exemple :

```
Serveur_Tuto/
```

Dans ce dossier, déposez les deux fichiers suivants :

- `compose.yaml`  
  https://github.com/dhtarr/french-servarr-dht-guide/blob/main/1-installation/compose.yaml

- `.env`  
  https://github.com/dhtarr/french-servarr-dht-guide/blob/main/1-installation/.env

```
Serveur/
├── compose.yaml
└── .env
```
---

## 1.7.2 Modifier le fichier `.env`

Ouvrez le fichier `.env` avec un éditeur de texte.

### Clé VPN (obligatoire)

- Repérez la variable :

```

WIREGUARD_PRIVATE_KEY=

```

- Copiez / collez **la clé WireGuard fournie par Proton VPN** après le `=`

👉 Sans cette clé, le VPN **ne fonctionnera pas**.

---
### Pays du serveur VPN (optionnel)

Dans le fichier `compose.yaml`, vous pouvez modifier la variable :

```

SERVER_COUNTRIES=

````

Par défaut, elle est configurée sur France.  
Vous pouvez la changer si vous souhaitez utiliser un autre pays.

👉 Ce réglage est **optionnel**.  
Si vous ne savez pas quoi mettre, **ne touchez à rien**.

---
## 1.7.3 Lancer la stack Docker

Une fois les fichiers en place et configurés, il est temps de lancer Docker.

---

<details>
<summary><strong>Windows / macOS — via Docker Desktop (méthode simple)</strong></summary>

1. Ouvrez **Docker Desktop**
2. Allez dans l’onglet **Containers / Apps**
3. Cliquez sur **“Create / Run”** ou **“Compose”** selon votre version
4. Sélectionnez le fichier `compose.yaml` dans le dossier `Serveur/`
5. Lancez la stack

Docker Desktop va :
- télécharger les images
- créer les conteneurs
- démarrer tous les services automatiquement

👉 Attendez que tout soit en statut **Running**.

</details>

---

<details>
<summary><strong>Linux / ligne de commande</strong></summary>

Dans un terminal, placez-vous dans le dossier `Serveur/` :

```bash
cd Serveur
````

Puis lancez la stack :

```bash
docker compose up -d
```

Docker va télécharger les images et démarrer les services en arrière-plan.

Pour vérifier que tout tourne :

```bash
docker compose ps
```

</details>

---
Voila, votre stack servarr est desormais deploye, votre installation docker tourne
et la maladie du selfhosting vient de vous contaminer. Je vous felicite d'avoir tenu jusque la vous avez fais le plus dur !
Desormais nous pouvons : 
- Acceder a bitorrent en tapant dans le navigateur http://127.0.0.1:9999/
- Prowlarr http://127.0.0.1:9999/
- Sonarr http://127.0.0.1:8989/
- Radarr http://127.0.0.1:7878/

Pour utiliser correctement ccette infrastructure nous aller la configurer ici  [`Configurer Servarr`](./Servarr.md)