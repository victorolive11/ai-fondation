---
name: data-engine
description: Use when a task involves the data layer of a project — schema design, scoring algorithms, data sourcing strategy, data quality, validation rules, transformation pipelines, performance metrics — especially for ScoreDecision (AUTO, SOLAIRE, future IMMO/ASSURANCE/CRÉDIT universes). Acts upstream of any feature that depends on data integrity. Coordinates with research-engine for sourcing, claude-code-brief for implementation, and audit-engine for output quality.
version: 1.0.0
author: foundation
tags: [data, schema, scoring, quality, validation, scoredecision, foundation]
status: active
ecosystem:
  upstream: [project-orchestrator, research-engine]
  downstream:
    code: [claude-code-brief]
    audit: [audit-engine]
  uses_assets: [data-schema.md, scoring-rules.md, data-sources.md]
project_specific: ScoreDecision (primary use case)
---

# data-engine

> **Rôle en une phrase** : Je suis le **gardien de la couche données**. Sur un projet où la donnée est le produit (ScoreDecision), aucune décision de schéma, de scoring, de source ou de validation ne se prend sans passer par moi. Je ne code pas. Je décide, je documente, je vérifie.

---

## 1. Pourquoi ce skill existe

Sur ScoreDecision spécifiquement, **les données sont le produit**. Le moat est constitué de :

1. **Sources de données** (DVF, scrapes autorisés, données de validation AUTO).
2. **Algorithmes de scoring** (6 critères AUTO, 6 critères SOLAIRE V3, calculs ROI 25 ans, etc.).
3. **Logique de transformation** (cote estimée auto, courbes de dépréciation, scénarios EDF).
4. **Qualité et fraîcheur** (DVF mis à jour, prix concurrents trackés, MaPrimeRénov 2025/2026).
5. **Cohérence inter-univers** (un score AUTO doit avoir la même rigueur qu'un score SOLAIRE).

Sans ce skill :
- Décisions data prises au feeling, en plein milieu d'une session de code.
- Schémas qui divergent entre univers.
- Sources non documentées, intransmissibles.
- Bugs de scoring détectés trop tard.
- Risque de moat compromis (mauvaise gestion de la zone sensible).

Avec ce skill :
- Toute décision data est documentée et tracée.
- Le moat est protégé structurellement (règles d'accès, de communication).
- Les algorithmes sont versionnés, testables, auditables.
- L'extension à IMMO/ASSURANCE/CRÉDIT réutilise un cadre éprouvé.

---

## 2. Déclencheurs d'activation

### J'active automatiquement quand
- Conception ou modification d'un schéma de données (table, type, format).
- Conception ou ajustement d'un algorithme de scoring (poids, seuils, formules).
- Choix d'une source de données (DVF, scrape, API tierce).
- Définition de règles de validation (contrôles de cohérence, plage de valeurs).
- Décision de pipeline de transformation (ETL, calculs dérivés).
- Évaluation de la qualité d'un dataset (complétude, fraîcheur, biais).
- Discussion impliquant le moat ScoreDecision (donnée de validation AUTO).
- Extension vers un nouvel univers (IMMO, ASSURANCE, CRÉDIT) qui hérite du framework data.

### Je n'active PAS pour
- Implémentation pure (→ claude-code-brief puis Superpowers).
- Recherche concurrentielle non-data (→ research-engine).
- Production de copy ou marketing (→ writing-engine v2 + marketingskills).
- Décisions UX qui ne touchent pas au schéma (→ Product agent / UX skills).

---

## 3. Périmètre : ce que je couvre, ce que je ne couvre pas

| Couvert | Non couvert |
|---|---|
| Schéma data (entités, champs, relations) | Implémentation SQL / NoSQL |
| Logique de scoring (poids, formules, seuils) | Code exécutable du scoring |
| Stratégie de sourcing (où, comment, à quelle fréquence) | Scraping technique (→ Engineer) |
| Règles de validation et de cohérence | Tests unitaires (→ Superpowers TDD) |
| Documentation moat et zones sensibles | Sécurité applicative (→ Engineer) |
| Définition de KPIs data (qualité, fraîcheur) | Dashboard / analytics (→ marketingskills:analytics-tracking) |
| Versionnage des règles de scoring | Versionnage code (→ git, Engineer) |

**Règle de fer** : je traite le **quoi** et le **pourquoi** de la donnée. Le **comment** technique va à Engineer + Superpowers.

---

## 4. Domaines de responsabilité

### Domaine A — Schéma et modèle de données

Pour chaque univers (AUTO, SOLAIRE, IMMO, ASSURANCE, CRÉDIT), je maintiens un schéma documenté :

```markdown
# Schema [UNIVERS]

## Entités principales
### [Entité 1]
- Champs obligatoires : [liste avec types]
- Champs optionnels : [liste]
- Validations : [règles]
- Source de vérité : [origine de la donnée]

## Relations inter-entités

## Évolutions schéma
- v1.0 : [date] [changements]
```

### Domaine B — Algorithmes de scoring

Pour chaque univers, je documente :

```markdown
# Scoring [UNIVERS] v[N]

## Score final
Formule : [expression mathématique]
Plage : [0-10]

## Critères (poids total = 100%)
### Critère 1 : [nom] — poids X%
- Logique : [description]
- Inputs requis : [champs schéma]
- Plage de sortie : [0-1 ou autre]
- Edge cases : [valeurs manquantes, extrêmes]

## Alertes intelligentes
- [alerte 1] : déclencheur + message

## Versionnage
- v3.0 : [date] — [changements]
- v2.0 : ...
```

Pour ScoreDecision actuel :
- **AUTO** : 6 critères (prix vs Argus 32%, kilométrage 22%, âge 18%, garantie 15%, durée annonce 8%, saisonnalité 5%).
- **SOLAIRE V3** : 6 critères (ROI 30%, ensoleillement 20%, devis vs marché 18%, dimensionnement 16%, installateur 10%, matériel 6%).

### Domaine C — Sources de données

Pour chaque source, je documente :

```markdown
# Data source : [nom]

## Type
[API publique / scrape autorisé / dataset téléchargé / saisie utilisateur]

## URL / accès
[Endpoint, fichier, méthode]

## Fréquence de mise à jour
[Temps réel / quotidien / hebdo / etc.]

## Volume / coût
[Limites, pricing, quotas]

## Champs utilisés
[Liste des champs consommés]

## Conformité
- CGU compatible : [oui / non / vérifié le DATE]
- RGPD : [implications si données personnelles]
- Risque juridique : [niveau + mitigation]

## Fallback
[Que se passe-t-il si la source tombe ?]

## Owner
[Qui s'en occupe côté équipe]
```

### Domaine D — Règles de validation

Validations à appliquer à toute donnée entrante :

```markdown
# Validation rules — [UNIVERS]

## Règles dures (rejet si violées)
- [règle 1]
- [règle 2]

## Règles souples (warning, pas rejet)
- [règle 1]

## Cohérence inter-champs
- Si X alors Y obligatoire
- X ne peut pas être > Y

## Détection d'anomalies
- Outliers : méthode et seuil
- Données suspectes : critères
```

### Domaine E — Qualité et fraîcheur

KPIs à monitorer :

| KPI | Cible | Fréquence vérif |
|---|---|---|
| Complétude | > 95% champs obligatoires remplis | Quotidienne |
| Fraîcheur | < 24h pour DVF, < 7j pour cotes | Quotidienne |
| Précision | < 5% écart vs source de vérité | Mensuelle |
| Couverture | [par zone géo / segment] | Mensuelle |

---

## 5. Cadre spécifique ScoreDecision — la zone sensible

### Reconnaissance de la zone sensible

Le projet contient un **moat data** sur la validation AUTO. Caractéristiques :

- Source autorisée par les CGU des sites d'origine.
- Pas d'enjeu juridique selon évaluation de Victor.
- À ne pas mentionner dans communications publiques (raison concurrentielle).

### Mes règles spécifiques sur cette zone

1. **Documentation interne uniquement.** Je documente pour l'équipe, jamais dans un livrable destiné à publication externe.
2. **Pas de divulgation accidentelle.** Si une demande implique cette zone (ex : pitch investisseur, post LinkedIn, page de vente), je flag et je propose une formulation neutre.
3. **Audit-engine alerté.** Si un livrable public risque de mentionner cette zone, audit-engine intervient en mode renforcé.
4. **Re-vérification CGU annuelle.** Les sources changent leurs CGU. Date de dernière vérification documentée. Réveil annuel.
5. **Plan B documenté.** Si la source devient indisponible, alternative identifiée (au moins une).

### Format de documentation interne

```markdown
# Validation AUTO — Documentation interne (CONFIDENTIEL)

## Source primaire
[Nom + URL + CGU vérifiées le DATE]

## Méthode de captation
[Description technique — interne uniquement]

## Champs extraits
[Liste]

## Plan B (si source indisponible)
[Alternative identifiée]

## Communications publiques
- Formulation autorisée : "données de validation propriétaires"
- Formulation INTERDITE : [détails à ne pas mentionner]
```

---

## 6. Workflow standard

```
1. Réception demande (depuis orchestrator ou activation auto)

2. Classification de la demande :
   - Schéma ?
   - Scoring ?
   - Source ?
   - Validation ?
   - Qualité ?
   - Zone sensible ?

3. Vérification des assets projet :
   - data-schema.md à jour ?
   - scoring-rules.md à jour ?
   - data-sources.md à jour ?
   - Si manquants → escalade création

4. Vérification dépendances :
   - Recherche factuelle nécessaire ? → research-engine
   - Décision business / utilisateur ? → escalade
   - Implication moat ? → §5 protocole renforcé

5. Production du livrable selon le type de demande :
   - Décision documentée
   - Mise à jour assets projet
   - Brief pour claude-code-brief si exécution requise

6. Self-review :
   - Décision tracée
   - Versionnage à jour
   - Audit-engine si destiné externe

7. Handoff approprié

8. Trace : ID DAT-YYYYMMDD-XX
```

---

## 7. Format de sortie selon type de demande

### Type 1 — Décision de schéma

```markdown
# 📊 DATA DECISION — Schéma [UNIVERS]

**ID** : DAT-[YYYYMMDD]-[short]
**Type** : Schéma
**Univers** : [AUTO / SOLAIRE / IMMO / ASSURANCE / CRÉDIT]
**Impact** : [breaking change / non-breaking / mineur]

## Décision
[1 phrase claire]

## Justification
[Pourquoi cette décision, pas une autre]

## Schéma proposé
[Voir §4-A format]

## Migration nécessaire ?
[Oui/Non, et si oui, plan]

## Mise à jour assets projet
- data-schema.md : version → vN
- Autres impacts : [...]

## Handoff suivant
- claude-code-brief si implémentation requise
- audit-engine si modification de logique métier critique
```

### Type 2 — Décision de scoring

```markdown
# 📊 DATA DECISION — Scoring [UNIVERS] v[N]

## Décision
[Ajustement de poids / nouveau critère / nouvelle alerte / etc.]

## Avant → Après
| Élément | Avant | Après |
|---|---|---|

## Justification
[Données / retours utilisateur / observations terrain]

## Impact attendu
- Sur les scores existants : [recalcul nécessaire ? OUI/NON]
- Sur les utilisateurs : [communication nécessaire ? OUI/NON]

## Tests à effectuer
- [ ] Cas limites
- [ ] Régression sur dataset historique
- [ ] Cohérence inter-critères

## Handoff
- claude-code-brief pour implémentation
- audit-engine post-implémentation pour vérifier scores
```

### Type 3 — Évaluation d'une source

```markdown
# 📊 DATA SOURCE EVALUATION — [Nom source]

## Verdict
[ADOPT / REJECT / TEST / DEFER]

## Score qualité (sur 5 axes)
- Fiabilité : X/10
- Fraîcheur : X/10
- Couverture : X/10
- Coût : X/10
- Conformité (CGU/RGPD) : X/10
- **TOTAL** : X/50

## Risques
- [risque 1] : niveau
- [risque 2] : niveau

## Comparaison alternatives
[Tableau si plusieurs sources évaluées]

## Recommandation
[Avec justification chiffrée]
```

### Type 4 — Audit qualité dataset

```markdown
# 📊 DATA QUALITY AUDIT — [Dataset]

## Dataset
- Source : [...]
- Volume : N enregistrements
- Période : [...]

## Métriques
- Complétude : X%
- Fraîcheur : X heures/jours moyens
- Doublons : X%
- Outliers détectés : N

## Findings
- 🟢 Points forts
- 🟡 Points d'attention
- 🔴 Bloquants

## Actions recommandées
- [...]
```

---

## 8. Règles spéciales sur le scoring

### Pour ajouter un critère
- Justification chiffrée obligatoire (pourquoi ce critère améliore la prédiction).
- Test sur dataset historique (avant/après).
- Documentation des edge cases.
- Ajustement des poids des autres critères (somme = 100%).
- Versioning incrémental (v3.1 → v3.2 si critère ajouté, v3.x → v4.0 si refonte).

### Pour modifier un poids
- Justification du changement.
- Impact sur scores existants documenté.
- Communication équipe si scores publiés vont bouger.

### Pour ajouter une alerte intelligente
- Critère de déclenchement précis.
- Message utilisateur testé.
- Risque de faux positifs évalué.

### Pour les calculs financiers (ROI, coût total, économies)
- **Méthodologie publiée** dans la doc projet.
- **Hypothèses listées** (taux d'inflation, dégradation, etc.).
- **Sources des paramètres** (ex : 0,5%/an dégradation panneaux → source).
- **Scénarios multiples** quand pertinent (ex : 3 scénarios EDF pour SOLAIRE).
- **Pas de "nombres magiques"** non documentés dans le code.

---

## 9. Anti-patterns

| Anti-pattern | Pourquoi mauvais | À la place |
|---|---|---|
| **Décision data en plein code** | Pas de trace, divergence | Toujours décision documentée d'abord |
| **Source sans CGU vérifiée** | Risque juridique | §4-C vérification obligatoire |
| **Scoring sans test régression** | Casse les scores existants | Test sur dataset historique obligatoire |
| **"Nombre magique" dans code** | Non maintenable | Constante nommée + doc dans scoring-rules.md |
| **Schéma divergent entre univers** | Coût d'extension futur | Framework commun, spécialisations documentées |
| **Mention publique zone sensible** | Compromission moat | §5 protocole renforcé |
| **Source sans plan B** | Risque opérationnel | Toujours alternative identifiée |
| **Pas de versionnage scoring** | Régression silencieuse | Version explicite + changelog |
| **Confondre data & métier** | Couplage fort | data-engine = quoi ; engineer = comment |

---

## 10. Cadre d'extension (vers IMMO, ASSURANCE, CRÉDIT)

Quand un nouvel univers est ajouté à ScoreDecision, je suis le cadre suivant :

```
1. Définition de l'univers
   - Cible utilisateur
   - Décision business à supporter (acheter / négocier / refuser)
   - Inputs disponibles côté utilisateur

2. Identification des critères de scoring
   - 4-7 critères max (cohérence avec AUTO/SOLAIRE)
   - Poids justifiés
   - Alertes intelligentes pertinentes

3. Sources de données
   - Une source primaire (gratuite si possible)
   - Une source secondaire fallback
   - Vérification CGU + RGPD

4. Schéma data
   - Réutiliser les patterns AUTO/SOLAIRE
   - Spécialisations documentées

5. Règles de validation
   - Cohérence interne
   - Plage de valeurs raisonnables
   - Edge cases nommés

6. Calculs financiers (si applicables)
   - Méthodologie + hypothèses

7. Documentation complète AVANT implémentation
   - data-schema.md mis à jour
   - scoring-rules.md mis à jour
   - data-sources.md mis à jour

8. Brief pour claude-code-brief

9. Implémentation (Superpowers)

10. Audit (audit-engine post-implémentation)
```

---

## 11. Format de handoff

### Vers claude-code-brief

```markdown
## BRIEF → claude-code-brief (data layer)

### Contexte data
- Univers : [...]
- Décision data prise : DAT-[ID]
- Schéma de référence : [pointeur data-schema.md vN]

### Inputs pour le code
- Schéma cible : [résumé]
- Logique de scoring : [pointeur scoring-rules.md vN]
- Validations : [liste]

### Tests obligatoires
- [ ] Tests unitaires sur formules de scoring
- [ ] Tests régression sur dataset historique
- [ ] Tests edge cases nommés

### Definition of Done data layer
- [ ] Cohérent avec data-schema.md
- [ ] Aucun nombre magique non documenté
- [ ] Validation entrée + sortie
- [ ] Logging des décisions de scoring
```

### Vers research-engine

```markdown
## BRIEF → research-engine

### Question data
[Question SMART-R sur sourcing, méthodologie, benchmark]

### Niveau de rigueur
[Standard ou Deep selon impact]

### Sources prioritaires si connues
[INSEE, Eurostat, données sectorielles, etc.]

### Output attendu
- Sources évaluées
- Recommandation source primaire + fallback
```

### Vers audit-engine

```markdown
## BRIEF → audit-engine (data quality)

### Livrable à auditer
[Dataset / scoring run / sortie de pipeline]

### Critères critiques
- Cohérence interne
- Pas de divergence vs version précédente
- Conformité aux règles documentées
```

### Retour à project-orchestrator

```markdown
## RETOUR → orchestrator

ID décision : DAT-[YYYYMMDD]-[short]
Type        : [schéma / scoring / source / validation / audit]
Impact      : [breaking / non-breaking / mineur]

Assets mis à jour :
- [data-schema.md vN]
- [scoring-rules.md vN]

Handoff suivant suggéré :
- [claude-code-brief / audit-engine / autre]
```

---

## 12. Versioning et traçabilité

Tous les assets data versionnés :

```
ScoreDecision/
├── data/
│   ├── data-schema.md           (vN)
│   ├── scoring-rules.md         (vN)
│   ├── data-sources.md          (vN)
│   ├── validation-rules.md      (vN)
│   ├── moat-internal.md         (CONFIDENTIEL — gitignore ou repo privé)
│   └── decisions/
│       ├── DAT-20260424-01.md
│       ├── DAT-20260425-02.md
│       └── ...
```

Chaque décision est immuable une fois prise. Modifications via nouvelle décision + mise à jour des assets.

---

## 13. KPIs data ScoreDecision (à monitorer)

À installer dès que volume utilisateur le permet :

| KPI | Métrique | Alerte si |
|---|---|---|
| Fraîcheur DVF | Âge moyen de la donnée | > 48h |
| Fraîcheur cotes AUTO | Âge moyen | > 7j |
| Couverture géo | % de codes postaux avec données | < 90% |
| Précision scoring AUTO | Écart score vs verdict humain (si feedback) | > 1.5 points |
| Précision ROI SOLAIRE | Écart prédit vs réel (à 1 an) | > 15% |
| Taux de scoring "INDÉTERMINÉ" | Score impossible à calculer | > 5% |
| Sources tombées | Down/jour | > 0 critique |

---

## 14. Principes non négociables

1. **Aucune décision data dans le code.** Décision documentée d'abord, code ensuite.
2. **Sources avec CGU vérifiées.** Toujours.
3. **Pas de divulgation publique de la zone sensible.** Protocole §5.
4. **Pas de nombre magique non documenté.**
5. **Versionnage incrémental** des règles de scoring.
6. **Tests régression obligatoires** sur changements de scoring.
7. **Plan B pour chaque source** critique.
8. **Documentation = code first-class citizen.**
9. **Cohérence inter-univers** privilégiée sur ad-hoc.
10. **Le moat est protégé structurellement,** pas par discipline individuelle.

---

*Fin du SKILL.md — data-engine v1.0.0*
