# Gérard & sa guitare SAS - Scénario client
> *«  »*

## Le client

**Gérard & sa guitare** est un magasin de guitares indépendant fondé à Nantes en 1994, au pied du château des ducs de Bretagne. Il vend des guitares neuves et vintage, des amplis et des pédales d'effets, et possède son propre atelier de lutherie. Depuis 2020, la boutique en ligne représente 40 % du chiffre d'affaires.


| Site | Lieu | Utilisateurs | Particularité |
|---|---|---|---|
| Siège et magasin principal | Nantes, quartier Bouffay | ~60 | Direction, compta, vente, studio d'essai insonorisé |
| Agence 1 | Saint-Nazaire | ~12 | Magasin et atelier de lutherie |
| Agence 2 | Rennes | ~10 | Magasin, cours de guitare, location de matériel |
| Datacenter | Rezé | – | Surnommé « le Local 11 », à côté d'une salle de répétition : les serveurs vibrent en rythme le jeudi soir |

**Fournisseurs d'accès (fictifs)** :
- **Distortion Télécom** : un débit énorme, mais qui grésille dès qu'on le pousse un peu.
- **Unplugged Networks** : lent, sobre, mais il ne lâche jamais. La version acoustique d'Internet.

## Les personnages

- **Gérard Honoré, PDG** : ancien guitariste classique de renommée internationale et luthier sur son temps libre.
- **Sandrine, responsable e-commerce** : gère la boutique en ligne et redoute chaque grand festival de l'été, quand les commandes explosent.
- **Bertrand, maître luthier** (Saint-Nazaire) : a installé des capteurs connectés pour surveiller l'humidité de l'atelier, car le bois des guitares n'aime pas ça. Ils sont branchés sur le même réseau que la caisse.
- **Enzo, vendeur et stagiaire** : branche son pédalier « connecté » et sa box perso dans le studio d'essai « pour streamer ses démos ».
- **Les techniciens itinérants** : vont régler le son des concerts et des salles partenaires, et ont besoin d'accéder au stock depuis la route.
- **Patrick, le seul informaticien** : guitariste de jazz à ses heures, il préfère l'improvisation à la documentation. Les mots de passe sont notés au dos d'une affiche de concert.
- **Moi** : ingénieur intégrateur chez *NetRiff Intégration*, appelé pour accorder tout ça.

## L'incident déclencheur

Le vendredi du plus gros week-end de festival de l'été, à 18 h, **tout le réseau est tombé** : un câble Ethernet branché entre deux prises du studio d'essai a créé une boucle réseau. La boutique en ligne est restée muette 5 heures, 1 500 commandes ont été perdues, et les caisses des trois magasins ont dû passer au carnet papier.

Gérard a déclaré, très calmement : *« Plus jamais de larsen sur le réseau. »*

## Le cahier des charges

| Étape | Demande du client | Réponse technique |
|---|---|---|
| 1 – LAN du siège | « Plus jamais de vendredi noir. Et que la box d'Enzo arrête de jouer les serveurs DHCP. » | VLAN par service, STP maîtrisé, VRRP, DHCP snooping, port-security |
| 2 – Routage et WAN | « Quand Distortion sature, on bascule sur Unplugged sans que les clients ne l'entendent. » | OSPF entre les sites, BGP multi-FAI avec bascule automatique |
| 3 – Firewall | « La boutique en ligne doit tourner jour et nuit, mais si elle se fait pirater, la compta et les caisses ne doivent pas suivre. » | Firewall OPNsense, DMZ, matrice de flux |
| 4 – VPN | « Saint-Nazaire et Rennes doivent voir le stock en temps réel, et les techniciens réserver du matériel depuis la route. » | IPsec site-à-site, VPN SSL nomade |
| 5 – Contrôle d'accès | « Un appareil inconnu branché dans le studio d'essai ne doit accéder à rien. Et les capteurs de Bertrand n'ont rien à faire à côté de la caisse. » | 802.1X, VLAN invité, VLAN IoT isolé, quarantaine |
| 6 – Supervision | « Je veux entendre la fausse note avant que Jimmy ne la remarque. » (Patrick) | Zabbix ou LibreNMS, alertes, tableau de bord |
| 7 – Automatisation | « L'affiche de concert avec les mots de passe a été vendue sur une brocante. » | Ansible, sauvegardes automatiques, configurations versionnées |
| 8 – Audit | « Prouvez-moi qu'Enzo ne peut plus rien casser, même en jouant fort. » | Tests d'attaque depuis Kali, rapport avant/après |

---
