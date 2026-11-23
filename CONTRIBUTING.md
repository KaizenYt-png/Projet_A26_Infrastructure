# Conventions de collaboration

## Branches
- `main` : état stable, toujours fonctionnel
- `feat/<sujet>` : nouvelle fonctionnalité
- `fix/<sujet>` : correction
- `docs/<sujet>` : documentation

## Messages de commit — Conventional Commits
`<type>(<scope>): <description à l'impératif>`

Types : feat, fix, docs, chore, refactor, test, ci

Exemples :
- feat(nftables): ajouter règles DMZ -> LAN sur port 443
- docs(readme): ajouter matrice des flux
- fix(bind): corriger zone inverse edutech.local

## Workflow
1. Créer une branche depuis `main`
2. Commits atomiques et réguliers
3. Ouvrir une Pull Request, l'autre coéquipier relit et approuve
4. Merger dans `main` après validation

## Règle d'or
Chaque coéquipier doit avoir des commits sur tous les piliers
dans l'historique (les rôles sont des spécialisations, pas des silos).

## Répartition officielle du binôme

### Admin Réseau & Identité — Malick
Périmètre : IP, nftables, DNS/DHCP, OpenLDAP, PAM/SSSD, Samba, CUPS
Branches typiques : feat/nftables-*, feat/bind-*, feat/ldap-*, feat/samba-*

### Ingénieur DevSecOps & Cloud — Scott
Périmètre : Docker Compose, Nginx TLS, Bash, sauvegardes, Prometheus/Grafana
Branches typiques : feat/docker-*, feat/nginx-*, feat/backup-*, feat/monitoring-*

## Règle des PR croisées
Chaque PR doit être relue et approuvée par l'autre coéquipier avant merge.
Objectif : maintenir une traçabilité équilibrée sur l'ensemble des piliers.
