# projet_de_fin_d-tude
# 🌐 Conception et mise en œuvre d’une infrastructure réseau — CFPT Sénégal-Japon

## 📌 Présentation du projet

Ce dépôt présente un projet de **conception et de mise en œuvre d’une infrastructure réseau LAN** réalisé dans le cadre d’un mémoire de fin d’études au **Centre de Formation Professionnelle et Technique Sénégal-Japon (CFPT-SJ)**.

Le projet consiste à concevoir une infrastructure réseau **structurée, performante, sécurisée et administrable**, répondant aux besoins d’un environnement de formation composé de plusieurs bâtiments.

L’ensemble de l’infrastructure a été conçu, configuré et testé dans un environnement de simulation avec **Cisco Packet Tracer**.

---

## 🎯 Objectifs

Le projet vise principalement à :

* concevoir une architecture réseau adaptée au campus ;
* organiser le réseau à l’aide d’une segmentation en VLAN ;
* mettre en place le routage inter-VLAN ;
* définir un plan d’adressage IPv4 cohérent ;
* déployer les services DHCP et DNS ;
* intégrer une solution d’authentification centralisée avec RADIUS ;
* centraliser les journaux réseau avec SYSLOG ;
* mettre en œuvre des ACL pour contrôler les communications ;
* sécuriser l’administration des équipements réseau ;
* concevoir une infrastructure Wi-Fi sécurisée ;
* prévoir une connexion Internet redondante ;
* effectuer des tests afin de valider le fonctionnement de l’infrastructure.

---

# 🏗️ Architecture réseau

L’architecture proposée repose sur une organisation hiérarchique permettant de séparer les différentes fonctions du réseau.

### Architecture générale

```text
                         INTERNET
                       /          \
                  SONATEL          ADIE
                     |               |
                    R1              R2
                      \             /
                       \           /
                        FW-CFPT
                           |
                         R-CORE
                           |
                      +----------+
                      | SW-CORE  |
                      |  L3 3560 |
                      +----------+
                       /   |   \
                      /    |    \
                   SW-A   SW-B  SW-C ...
                    |      |      |
                  Users   Users   Users
```

### Cœur du réseau

Le cœur de l’infrastructure est assuré par un **Cisco Catalyst 3560-24PS**.

Il assure notamment :

* le routage inter-VLAN ;
* la gestion des interfaces SVI ;
* l’interconnexion des switches d’accès ;
* la communication entre les différents segments du réseau ;
* l’application des politiques de filtrage.

### Accès

L’infrastructure comprend **9 switches Cisco Catalyst 2960-24TT** répartis dans les différents bâtiments du campus.

Les liaisons entre le cœur et les switches d’accès utilisent des **liens trunk** afin de transporter les différents VLAN.

### Périmètre réseau

Le **R-CORE** assure la liaison entre le réseau interne et le périmètre Internet.

Le **FW-CFPT** est représenté dans la simulation par un routeur configuré avec des mécanismes de filtrage, en raison des limitations de Cisco Packet Tracer.

---

# 🏢 Organisation du réseau

Les différents switches d’accès sont répartis dans les bâtiments suivants :

| Switch | Bâtiment |
| ------ | -------- |
| SW-A   | A        |
| SW-B   | B        |
| SW-C   | C        |
| SW-D   | D        |
| SW-F   | F        |
| SW-G   | G        |
| SW-MEL | MEL      |
| SW-ET  | ET       |
| SW-MEC | MEC      |

Le bâtiment **F** constitue notamment un point central de l’infrastructure et accueille le réseau de serveurs ainsi que le NOC.

---

# 🔀 Segmentation VLAN

La segmentation du réseau permet d’isoler les différents profils et services.

| VLAN | Nom            | Réseau          | Passerelle   |
| ---: | -------------- | --------------- | ------------ |
|   10 | ADMINISTRATION | 192.168.10.0/24 | 192.168.10.1 |
|   20 | DIRECTION      | 192.168.20.0/24 | 192.168.20.1 |
|   30 | ENSEIGNANTS    | 192.168.30.0/24 | 192.168.30.1 |
|   40 | ÉTUDIANTS      | 192.168.40.0/24 | 192.168.40.1 |
|   50 | LABORATOIRES   | 192.168.50.0/24 | 192.168.50.1 |
|   60 | SERVEURS       | 192.168.60.0/24 | 192.168.60.1 |
|   70 | INVITÉS        | 192.168.70.0/24 | 192.168.70.1 |
|   80 | CAMÉRAS        | 192.168.80.0/24 | 192.168.80.1 |
|   99 | MANAGEMENT     | 192.168.99.0/24 | 192.168.99.1 |

Le plan d’adressage utilise le réseau privé **192.168.0.0/16**, subdivisé en différents sous-réseaux /24.

---

# 🌐 Routage

Le routage inter-VLAN est réalisé sur le **SW-CORE**, qui fonctionne comme un commutateur de niveau 3.

Chaque VLAN possède une interface virtuelle **SVI** servant de passerelle.

Cette architecture permet aux différents segments de communiquer selon les règles définies par la politique de sécurité.

Une route vers le réseau externe permet également au trafic destiné à Internet de passer par le réseau périmétrique.

---

# 🖥️ Services réseau

Les principaux services sont regroupés dans le **VLAN 60 — SERVEURS**.

| Serveur    | Adresse IP   | Service |
| ---------- | ------------ | ------- |
| SRV-DHCP   | 192.168.60.2 | DHCP    |
| SRV-DNS    | 192.168.60.3 | DNS     |
| SRV-SYSLOG | 192.168.60.4 | SYSLOG  |
| SRV-RADIUS | 192.168.60.5 | RADIUS  |

### DHCP

Le serveur DHCP permet l’attribution dynamique des paramètres IP aux différents réseaux clients.

Un mécanisme de **DHCP Relay** est utilisé afin de permettre aux clients des différents VLAN de communiquer avec le serveur DHCP centralisé.

### DNS

Un service DNS interne est mis en place pour assurer la résolution des noms des ressources et services du réseau.

La zone interne utilisée dans le projet est :

```text
cfpt.local
```

### RADIUS / AAA

Le serveur RADIUS permet de centraliser l’authentification des accès administratifs aux équipements réseau.

Le projet aborde également les principes de l’architecture **AAA** et du protocole **802.1X**.

### SYSLOG

Le serveur SYSLOG centralise les événements générés par les équipements réseau.

Cette centralisation facilite :

* la supervision ;
* le diagnostic ;
* l'analyse des événements ;
* le suivi de l'état des équipements.

---

# 📡 Réseau Wi-Fi

Une solution Wi-Fi a été intégrée à l’architecture.

Deux catégories d’accès sont prévues :

### Réseau étudiants

```text
CFPT-ETUDIANTS
```

Associé au **VLAN 40**.

### Réseau invités

```text
CFPT-INVITES
```

Associé au **VLAN 70**.

Le réseau invités est séparé du réseau interne afin de limiter son accès aux ressources de l’établissement.

La sécurité Wi-Fi repose sur **WPA2-PSK** dans la simulation.

> **Limitation de la simulation :** les équipements Wi-Fi disponibles dans Cisco Packet Tracer ne permettent pas de reproduire toutes les fonctionnalités d’une infrastructure Wi-Fi professionnelle, notamment la gestion avancée de plusieurs SSID. Le mémoire propose donc une architecture adaptée aux possibilités de l’environnement de simulation.

---

# 🛡️ Sécurité réseau

La sécurité repose sur plusieurs mécanismes complémentaires.

### Segmentation

Les VLAN permettent d’isoler les différents profils utilisateurs et services.

### ACL

Des **Access Control Lists (ACL)** sont utilisées afin de contrôler les communications entre les différents VLAN.

La politique vise notamment à empêcher les utilisateurs non autorisés d’accéder aux ressources sensibles telles que les réseaux d’administration et de direction.

### Administration

L’administration des équipements réseau est sécurisée notamment par l'utilisation de **SSH**.

Le réseau dispose également d’un VLAN dédié au **Management**, permettant de séparer l’administration du trafic utilisateur.

### Pare-feu

Le pare-feu périmétrique est simulé à l’aide d’un routeur Cisco configuré pour assurer les fonctions de filtrage nécessaires dans l’environnement Packet Tracer.

---

# 🌍 Connexion Internet

L’architecture prévoit deux connexions Internet :

```text
SONATEL
   |
   R1
   |
FW-CFPT
   |
Réseau interne
   |
ADIE
   |
   R2
```

Cette conception permet d’envisager une meilleure disponibilité de la connexion Internet grâce à deux fournisseurs.

Dans un environnement réel, des mécanismes supplémentaires de haute disponibilité et de basculement automatique pourraient être intégrés.

---

# 🧪 Tests et validation

Après la mise en œuvre, plusieurs catégories de tests ont été réalisées afin de vérifier le fonctionnement de l’infrastructure.

Les tests documentés dans le mémoire comprennent notamment :

* vérification des liens trunk ;
* vérification des interfaces SVI ;
* vérification de la table de routage ;
* tests DHCP ;
* tests DNS ;
* tests de connectivité inter-VLAN ;
* tests d’authentification RADIUS ;
* tests de journalisation SYSLOG ;
* tests des politiques de sécurité ACL.

Les résultats obtenus ont permis de valider le fonctionnement global de l’architecture simulée.

---

# 🧰 Technologies et équipements

### Technologies

* IPv4
* Ethernet
* VLAN
* IEEE 802.1Q
* Routage inter-VLAN
* DHCP
* DNS
* RADIUS
* AAA
* SYSLOG
* ACL
* SSH
* Wi-Fi
* WPA2-PSK

### Équipements simulés

* Cisco Catalyst 3560-24PS
* Cisco Catalyst 2960-24TT
* Cisco 2911
* Server-PT
* AP-PT-N
* PC-PT

### Outil

**Cisco Packet Tracer**

---

# 📊 Résultats

La simulation et les tests réalisés ont permis de vérifier les principales fonctionnalités de l’infrastructure :

| Fonctionnalité      | Validation |
| ------------------- | ---------- |
| Segmentation VLAN   | ✅          |
| Routage inter-VLAN  | ✅          |
| DHCP                | ✅          |
| DNS                 | ✅          |
| RADIUS / AAA        | ✅          |
| SYSLOG              | ✅          |
| ACL                 | ✅          |
| Connectivité réseau | ✅          |
| Wi-Fi               | ✅          |
| Sécurité réseau     | ✅          |

Le projet a ainsi permis de mettre en pratique les différentes étapes d’un projet réseau : **analyse des besoins, conception, configuration, sécurisation, tests et validation**.

---

# 🔭 Perspectives d’évolution

Le mémoire identifie plusieurs améliorations possibles dans le cadre d’un déploiement réel :

* utilisation d’un pare-feu dédié ;
* déploiement de points d’accès Wi-Fi professionnels ;
* utilisation d’un contrôleur Wi-Fi ;
* gestion avancée de plusieurs SSID ;
* mise en place de mécanismes de haute disponibilité ;
* intégration de l’IPv6 ;
* mise en œuvre de la QoS ;
* déploiement de VPN ;
* amélioration de la supervision ;
* utilisation de solutions telles que Cisco DNA Center ou Zabbix.

Ces évolutions permettraient de rapprocher davantage l’architecture simulée d’une infrastructure réseau d’entreprise réelle.

---

# 📁 Contenu du dépôt

```text
projet_de_fin_d-tude/
│
├── README.md
│
└── rapport/
    └── Memoire_Projet_Reseau_CFPT.pdf
```

Le rapport contient l'ensemble de l'étude :

* contexte et analyse des besoins ;
* état de l'art ;
* conception de l'architecture ;
* plan d'adressage ;
* segmentation VLAN ;
* conception des services réseau ;
* réalisation et mise en œuvre ;
* configurations ;
* tests ;
* résultats ;
* conclusion et perspectives.

---

# 🎓 Compétences développées

Ce projet m'a permis de développer et de consolider des compétences en :

* conception d'infrastructures réseau ;
* administration de réseaux Cisco ;
* configuration de switches et routeurs ;
* VLAN et trunking ;
* routage inter-VLAN ;
* adressage IPv4 et subnetting ;
* services DHCP et DNS ;
* RADIUS / AAA ;
* SYSLOG ;
* ACL et sécurité réseau ;
* SSH ;
* réseaux Wi-Fi ;
* diagnostic et validation d'une infrastructure réseau ;
* simulation avec Cisco Packet Tracer.

---

## 📄 Documentation complète

Le **mémoire de fin d'études** contenant l'étude, la conception, la mise en œuvre et les résultats du projet est disponible dans le dossier `rapport/`.

---

## 👨‍💻 Projet académique

**Centre de Formation Professionnelle et Technique Sénégal-Japon (CFPT-SJ)**

**Spécialité : Informatique Industrielle et Réseau**

**Projet de fin d'études — BTS**

**Année académique : 2025–2026**

---

> **Note :** Cette infrastructure a été conçue et validée dans un environnement de simulation Cisco Packet Tracer. Certaines fonctionnalités sont donc représentées de manière simplifiée par rapport à un déploiement sur des équipements professionnels réels.
Réamiser par Ismaila DIALLO
