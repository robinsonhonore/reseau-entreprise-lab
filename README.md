# reseau-entreprise-lab
Lab d’intégration réseau et sécurité : LAN, OSPF/BGP, firewall, VPN, 802.1X, supervision, automatisation

# Réseau d'entreprise sécurisé – Lab d'intégration

Conception, intégration, sécurisation et automatisation du réseau d'une PME fictive
(siège, 2 agences, datacenter, 2 FAI) dans un lab GNS3.

## Environnement
- GNS3 2.2.61 + GNS3 VM sur VMware Workstation Pro
- Routeurs VyOS, firewall OPNsense 26.7, switchs Open vSwitch, postes Alpine Linux

## Avancement
- [x] Étape 0 – Préparation du lab
- [ ] Étape 1 – LAN du siège (VLAN, STP, VRRP, durcissement L2)
- [ ] Étape 2 – Routage OSPF et BGP multi-FAI
- [ ] Étape 3 – Firewall, DMZ, matrice de flux
- [ ] Étape 4 – VPN IPsec site-à-site et VPN SSL
- [ ] Étape 5 – Contrôle d'accès 802.1X
- [ ] Étape 6 – Supervision
- [ ] Étape 7 – Automatisation
- [ ] Étape 8 – Audit attaque/défense

## Organisation du dépôt
- `docs/` : schémas, plan d'adressage, matrice de flux
- `configs/` : configurations des équipements, par étape
- `tests/` : cahier de recette et scripts de test
- `gns3/` : export du projet GNS3 (sans images)
