# Architecture réseau — EduTech Campus

## Zones
| Zone | Sous-réseau | Rôle |
|---|---|---|
| DMZ | 10.10.10.0/24 | Nginx TLS, Docker host |
| LAN | 10.10.20.0/24 | Samba, CUPS, postes |
| Gestion | 10.10.30.0/24 | Bastion, monitoring, LDAP |

## Plan d'adressage (à compléter)
| Hôte | Zone | IP | Rôle |
|---|---|---|---|
| routeur-pare-feu | — | 10.10.10.1 / 10.10.20.1 / 10.10.30.1 | nftables, routage |
| bastion | Gestion | 10.10.30.10 | SSH Ed25519 + Fail2ban |
| dns-dhcp | LAN | 10.10.20.10 | BIND9 + DHCP |
| ldap-samba | LAN | 10.10.20.20 | OpenLDAP + Samba + CUPS |
| docker-host | DMZ | 10.10.10.20 | Nginx + stack applicative |
| monitoring | Gestion | 10.10.30.30 | Prometheus + Grafana |

## Schéma
(à insérer — docs/schema-reseau.png)
