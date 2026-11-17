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
