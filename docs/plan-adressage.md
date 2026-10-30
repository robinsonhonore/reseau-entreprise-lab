# Plan d'adressage IP – Larsen & Médiator

**Version** : 1.0 – octobre 2026
**Auteur** : Robin Honoré (NetRiff Intégration)

## Objectif

Ce document définit l'adressage IP et les VLAN de l'ensemble du réseau de Larsen & Médiator.
Il sert de référence pour la configuration des équipements, le filtrage sur le firewall
et le dépannage.

## Périmètre

- Siège et magasin principal – Nantes
- Agence de Saint-Nazaire (magasin et atelier de lutherie)
- Agence de Rennes (magasin, cours, location)
- Datacenter « Local 11 » – Rezé : serveurs, DMZ, sortie Internet de toute l'entreprise
- Infrastructure : liens inter-sites, tunnels VPN, loopbacks, accès aux deux FAI

## Architecture en bref

- **Le Local 11 est le point central** : il héberge les serveurs, la DMZ de la boutique en ligne
  et le firewall **FW-DC** (OPNsense), seul point de sortie vers Internet, relié aux deux FAI en BGP.
- **Le siège de Nantes** est relié au Local 11 par deux liens privés (un par routeur), routés en OSPF.
- **Les agences** ont leur propre accès Internet et rejoignent le Local 11 par un tunnel IPsec.
- **Les nomades** (techniciens itinérants) se connectent en VPN SSL sur FW-DC.

## Principes retenus

**Plage globale** : 10.0.0.0/8 (adressage privé, RFC 1918).

**Logique d'adressage** : `10.<site>.<VLAN>.0/24`, pour qu'une adresse indique d'elle-même
son site et son VLAN.

| Code site | Site | Plage | Préfixe des noms d'équipements |
|---|---|---|---|
| 1 | Nantes (siège) | 10.1.0.0/16 | NTE |
| 2 | Saint-Nazaire | 10.2.0.0/16 | SNZ |
| 3 | Rennes | 10.3.0.0/16 | REN |
| 10 | Local 11 (datacenter) | 10.10.0.0/16 | DC |
| 250 | Nomades (VPN SSL) | 10.250.0.0/16 | – |
| 254 | Liens point à point et tunnels | 10.254.0.0/16 | – |
| 255 | Loopbacks | 10.255.0.0/16 | – |

**VLAN harmonisés** : un même numéro de VLAN a le même rôle sur tous les sites.
Les VLAN 100 et plus sont réservés au datacenter.

**Taille des sous-réseaux** : /24 pour tous les VLAN, par souci de lisibilité
(l'espace privé rend le gaspillage sans conséquence). Masques serrés pour l'infrastructure :
/30 pour les liens point à point et les tunnels, /32 pour les loopbacks.

**Conventions dans chaque sous-réseau** :

| Adresses | Siège (2 routeurs) | Agences et datacenter (1 routeur) |
|---|---|---|
| .1 | Passerelle virtuelle VRRP | Passerelle (le routeur ou le firewall) |
| .2 / .3 | R1-NTE / R2-NTE | Non utilisées |
| .4 – .49 | Adresses fixes (imprimantes, bornes, switchs, serveurs) | Idem |
| .50 – .99 | Réserve | Réserve |
| .100 – .250 | Plage DHCP | Plage DHCP |

**DHCP** : assuré par les routeurs de chaque site (VyOS), pour rester autonome en cas de coupure
du lien vers le datacenter.

**Exception** : le VLAN 666 (quarantaine) ne peut pas suivre la règle, un octet ne dépassant
pas 255. Il utilise `10.<site>.66.0/24`.

**Adresses publiques** : le réseau étant fictif, toutes les adresses publiques sont prises dans
les plages réservées à la documentation (RFC 5737).

## Catalogue des VLAN

| VLAN | Nom | Rôle | Nantes | Saint-Nazaire | Rennes | Local 11 |
|---|---|---|---|---|---|---|
| 10 | DIRECTION | Direction | ✔ | | | |
| 20 | COMPTA | Compta, RH | ✔ | | | |
| 30 | VENTE | Vendeurs, caisses, atelier | ✔ | ✔ | ✔ | |
| 40 | ECOMMERCE | Équipe e-commerce | ✔ | | | |
| 50 | VOIP | Téléphones IP | ✔ | ✔ | ✔ | |
| 60 | IOT | Capteurs d'humidité de l'atelier | | ✔ | | |
| 70 | INVITES | Studio d'essai, élèves, Wi-Fi invités | ✔ | ✔ | ✔ | |
| 80 | IMPRESSION | Imprimantes | ✔ | | | |
| 99 | MGMT | Administration des équipements | ✔ | ✔ | ✔ | ✔ |
| 110 | SERVEURS | Serveurs internes | | | | ✔ |
| 120 | DMZ | Boutique en ligne | | | | ✔ |
| 130 | ADMIN-SI | Supervision, RADIUS, automatisation | | | | ✔ |
| 666 | QUARANTAINE | Appareils refusés par le 802.1X | ✔ | ✔ | ✔ | |

## Siège – Nantes (site 1)

Routeurs **R1-NTE** et **R2-NTE** en VRRP : chaque passerelle `.1` est une adresse virtuelle.

| VLAN | Nom | Sous-réseau | Passerelle (VRRP) | R1 / R2 | DHCP |
|---|---|---|---|---|---|
| 10 | DIRECTION | 10.1.10.0/24 | 10.1.10.1 | .2 / .3 | .100 – .250 |
| 20 | COMPTA | 10.1.20.0/24 | 10.1.20.1 | .2 / .3 | .100 – .250 |
| 30 | VENTE | 10.1.30.0/24 | 10.1.30.1 | .2 / .3 | .100 – .250 |
| 40 | ECOMMERCE | 10.1.40.0/24 | 10.1.40.1 | .2 / .3 | .100 – .250 |
| 50 | VOIP | 10.1.50.0/24 | 10.1.50.1 | .2 / .3 | .100 – .250 |
| 70 | INVITES | 10.1.70.0/24 | 10.1.70.1 | .2 / .3 | .100 – .250 |
| 80 | IMPRESSION | 10.1.80.0/24 | 10.1.80.1 | .2 / .3 | Adresses fixes |
| 99 | MGMT | 10.1.99.0/24 | 10.1.99.1 | .2 / .3 | Adresses fixes |
| 666 | QUARANTAINE | 10.1.66.0/24 | 10.1.66.1 | .2 / .3 | .100 – .250 |

**Adresses fixes de management** :

| Équipement | Adresse |
|---|---|
| SW-CORE-NTE (switch de cœur) | 10.1.99.10 |
| SW-ACC1-NTE (accès magasin) | 10.1.99.11 |
| SW-ACC2-NTE (accès bureaux) | 10.1.99.12 |

## Agence – Saint-Nazaire (site 2)

Un seul routeur, **R-SNZ** : il porte la passerelle `.1` de chaque VLAN.

| VLAN | Nom | Sous-réseau | Passerelle | DHCP |
|---|---|---|---|---|
| 30 | VENTE | 10.2.30.0/24 | 10.2.30.1 | .100 – .250 |
| 50 | VOIP | 10.2.50.0/24 | 10.2.50.1 | .100 – .250 |
| 60 | IOT | 10.2.60.0/24 | 10.2.60.1 | Adresses fixes |
| 70 | INVITES | 10.2.70.0/24 | 10.2.70.1 | .100 – .250 |
| 99 | MGMT | 10.2.99.0/24 | 10.2.99.1 | Adresses fixes |
| 666 | QUARANTAINE | 10.2.66.0/24 | 10.2.66.1 | .100 – .250 |

**Adresses fixes** :

| Équipement | Adresse |
|---|---|
| SW-SNZ (switch d'accès) | 10.2.99.10 |
| CAPTEUR-HUM-01 / 02 / 03 | 10.2.60.10 / .11 / .12 |

## Agence – Rennes (site 3)

Un seul routeur, **R-REN** : il porte la passerelle `.1` de chaque VLAN.

| VLAN | Nom | Sous-réseau | Passerelle | DHCP |
|---|---|---|---|---|
| 30 | VENTE | 10.3.30.0/24 | 10.3.30.1 | .100 – .250 |
| 50 | VOIP | 10.3.50.0/24 | 10.3.50.1 | .100 – .250 |
| 70 | INVITES | 10.3.70.0/24 | 10.3.70.1 | .100 – .250 |
| 99 | MGMT | 10.3.99.0/24 | 10.3.99.1 | Adresses fixes |
| 666 | QUARANTAINE | 10.3.66.0/24 | 10.3.66.1 | .100 – .250 |

**Adresses fixes** :

| Équipement | Adresse |
|---|---|
| SW-REN (switch d'accès) | 10.3.99.10 |

## Datacenter – Local 11 (site 10)

Le routeur **R-DC** porte les passerelles des VLAN internes. La DMZ a pour passerelle le firewall **FW-DC**.

| VLAN | Nom | Sous-réseau | Passerelle | Adressage |
|---|---|---|---|---|
| 99 | MGMT | 10.10.99.0/24 | 10.10.99.1 (R-DC) | Fixe |
| 110 | SERVEURS | 10.10.110.0/24 | 10.10.110.1 (R-DC) | Fixe |
| 120 | DMZ | 10.10.120.0/24 | 10.10.120.1 (FW-DC) | Fixe |
| 130 | ADMIN-SI | 10.10.130.0/24 | 10.10.130.1 (R-DC) | Fixe |

**Serveurs** :

| Serveur | Rôle | Adresse |
|---|---|---|
| SRV-DNS | DNS interne | 10.10.110.10 |
| SRV-ERP | Gestion des stocks et des caisses | 10.10.110.20 |
| SRV-FICHIERS | Partage de fichiers | 10.10.110.30 |
| WEB-BOUTIQUE | Boutique en ligne (DMZ) | 10.10.120.10 |
| PROXY-INVERSE | Reverse proxy de la boutique (DMZ) | 10.10.120.11 |
| SRV-RADIUS | Authentification 802.1X | 10.10.130.10 |
| SRV-SUPERVISION | Zabbix ou LibreNMS | 10.10.130.20 |
| SRV-ANSIBLE | Automatisation | 10.10.130.30 |
| SW-DC (switch du datacenter) | Management | 10.10.99.10 |
| FW-DC (interface de management) | Management | 10.10.99.254 |

## Nomades – VPN SSL

| Usage | Sous-réseau | Passerelle |
|---|---|---|
| Pool d'adresses des techniciens itinérants | 10.250.0.0/24 | FW-DC |

## Infrastructure

### Loopbacks (identifiants OSPF et BGP)

| Équipement | Loopback |
|---|---|
| R1-NTE | 10.255.1.1/32 |
| R2-NTE | 10.255.1.2/32 |
| R-SNZ | 10.255.2.1/32 |
| R-REN | 10.255.3.1/32 |
| R-DC | 10.255.10.1/32 |
| FW-DC | 10.255.10.254/32 |

### Liens point à point (/30)

Le troisième octet indique le site concerné : 1 = interne à Nantes, 10 = vers le Local 11.

| Lien | Sous-réseau | Côté A | Côté B |
|---|---|---|---|
| R1-NTE ↔ R2-NTE (interconnexion) | 10.254.1.0/30 | R1-NTE : 10.254.1.1 | R2-NTE : 10.254.1.2 |
| R1-NTE ↔ R-DC | 10.254.10.0/30 | R1-NTE : 10.254.10.1 | R-DC : 10.254.10.2 |
| R2-NTE ↔ R-DC | 10.254.10.4/30 | R2-NTE : 10.254.10.5 | R-DC : 10.254.10.6 |
| R-DC ↔ FW-DC | 10.254.10.8/30 | R-DC : 10.254.10.9 | FW-DC : 10.254.10.10 |

### Tunnels IPsec (interfaces VTI, /30)

Le troisième octet reprend le code de l'agence.

| Tunnel | Sous-réseau | Côté FW-DC | Côté agence |
|---|---|---|---|
| FW-DC ↔ R-SNZ | 10.254.2.0/30 | 10.254.2.1 | 10.254.2.2 |
| FW-DC ↔ R-REN | 10.254.3.0/30 | 10.254.3.1 | 10.254.3.2 |

### Accès Internet et BGP

| Système autonome | Numéro d'AS |
|---|---|
| Larsen & Médiator | 65100 |
| Distortion Télécom (FAI 1) | 65001 |
| Unplugged Networks (FAI 2) | 65002 |

| Lien | Sous-réseau | Côté FAI | Côté client |
|---|---|---|---|
| FW-DC ↔ Distortion Télécom | 203.0.113.0/30 | 203.0.113.1 | FW-DC : 203.0.113.2 |
| FW-DC ↔ Unplugged Networks | 198.51.100.0/30 | 198.51.100.1 | FW-DC : 198.51.100.2 |
| R-SNZ ↔ Distortion Télécom | 203.0.113.4/30 | 203.0.113.5 | R-SNZ : 203.0.113.6 |
| R-REN ↔ Unplugged Networks | 198.51.100.4/30 | 198.51.100.5 | R-REN : 198.51.100.6 |

**Préfixe public de Larsen & Médiator**, annoncé en BGP aux deux FAI : `192.0.2.0/24`.

| Service publié | Adresse publique | Adresse interne (NAT) |
|---|---|---|
| Boutique en ligne | 192.0.2.10 | 10.10.120.11 (PROXY-INVERSE) |
| Passerelle VPN SSL | 192.0.2.20 | FW-DC |
| Sortie Internet des utilisateurs (NAT) | 192.0.2.1 | Tous les VLAN internes |

## Historique des versions

| Version | Date | Modification |
|---|---|---|
| 1.0 | Octobre 2026 | Création du plan d'adressage |
