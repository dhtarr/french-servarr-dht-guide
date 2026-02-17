# Servarr — comprendre la stack (*arr)

Ce document explique **ce qu’est Servarr**, à quoi servent les différents services,  
et **ce qu’ils font / ne font pas** dans le cadre de ce guide.

👉 L’objectif n’est pas de tout configurer ici, mais de **comprendre les briques** que vous venez de lancer.

---

## Qu’est-ce que Servarr ?

**Servarr** n’est pas un logiciel unique.  
C’est un **ensemble d’outils spécialisés** qui automatisent la gestion de contenus via BitTorrent (ou Usenet).

Chaque outil a un rôle précis :
- chercher du contenu
- déclencher des téléchargements
- organiser les fichiers
- maintenir une bibliothèque propre

👉 Servarr **ne télécharge rien tout seul** et **ne remplace pas un client torrent**.

---

## Les services utilisés dans ce guide

### qBittorrent (client torrent)

- Télécharge et seed les torrents
- Gère la DHT, PEX, LSD
- C’est **le seul composant qui parle réellement au réseau BitTorrent**

👉 Tout passe par lui.

---

### Prowlarr (indexation)

- Centralise les sources de torrents
- Sert d’interface entre :
  - Servarr
  - les indexeurs (trackers publics, RSS, etc.)

⚠️ Important :
- Prowlarr **ne parcourt pas la mainline DHT**
- il ne fait que consommer des sources déclarées

👉 La DHT est gérée par **le client torrent**, pas par Prowlarr.

---

### Radarr / Sonarr

- **Radarr** : films  
- **Sonarr** : séries

Ils permettent :
- de définir ce que vous voulez
- de décider quand télécharger
- d’automatiser la gestion des fichiers

Ils ne :
- cherchent pas directement sur la DHT
- ne seedent rien eux-mêmes

---

## Ce que Servarr fait bien

- Automatiser
- Organiser
- Maintenir une bibliothèque propre
- Éviter les doublons
- Gérer des workflows complexes

---

## Ce que Servarr ne fait PAS

C’est essentiel de le comprendre pour la suite du guide :

- ❌ Explorer la mainline DHT
- ❌ Publier des torrents
- ❌ Seeder activement sans client torrent
- ❌ Remplacer un tracker ou un moteur DHT

👉 Pour ça, on aura besoin :
- du **client torrent**
- et plus tard, d’outils comme **Bitmagnet**

---

## Pourquoi Servarr est quand même utile ici

Même dans une approche **torrents publics / trackerless** :

- Servarr reste excellent pour :
  - gérer des bibliothèques
  - automatiser les flux
  - garder une structure propre

👉 Il est un **outil de confort et d’organisation**, pas le cœur du réseau BitTorrent.

---

## À ce stade du guide

Si vous venez de terminer la partie 1 :

- Servarr est lancé
- Les interfaces sont accessibles
- Mais :
  - aucun torrent public n’est encore créé
  - aucune logique DHT avancée n’est en place

👉 C’est normal.

---

➡️ **Prochaine étape :**  
[2. Créer et publier un torrent public](../2-publication-torrent-public/)

Dans la suite, on quitte l’automatisation “classique” pour entrer dans le **vrai sujet** :
- publication
- seed
- pérennité
- DHT
