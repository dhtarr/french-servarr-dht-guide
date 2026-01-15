# French-Servarr-DHT — Guide trackerless (mainline DHT + trackers publics)

Guide francophone dont l’ambition est d’amener le plus grand nombre à utiliser efficacement la **mainline DHT** et les **trackers publics**.  
Le tutoriel couvre l’installation et l’utilisation d’une stack complète incluant **qBittorrent**, **Servarr** (Radarr/Sonarr & co), **Jellyfin**, **Overseerr (Seer)** et **Bitmagnet**, avec un focus sur les **torrents publics** et les architectures **trackerless**.

---

## ⚠️ Avertissement / état du support

- Je ne suis **pas sous Windows**, donc je ne peux pas tester toutes les instructions Windows “parfaitement”.
- Le guide sera principalement écrit et validé **avec Docker**.
- Je **recommande** de faire pareil (Docker simplifie énormément : dépendances, services, mises à jour, rollback).
- Pour les moins tech : **certaines parties peuvent fonctionner sans Docker**, mais ce ne sera pas le chemin le plus simple ni celui que je peux garantir.
- **Docker**, en une ligne : c’est un système qui permet de lancer des applis dans des **conteneurs** (isolés et reproductibles), ce qui évite les galères de dépendances et rend l’installation/maintenance beaucoup plus simple.
  - 👉 **On installera Docker dans `1. Installations`.**

---

## 🎯 Ce que vous allez apprendre (haut niveau)

- Installer une stack torrent/self-hosted orientée **torrents publics**
- Comprendre comment fonctionne la **mainline DHT** (et ce qu’elle n’est pas)
- Créer et **publier** un torrent public de manière réaliste (seed, pérennité, trackerless)
- Mettre en place une médiathèque avec **Jellyfin** + gestion de demandes via **Overseerr**
- (Optionnel) Accéder à plus de contenu via **Bitmagnet**
- (Optionnel) Contourner certaines protections Cloudflare avec **FlareSolverr**
- (Optionnel) Rendre un service accessible depuis Internet via **Cloudflare**

> Note : ce guide est une **introduction**. Le but est de vous rendre autonome avec des bases propres et de bons réflexes.

---

## 🧱 Prérequis minimaux

- Une machine capable de faire tourner **Docker** (recommandé) ou des services natifs
- Une **connexion Internet stable**
- Un peu d’espace disque (le “combien” dépend de votre usage)
- De la patience : le **seed** et la disponibilité, c’est une affaire de durée

---

## 🔒 VPN : recommandation

Je recommande fortement de **télécharger via un VPN**, surtout si vous utilisez des torrents publics.  
Ça ne remplace pas les bonnes pratiques (ports, pare-feu, réglages du client), mais ça évite pas mal de problèmes classiques.

---

## 📚 Structure du repo

- [`1. Installations`](./1-installation/)  
  Installer Docker (recommandé), qBittorrent, Servarr, et l’intégration entre eux.

- [`2. Creer et publier un torrent public`](./2-publication-torrent-public/)  
  Le cœur du sujet : torrent public, trackerless, seed correct, et publication “utile” (dans le monde réel).

- [`3. Plateforme de streaming personnelle (Jellyfin + Seerr)`](./3-jellyfin-seerr/)  
  Lecture, demandes, automatisation, et gestion “Netflix-like” mais self-hosted.

- [`4. Optionnel - Gerer les sous titres`](./4-sous-titres-bazarr/)  
  Bazarr, bonnes pratiques, et automatisation des sous-titres.

- [`5. Optionnel - Torrent avances, DHT avec Bitmagnet`](./5-bitmagnet-dht-avance/)  
  Exploration/observabilité de la DHT et cas d’usage avancés.

- [`6. Optionnel - Contourner Cloudflare (FlareSolverr)`](./6-contourner-cloudflare-flaresolverr/)  
  Notes et méthodes pour gérer certains blocages Cloudflare (quand c’est pertinent).

- [`7. Optionnel - Mettre un nom de domaine`](./7-nom-de-domaine-cloudflare/)  
  Rendre un service accessible avec un nom de domaine (et Cloudflare si besoin).


---

## ⚖️ Disclaimer légal (important)

Ce projet est publié à des fins **éducatives** : comprendre les technologies BitTorrent, la mainline DHT, l’auto-hébergement, et les architectures trackerless.

- Je **n’encourage pas** et **n’incite pas** au téléchargement ou au partage de contenu protégé par le droit d’auteur.
- Vous êtes responsable de ce que vous faites, de ce que vous téléchargez, de ce que vous seedez, et du respect des lois de votre pays.
- Les exemples et configurations visent des usages légitimes (données publiques, distributions libres, projets open source, archives autorisées, etc.).

---

## ✅ Contribution / retours

Si vous êtes sous Windows et que vous constatez une différence :
- ouvrez une issue avec **votre version de Windows**, vos logs, et ce qui bloque
- proposez une variante “Windows-friendly” si vous pouvez la valider

---

## Licence

Ce guide est publié sous licence **Creative Commons Attribution – Partage dans les mêmes conditions 4.0 (CC BY-SA 4.0)**.  
Toute réutilisation ou modification doit conserver cette licence et mentionner la source du projet.
