# Plan d'adressage IP 



## Objectif

Ce document définit l'adressage IP et les VLAN de l'ensemble du réseau de Larsen & Médiator.
Il sert de référence pour la configuration des équipements, le filtrage sur le firewall
et le dépannage.

## Périmètre

- Siège et magasin principal – Nantes
- Agence de Saint-Nazaire (magasin et atelier de lutherie)
- Agence de Rennes
- Datacenter « Local 11 » – Rezé
- Infrastructure : liens inter-sites, loopbacks, accès aux deux FAI

  ## Principes retenus

**Plage globale** : 10.0.0.0/8 (adressage privé, RFC 1918).

**Logique d'adressage** : `10.<site>.<VLAN>.0/24`, pour qu'une adresse indique d'elle-même
son site et son VLAN.

| Code site | Site |
|---|---|
| 1 | Nantes |
| 2 | Saint-Nazaire |
| 3 | Rennes |
| 10 | Local 11 |
| 254 | Liens point à point |
| 255 | Loopbacks |

**VLAN harmonisés** : un même numéro de VLAN a le même rôle sur tous les sites.

**Taille des sous-réseaux** : /24 pour tous les VLAN utilisateurs, par souci de lisibilité
(l'espace privé rend le gaspillage sans conséquence). Masques serrés pour l'infrastructure :
/30 pour les liens point à point, /32 pour les loopbacks.

**Conventions dans chaque sous-réseau** :

| Adresses | Usage |
|---|---|
| .1 | Passerelle virtuelle (VRRP) |
| .2 / .3 | Routeurs physiques R1 / R2 |
| .4 – .49 | Adresses fixes (imprimantes, bornes, équipements) |
| .50 – .99 | Réserve |
| .100 – .250 | Plage DHCP |

**Exception** : le VLAN 666 (quarantaine) ne peut pas suivre la règle, un octet ne dépassant
pas 255. Il utilise `10.<site>.66.0/24`.

**Accès Internet** : les adresses publiques des liens FAI sont prises dans les plages
réservées à la documentation (RFC 5737), puisque le réseau est fictif.
