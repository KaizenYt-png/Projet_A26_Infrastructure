# Matrice des flux / pare-feu

Politique par défaut : **DROP sur INPUT, FORWARD et OUTPUT** (moindre privilège).
Seuls les flux explicitement listés ci-dessous sont autorisés.

## Légende
- 🟢 Autorisé
- 🔴 Refusé (explicite ou par défaut)
- 🟡 Limité (rate-limited ou restreint par IP)

## Flux entrants depuis Internet (WAN → DMZ)

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| Internet | routeur-pare-feu | 443 | TCP | 🟢 | HTTPS public (reverse proxy) |
| Internet | routeur-pare-feu | 80 | TCP | 🟢 | Redirection HTTP → HTTPS |
| Internet | routeur-pare-feu | * | * | 🔴 | Tout le reste fermé |

## Flux DMZ → LAN

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| docker-host | ldap-samba | 389 | TCP | 🟡 | LDAP (authentification app) |
| docker-host | ldap-samba | 636 | TCP | 🟡 | LDAPS (préféré) |
| docker-host | dns-dhcp | 53 | UDP/TCP | 🟢 | Résolution DNS |
| docker-host | LAN | * | * | 🔴 | Aucun autre accès |

## Flux LAN → DMZ

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| LAN | docker-host | * | * | 🔴 | Le LAN ne remonte pas vers la DMZ |

## Flux Gestion → Toutes zones

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| bastion | DMZ (toutes VMs) | 22 | TCP | 🟡 | SSH admin (clé Ed25519 uniquement) |
| bastion | LAN (toutes VMs) | 22 | TCP | 🟡 | SSH admin |
| bastion | Gestion (toutes VMs) | 22 | TCP | 🟡 | SSH admin |
| monitoring | DMZ | 9100 | TCP | 🟢 | Node Exporter scrape |
| monitoring | LAN | 9100 | TCP | 🟢 | Node Exporter scrape |
| monitoring | Gestion | 9100 | TCP | 🟢 | Node Exporter scrape |
| monitoring | docker-host | 8080 | TCP | 🟢 | cAdvisor (métriques Docker) |
| monitoring | ldap-samba | 389 | TCP | 🟡 | LDAP exporter |
| monitoring | toutes zones | * | * | 🔴 | Aucun autre port |

## Flux Toutes zones → Gestion

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| DMZ | monitoring | * | * | 🔴 | Pas de retour vers monitoring |
| LAN | monitoring | * | * | 🔴 | Idem |
| Toutes | bastion | * | * | 🔴 | Aucune connexion entrante au bastion sauf SSH admin |

## Flux sortants (Internet)

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| bastion | Internet | 443 | TCP | 🟢 | `apt update`, git clone |
| bastion | Internet | 80 | TCP | 🟢 | Miroirs HTTP |
| toutes autres VMs | Internet | * | * | 🔴 | Aucun accès sortant direct |

## Flux intra-LAN

| Source | Destination | Port | Proto | État | Justification |
|---|---|---|---|---|---|
| Postes LAN | dns-dhcp | 53 | UDP/TCP | 🟢 | DNS |
| Postes LAN | dns-dhcp | 67/68 | UDP | 🟢 | DHCP |
| Postes LAN | ldap-samba | 389/636 | TCP | 🟢 | Auth LDAP |
| Postes LAN | ldap-samba | 445 | TCP | 🟢 | Samba (CIFS/SMB) |
| Postes LAN | ldap-samba | 631 | TCP | 🟢 | IPP (CUPS) |
| Postes LAN | ldap-samba | 139 | TCP | 🔴 | NetBIOS désactivé (obsolète) |

## Règles de mise en œuvre (nftables)

1. Table `inet filter` avec 3 chaînes : `input`, `forward`, `output`
2. Politique par défaut : `drop` sur les 3 chaînes
3. Conntrack activé : `ct state established,related accept` en tête
4. Logs sur DROP : `log prefix "nft-drop: "` (rate-limited)
5. Rate limiting sur SSH : `limit rate 3/minute` (anti-bruteforce, en complément de Fail2ban)
6. Règles commentées dans le fichier de config (traçabilité audit)
