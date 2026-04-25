---
name: claude-code-brief
description: Use when the user needs to delegate a coding task to Claude Code (terminal/IDE) and the brief must be cadred upstream (business intent, definition of done, risks, files concerned) BEFORE Superpowers takes over the methodology (brainstorm → spec → plan → TDD → execute). Produces a clean handoff document that the user pastes into Claude Code, ensuring Superpowers starts with full business context. Does NOT replace Superpowers — it feeds it.
version: 1.0.0
author: foundation
tags: [claude-code, dev, brief, handoff, foundation]
status: active
ecosystem:
  upstream: [project-orchestrator, research-engine, writing-engine]
  downstream:
    methodology: [superpowers]
  uses_assets: [project-context.md, technical-decisions.md]
---

# claude-code-brief

> **Rôle en une phrase** : Je transforme une intention business + un contexte projet en un **brief technique propre** que Claude Code peut consommer immédiatement, et que Superpowers va exécuter sans avoir à reposer 10 questions.

---

## 1. Pourquoi ce skill existe

Sans ce skill, voici ce qui se passe en pratique :

1. Utilisateur tape « ajoute un système de scoring serveur » dans Claude Code.
2. Superpowers active `brainstorming` → pose 5-15 questions de clarification.
3. L'utilisateur répond au feeling, perd 20 minutes.
4. Superpowers produit un spec partiellement aligné avec l'objectif business.
5. Implémentation propre côté technique mais boiteuse côté produit.

Avec ce skill :

1. Le brief contient déjà : objectif business, contraintes, fichiers concernés, definition of done, risques.
2. Superpowers démarre avec 80% du cadrage déjà fait.
3. `brainstorming` ne pose que les questions techniques restantes (vraiment techniques).
4. Implémentation alignée business-tech dès la première itération.

**C'est un skill de gain de temps et d'alignement, pas un orchestrateur.**

---

## 2. Déclencheurs d'activation

### J'active quand
- L'orchestrator route une tâche dev qui doit être déléguée à Claude Code (pas exécutable dans le chat actuel).
- Une feature, un refactor, un bugfix, une migration ou un déploiement nécessite intervention dans un repo.
- Plusieurs fichiers/modules sont concernés et le contexte doit être structuré.
- Le briefing implique des contraintes business non-évidentes pour un dev générique.

### Je n'active PAS quand
- La tâche est triviale (1 fichier, < 20 lignes de code à modifier).
- L'utilisateur est déjà dans Claude Code et Superpowers a démarré.
- C'est du code à coller dans un artifact (Claude Code n'est pas le bon environnement).
- C'est de la recherche technique sans implémentation (→ research-engine).

### Règle de cession
Une fois mon brief produit, je sors du chemin. Je ne suis ni Superpowers, ni l'auteur du code. Je suis le rédacteur du cahier des charges.

---

## 3. Anti-doublon avec Superpowers

| Phase | Qui décide | Mon rôle |
|---|---|---|
| Cadrage business amont | claude-code-brief | **Je produis le brief** |
| Brainstorming spec | superpowers:brainstorming | Mon brief est l'input |
| Plan technique | superpowers:writing-plans | Référence à mon brief |
| Décomposition tâches | superpowers:writing-plans | Référence à mon brief |
| Exécution TDD | superpowers:executing-plans / subagent-driven-development | Mon brief reste accessible |
| Verification | superpowers:verification-before-completion | Vérifie contre ma DoD |

**Règle absolue** : je ne fais jamais le travail de spec, de plan, ou de code. Je fournis l'amont et je laisse Superpowers exécuter sa méthodo.

---

## 4. Anatomie d'un brief Claude Code propre

Un brief produit par ce skill suit cette structure stricte :

```markdown
# 🛠️ BRIEF CLAUDE CODE — [Titre tâche]

**ID brief**     : CCB-[YYYYMMDD]-[short]
**Projet**       : [nom du repo / projet]
**Type**         : [feature / refactor / bugfix / migration / deployment / other]
**Priorité**     : [haute / moyenne / basse]
**Effort estimé** : [petit < 2h / moyen 2-8h / grand > 1j]

---

## 1. Objectif business

[1 phrase. Pourquoi cette tâche existe du point de vue produit/business.
Pas "implémenter X" mais "permettre à Y de Z pour que W".]

## 2. Outcome attendu

[Comment on saura que la tâche a réussi. Pas "code qui marche" — métriques 
business, comportement utilisateur, ou capacité technique débloquée.]

## 3. Contexte projet minimal

- Stack : [techs principales — React, Vercel, Claude API, Make, etc.]
- État actuel : [ce qui existe aujourd'hui, en 3 lignes]
- État cible : [ce qui doit exister après, en 3 lignes]

## 4. Fichiers concernés (best estimate)

### À créer
- [chemin/fichier.ext] — [rôle]

### À modifier
- [chemin/fichier.ext] — [nature de la modification]

### À ne pas toucher
- [chemin/fichier.ext] — [raison]

## 5. Contraintes dures

### Techniques
- [contrainte stack, perf, compatibilité, etc.]

### Business
- [contrainte produit, légale, brand, sécurité]

### Non-objectifs
- [ce qui n'est PAS demandé — important pour éviter scope creep]

## 6. Definition of Done

### Fonctionnel
- [ ] [Critère vérifiable 1]
- [ ] [Critère vérifiable 2]

### Tests
- [ ] [Test unitaire / intégration / e2e attendu]
- [ ] [Couverture cible si applicable]

### Qualité
- [ ] Pas de secrets hardcodés
- [ ] Gestion d'erreurs sur tous les points d'entrée
- [ ] Documentation inline pour la logique non triviale
- [ ] [Autres critères qualité spécifiques projet]

### Performance
- [ ] [Cible perf si applicable]

## 7. Risques identifiés

| Risque | Niveau | Mitigation suggérée |
|---|---|---|
| [risque 1] | [haut/moyen/bas] | [stratégie] |
| [risque 2] | | |

## 8. Décisions techniques pré-cadrées

[Décisions déjà prises par l'utilisateur, à NE PAS rebrainstormer.]

- [Décision 1] : choisi parce que [raison]
- [Décision 2] : choisi parce que [raison]

## 9. Décisions ouvertes (à brainstormer avec Superpowers)

[Questions techniques laissées au discernement de Superpowers + utilisateur 
en session Claude Code.]

- [Question ouverte 1]
- [Question ouverte 2]

## 10. Inputs disponibles

- Spec / design doc : [lien ou pointeur]
- API / docs externes : [liens]
- Recherche préalable : [research-engine ID si applicable]
- Brand assets : [pointeurs si applicable — copy à intégrer, etc.]

## 11. Sortie attendue côté Superpowers

À la fin de la session Claude Code, Superpowers doit avoir produit :
- [ ] Code commité avec message clair
- [ ] Tests passants
- [ ] Spec saved (docs/superpowers/specs/...) — c'est le défaut Superpowers
- [ ] Plan d'implémentation saved — défaut Superpowers
- [ ] [Autre artefact spécifique projet : changelog, migration script, etc.]

## 12. Handoff post-exécution

Après merge :
- [ ] Validation manuelle utilisateur
- [ ] [Action de déploiement / annonce / etc.]
- [ ] Retour à project-orchestrator pour suite (si pipeline)

---

**Note pour Claude Code** : démarre avec `superpowers:brainstorming` 
pour clarifier les décisions ouvertes (§9), puis enchaîne 
`writing-plans` → `executing-plans`. Toutes les décisions de §8 
sont arrêtées et ne doivent pas être rebrainstormées.
```

---

## 5. Workflow standard

```
1. Réception demande depuis orchestrator
   (intent EXECUTE-DEV, scope > triviale)
2. Vérification context projet
   ├── project-context.md existe ? → charger
   └── Sinon : me cadrer rapidement avec l'utilisateur
3. Vérification dépendances de cadrage
   ├── Recherche technique préalable nécessaire ?
   │   └── Oui → research-engine d'abord (séquentiel)
   ├── Décisions business/produit nécessaires ?
   │   └── Oui → escalade utilisateur avant de produire le brief
   └── Brand assets nécessaires (copy intégré) ?
       └── Oui → writing-engine v2 d'abord (pour produire le copy)
4. Production du brief selon §4
5. Self-review (checklist §6)
6. Livraison du brief en artifact (markdown copiable)
7. Instructions claires à l'utilisateur :
   "Copie ce brief dans Claude Code, démarre une session, 
    Superpowers prendra le relais."
8. Trace : ID CCB-YYYYMMDD-XX
```

---

## 6. Self-review avant livraison

Avant de produire le brief final, je vérifie :

- [ ] L'objectif business est en 1 phrase et porte un "pourquoi", pas un "quoi".
- [ ] L'outcome attendu est mesurable, pas "ça marche".
- [ ] Les fichiers concernés sont nommés (best estimate, pas exhaustif).
- [ ] La DoD a au moins 3 critères vérifiables.
- [ ] Les risques sont nommés et triés par niveau.
- [ ] Les décisions pré-cadrées (§8) sont distinctes des décisions ouvertes (§9).
- [ ] Le brief tient en moins de 2 pages écran (sinon = trop, scope à découper).
- [ ] Aucune mention de "implémentation détaillée" — c'est le job de Superpowers.

Si l'un manque, je retourne à l'utilisateur pour compléter avant de produire.

---

## 7. Cas spéciaux

### Cas A — Tâche cross-projets
Ex : « migrer auth de tous nos projets de JWT maison vers Auth0 »

```
Décomposition obligatoire :
- 1 brief par projet (CCB séparés)
- Référencement croisé entre briefs
- Ordre d'exécution proposé
- Identification des dépendances inter-projets
```

### Cas B — Tâche urgente / hotfix
Ex : « production down, fix immédiat »

```
Brief allégé :
- §1 Objectif business (1 ligne)
- §4 Fichiers concernés (best estimate)
- §6 DoD minimale (3 critères max)
- §11 Sortie : "fix + post-mortem"
- Skip §7-9 (pas le moment)
- Marker : URGENT en en-tête
```

### Cas C — Spike / exploration technique
Ex : « regarde si on peut intégrer DVF en streaming »

```
Brief de type spike :
- Outcome = rapport de faisabilité, pas code production
- DoD = décisions architecturales documentées
- Pas de §11 sortie code
- Routing post-spike : research-engine ou nouveau brief CCB
```

### Cas D — Décision technique majeure non tranchée
Ex : « refactor complet de notre couche de scoring »

```
Refus de brief en l'état :
"Décision architecturale majeure non tranchée. 
 Avant brief Claude Code, j'ai besoin de :
 - Décision de stack cible (langage, framework, hébergement)
 - Stratégie de migration (big bang vs strangler fig)
 - Critères de succès business (pas tech)
 
 Options :
   A) Session de cadrage avec utilisateur (1h)
   B) Routage vers research-engine pour benchmark approches
   C) Spike d'abord (cas C ci-dessus)"
```

### Cas E — Tâche déjà en cours côté Claude Code
Ex : « complète ce qu'on avait commencé sur le module X »

```
Brief de reprise :
- §3 État actuel = état réel après dernière session (à confirmer)
- Charger spec et plan existants (docs/superpowers/specs/...)
- §8 Décisions pré-cadrées = ce qui a déjà été décidé
- §9 Décisions ouvertes = ce qui restait
- Marker : REPRISE en en-tête
- Référence à la session précédente
```

---

## 8. Anti-patterns

| Anti-pattern | Pourquoi mauvais | À la place |
|---|---|---|
| **Faire le travail de Superpowers** (brainstormer dans le brief) | Doublon | Cadrer business, laisser superpowers brainstormer technique |
| **Brief trop long** (> 2 pages) | Pas digestible | Découper en plusieurs briefs |
| **Brief trop court** (< 1 page) | Cadrage insuffisant | Compléter §6 risques + §7 DoD |
| **Décrire l'implémentation** | Force la main de Superpowers | Décrire le quoi, pas le comment |
| **Mélanger tâches indépendantes** | Scope creep | 1 brief = 1 outcome |
| **Pas de DoD mesurable** | Validation au feeling | DoD = critères vérifiables |
| **Oublier les non-objectifs** | Scope creep en exécution | §5 explicite |
| **Pré-cadrer toutes les décisions** | Tue le bénéfice de brainstorming | Distinguer décidé / ouvert |

---

## 9. Format de sortie

Le brief est livré dans un **artifact markdown** dédié (ou fichier `.md` si en Claude Code).

L'utilisateur peut :
- Copier le markdown
- L'ouvrir dans son IDE
- Le pousser dans `.claude-code-briefs/` du repo projet (recommandé pour traçabilité)
- Le coller dans la première interaction Claude Code

**Suggestion d'arborescence projet** :

```
my-project/
├── .claude-code-briefs/
│   ├── CCB-20260424-01-add-scoring-engine.md
│   ├── CCB-20260425-02-integrate-dvf.md
│   └── ...
├── docs/
│   └── superpowers/specs/    ← géré par Superpowers
├── src/
└── ...
```

---

## 10. Format de handoff

### Vers utilisateur (instructions de transfert)

```
🛠️ Brief Claude Code prêt

ID : CCB-[YYYYMMDD]-[short]
Fichier : [titre artifact]

Pour exécuter :
1. Copie le contenu du brief
2. Lance Claude Code dans le repo : `cd <projet> && claude`
3. Colle ce brief comme premier message
4. Superpowers va prendre le relais avec brainstorming → writing-plans → executing-plans
5. Les décisions §8 sont arrêtées, ne te laisse pas rebrainstormer dessus

Estimation : [petit / moyen / grand]
Sortie attendue : voir §11 du brief
```

### Vers project-orchestrator (post-livraison)

```markdown
## RETOUR → orchestrator

ID brief : CCB-[YYYYMMDD]-[short]
État    : Livré, prêt pour Claude Code
Effort  : [estimation]
Risques majeurs : [top 1-2]

Suite suggérée :
- Exécution Claude Code par utilisateur
- Re-routage post-merge si pipeline (ex : déploiement, annonce, etc.)
```

---

## 11. Interface avec les autres skills

### Avec project-orchestrator v2
- Je reçois les routings EXECUTE-DEV.
- Je retourne avec brief + métadonnées.

### Avec research-engine
- Je peux le déclencher en amont si recherche technique nécessaire (ex : benchmark frameworks, approches state-of-the-art).
- Son output (RES-ID) est référencé dans §10 Inputs.

### Avec writing-engine v2
- Je le déclenche en amont si le brief implique du copy à intégrer (ex : emails transactionnels avec wording final).
- Son output est référencé dans §10 Inputs.

### Avec audit-engine
- Si la tâche concerne du code engageant en production, je peux suggérer un audit du brief avant Claude Code (rare).
- Plus typiquement : audit-engine intervient post-merge sur le code livré (review).

### Avec superpowers (en aval)
- Mon brief est l'input de leur workflow.
- Je laisse §8 arrêtées (ne pas rebrainstormer) et §9 ouvertes (à brainstormer).
- Superpowers va probablement créer un fichier `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` qui référence mon brief.

### Avec data-engine (custom)
- Si la tâche dev manipule la donnée critique projet (ex : scoring AUTO/SOLAIRE), data-engine vérifie d'abord la cohérence schéma/règles avant que je produise le brief.

---

## 12. Versioning des briefs

Briefs versionnés dans `.claude-code-briefs/` :
- **v1** : version initiale livrée à l'utilisateur
- **v1.x** : ajustements pré-exécution (ajout de risque identifié, etc.)
- **v2** : refonte si scope a significativement changé

Post-exécution, un brief peut être marqué :
- ✅ COMPLETED (link vers commit/PR)
- 🔄 PARTIAL (ce qui a été fait, ce qui reste)
- ❌ ABANDONED (raison)

---

## 13. Principes non négociables

1. **Je ne fais pas le travail de Superpowers.** Je le précède.
2. **Pas de brief sans objectif business.** Pas "implémenter X" comme objectif.
3. **DoD vérifiable.** Pas de "ça doit marcher".
4. **Distinguer décidé / à brainstormer.**
5. **1 brief = 1 outcome.** Pas de mix.
6. **Brief tient en 2 pages.** Sinon scope à découper.
7. **Risques nommés.** Toujours au moins 1.
8. **Inputs traçables.** Recherche, copy, design : référencés.
9. **Reprise = brief de reprise dédié,** pas un nouveau brief from scratch.
10. **Je sors du chemin** une fois le brief livré.

---

*Fin du SKILL.md — claude-code-brief v1.0.0*
