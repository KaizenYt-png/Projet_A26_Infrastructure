# Architecture réseau — EduTech Campus

## 1. Zones réseau

| Zone | Sous-réseau | Passerelle | Rôle |
|---|---|---|---|
| DMZ | 10.10.10.0/24 | 10.10.10.1 | Nginx TLS, Docker host |
| LAN | 10.10.20.0/24 | 10.10.20.1 | Samba, CUPS, LDAP, DNS/DHCP |
| Gestion | 10.10.30.0/24 | 10.10.30.1 | Bastion, monitoring |

## 2. Plan d'adressage IP

### Infrastructure

| Hôte | Zone | IP | Passerelle | Rôle |
|---|---|---|---|---|
| routeur-pare-feu | Inter-zone | 10.10.10.1 / 10.10.20.1 / 10.10.30.1 | — | nftables, routage |
| bastion | Gestion | 10.10.30.10 | 10.10.30.1 | SSH Ed25519 + Fail2ban |
| monitoring | Gestion | 10.10.30.30 | 10.10.30.1 | Prometheus + Grafana |
| dns-dhcp | LAN | 10.10.20.10 | 10.10.20.1 | BIND9 + DHCP |
| ldap-samba | LAN | 10.10.20.20 | 10.10.20.1 | OpenLDAP + Samba + CUPS |
| docker-host | DMZ | 10.10.10.20 | 10.10.10.1 | Nginx + stack applicative |

### Plages DHCP (LAN)

| Plage | Usage |
|---|---|
| 10.10.20.100 – 10.10.20.200 | Postes étudiants/enseignants |
| 10.10.20.10 – 10.10.20.99 | Réservations statiques (serveurs) |

### Noms DNS (`edutech.local`)

| FQDN | IP |
|---|---|
| bastion.edutech.local | 10.10.30.10 |
| monitoring.edutech.local | 10.10.30.30 |
| dns.edutech.local | 10.10.20.10 |
| ldap.edutech.local | 10.10.20.20 |
| samba.edutech.local | 10.10.20.20 |
| cups.edutech.local | 10.10.20.20 |
| portal.edutech.local | 10.10.10.20 |
| api.edutech.local | 10.10.10.20 |

## 3. Schéma logique

## 4. VMs VirtualBox

| VM | vCPU | RAM | Disque | NICs | Réseaux |
|---|---|---|---|---|---|
| routeur-pare-feu | 2 | 1 Go | 15 Go | 3 | dmz, lan, mgmt |
| bastion | 1 | 1 Go | 10 Go | 2 | mgmt + NAT |
| services-lan | 2 | 3 Go | 30 Go | 1 | lan |
| docker-host | 2 | 3 Go | 30 Go | 1 | dmz |
| monitoring | 2 | 2 Go | 20 Go | 1 | mgmt |

**Total alloué : ~10 Go / 16 Go disponibles.**

Réseaux VirtualBox :
- `vboxnet-dmz` (Internal Network) → 10.10.10.0/24
- `vboxnet-lan` (Internal Network) → 10.10.20.0/24
- `vboxnet-mgmt` (Internal Network) → 10.10.30.0/24
- NAT (défaut) sur le bastion uniquement → accès Internet

## 5. Justification des choix

- **5 VMs sur 16 Go** : permet une vraie séparation des rôles sans saturer l'hôte.
- **Routeur dédié** : indispensable pour simuler le cloisonnement et tester nftables sur 3 zones.
- **Bastion isolé en Gestion** : applique le principe zéro confiance — aucun accès direct aux zones DMZ/LAN.
- **Services LAN regroupés** : LDAP/Samba/CUPS partagent l'annuaire, la colocation est logique.
- **Pas de NIC NAT sur les VMs de service** : elles ne sortent pas sur Internet (philosophie zéro confiance). Seul le bastion a un accès sortant, pour les mises à jour.

## 6. Schéma (à produire)

À insérer :  (généré via draw.io ou Excalidraw à partir du schéma ASCII ci-dessus)
