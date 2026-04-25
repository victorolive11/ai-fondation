---
name: writing-engine
description: Use when a writing task arrives that needs strategic framing before production OR when the right marketingskills skill is unclear OR when the task isn't covered by a specialized marketingskills skill OR when brand voice/positioning consistency must be enforced across multiple pieces. Acts as a meta-orchestrator over marketingskills (cold-email, copywriting, page-cro, ad-creative, etc.). Does NOT produce content directly when a specialized skill exists.
version: 2.0.0
author: foundation
tags: [writing, meta, orchestration, marketing, brand, foundation]
status: active
supersedes: writing-engine v1.0.0 (archived)
ecosystem:
  upstream: [project-orchestrator]
  downstream:
    primary: [marketingskills]
    research: [research-engine]
    audit:    [audit-engine]
  uses_assets: [brand-voice.md, proof-bank.md, objection-bank.md]
---

# writing-engine v2

> **Rôle en une phrase** : Je suis le **chef d'orchestre du marketing**, pas un musicien. Je décide quel skill marketingskills joue, je lui donne le brief business + brand, je vérifie la cohérence inter-pièces. Je ne produis du contenu moi-même qu'en dernier recours.

---

## 0. Différence v1 → v2

**v1 (archivée)** essayait de couvrir 14+ registres en un seul skill. Devenu obsolète quand on a découvert marketingskills (33 skills spécialisés, 24k stars, mises à jour mensuelles, 197 evals).

**v2** :
1. **Ne produit plus directement** quand un skill marketingskills couvre le besoin.
2. **Apporte la couche brand + business** que marketingskills (générique) ne peut pas connaître.
3. **Maintient la cohérence inter-pièces** (un cold email, une landing et un thread doivent sonner ScoreDecision, pas SaaS générique).
4. **Fallback de production** uniquement pour les formats non couverts par marketingskills.

---

## 1. Déclencheurs d'activation

### J'active quand
- L'orchestrator route une demande de production marketing/copy.
- Un livrable mélange plusieurs registres (ex : « séquence email + post LinkedIn associé + landing »).
- Le skill marketingskills approprié n'est pas évident.
- Une cohérence brand/positionnement doit être enforced.
- Un asset projet (brand-voice.md, proof-bank.md) doit être créé ou mis à jour.

### Je n'active PAS quand
- Le skill marketingskills cible est évident et déjà nommé.
- Un format n'est pas couvert ET ne nécessite aucune cohérence brand (rare).
- Production code (→ superpowers).

### Règle de cession
Si un skill marketingskills spécialisé peut faire le job, **je passe la main après avoir injecté le brand context**. Je ne re-traite pas ce qu'il fait mieux.

---

## 2. Cartographie marketingskills

Voici ce que je connais de marketingskills (catégorisé par usage). Je route vers le skill spécialisé selon la demande.

### Production de copy
| Demande | Skill marketingskills |
|---|---|
| Cold email B2B | `cold-email` |
| Copy général (homepage, headlines) | `copywriting` |
| Édition / révision copy | `copy-editing` |
| Email sequences (welcome, nurture) | (chercher dans marketingskills, sinon fallback) |

### Pages et conversion
| Demande | Skill |
|---|---|
| Landing page / page de vente | `page-cro` |
| Architecture site / nav | `site-architecture` |
| Compétiteurs alternatives pages | `competitor-alternatives` |

### Ads et créatifs
| Demande | Skill |
|---|---|
| Ad creative (headlines, descs, primary text) | `ad-creative` |
| App Store / Google Play listings | `aso-audit` |

### Growth et expérimentation
| Demande | Skill |
|---|---|
| A/B test setup | `ab-test-setup` |
| Customer research | `customer-research` |
| Competitor profiling | `competitor-profiling` |
| Content strategy | `content-strategy` |

### Rétention et lifecycle
| Demande | Skill |
|---|---|
| Churn / cancel flows / dunning | `churn-prevention` |
| RevOps / lifecycle / scoring | `revops` |
| Sales enablement (decks, one-pagers) | `sales-enablement` |
| Community marketing | `community-marketing` |

### SEO
| Demande | Skill |
|---|---|
| SEO classique | `seo-audit` (à confirmer présence) |
| AI SEO (AEO/GEO/LLMO) | `ai-seo` |

### Analytics
| Demande | Skill |
|---|---|
| GA4 / tracking / mesure | `analytics-tracking` |

**Note** : la liste évolue. Avant de router, vérifier la version installée :
```
ls ~/.claude/skills/marketingskills/skills/
```

---

## 3. Mon vrai job — la couche brand

Quand un skill marketingskills exécute, il a **besoin de contexte projet** que j'injecte :

### Inputs que je fournis au skill cible

```markdown
## CONTEXT INJECTION (par writing-engine v2)

### Brand voice (pointeur ScoreDecision/brand-voice.md ou inline si absent)
- Ton dominant : [direct / authority / soft / agressif / executive / premium]
- Ton secondaire : [si combinaison]
- Vocabulaire : do/don't list
- Références culturelles : [autorisées / interdites]

### Positionnement
- Promesse unique : [1 ligne]
- Différenciation : [vs concurrents identifiés]
- Cibles primaires : [par persona]
- Cibles à éviter : [explicite]

### Munitions disponibles
- Proof points : pointeur proof-bank.md
- Cas client : pointeur ou inline
- Chiffres validés : pointeur (si research-engine fait, indiquer ID)
- Garanties : [liste]

### Objections récurrentes
- Pointeur objection-bank.md
- Top 3 objections de la cible : [liste]

### Contraintes éthiques/légales
- Claims interdits : [liste]
- Mentions obligatoires : [si applicables]
- Disclaimer requis : [oui/non + exemple]

### Cohérence inter-pièces
- Pièces existantes pour cette campagne : [liens/IDs]
- Niveau de cohérence requis : [strict / souple / standalone]
```

Sans cette injection, marketingskills produit du copy générique. **C'est ma valeur ajoutée**.

---

## 4. Workflow standard

```
1. Réception brief depuis orchestrator
2. Identification du skill marketingskills cible
   ├── Couvert → continuer
   └── Non couvert → mode fallback (§6)
3. Vérification assets projet
   ├── brand-voice.md existe ? → charger
   ├── proof-bank.md existe ? → charger
   └── objection-bank.md existe ? → charger
   Si l'un manque ET nécessaire → escalade création asset (§7)
4. Vérification besoin research factuel
   ├── Claims chiffrés requis ? → research-engine d'abord (séquentiel)
   └── Aucun chiffre → continuer direct
5. Construction du brief enrichi (§3 context injection)
6. Handoff au skill marketingskills cible
7. Réception output
8. Vérification cohérence brand (lecture rapide, pas re-écriture)
9. Si destiné à publication externe → handoff audit-engine
10. Retour orchestrator avec livrable + métadonnées
```

---

## 5. Cas où je passe la main directement (sans intervention)

Pour rester léger, je passe la main **sans rien faire** quand :

- L'orchestrator a déjà cadré objectif business + cible + DoD.
- Le skill cible est évident.
- Aucun asset brand n'est requis (ex : brouillon interne, pas de cohérence externe).
- L'utilisateur a explicitement nommé le skill marketingskills.

Dans ces cas je laisse une trace minimale :
```
✍️ writing-engine v2 → pass-through vers [skill]
   Brand context : non requis
   Trace : WE-[YYYYMMDD]-[short]
```

---

## 6. Mode fallback — production directe

J'écris moi-même **uniquement** quand :

1. Aucun skill marketingskills ne couvre le format demandé.
2. ET le format est documenté dans mon référentiel (§8).
3. ET aucun autre skill custom (research-engine, audit-engine) n'est pertinent.

### Formats fallback documentés

| Format | Pourquoi pas dans marketingskills | Quand utiliser |
|---|---|---|
| Note interne stratégique / brief | Pas marketing externe | Communications internes équipe |
| Réponse support sensible / escalade | Format situationnel | Réclamation, crise, escalade RH |
| Synthèse exécutive / TL;DR dirigeant | Format spécifique au lecteur | Brief CEO, board update |
| Pitch investisseur (deck non couvert) | Si sales-enablement absent | Pitch oral ou one-pager |
| Telegram/Discord communauté privée | Format underserved | Communautés natives |

Pour ces formats, j'applique les règles d'écriture documentées en §9.

### Refus de fallback
Si un format n'est ni couvert par marketingskills ni dans ma liste fallback :
```
🚦 STOP — format non couvert
Motif : aucun skill spécialisé, aucun template fallback validé.
Options :
  A) Créer un skill custom dédié (proposer un nom et brief)
  B) Approximer avec [skill marketingskills le plus proche]
  C) Production manuelle one-shot, pas réutilisable
Ma reco : [contextuelle]
```

---

## 7. Création d'assets projet

### brand-voice.md
Quand un projet n'a pas de brand-voice et qu'une pièce externe est demandée, je propose la création **avant production**. Format proposé :

```markdown
# Brand Voice — [Projet]

## 1. Identité
- Mission en 1 phrase :
- Promesse unique :
- Pour qui (persona primaire) :
- Pas pour qui (anti-persona) :

## 2. Ton
- Ton dominant : [voir §5 v1 archive ou registres standards]
- Ton secondaire :
- Combinaisons interdites :

## 3. Vocabulaire
### Do
- mots/expressions à privilégier

### Don't
- mots/expressions à éviter
- jargon corporate banni
- formules creuses bannies

## 4. Références culturelles
- Univers de référence (films, livres, marques)
- Codes implicites
- À éviter

## 5. Format types
- Email : ton + longueur cible
- Landing : structure + ton
- Social : voix + format

## 6. Exemples canoniques
- 1 email exemple "réussi"
- 1 post social exemple
- 1 paragraphe landing exemple

## 7. Anti-exemples
- 1 exemple de copy qui sonne FAUX pour cette marque
```

**Règle** : la création prend ~30 min en interview/itération. Je la facilite, l'utilisateur valide. C'est un asset critique, pas optionnel.

### proof-bank.md / objection-bank.md
Mêmes principes, format minimal :

```markdown
# Proof Bank — [Projet]

## Chiffres validés (avec source)
- [chiffre] — source : [lien] — vérifié le [date]

## Témoignages utilisables
- [verbatim] — auteur : [nom + contexte] — droit d'usage : [confirmé / à vérifier]

## Cas clients
- [cas] — client : [nom ou anonymisé] — résultats : [chiffrés]

## Garanties
- [garantie + conditions]
```

```markdown
# Objection Bank — [Projet]

## Cible : [persona]

### Objection : "[verbatim de l'objection]"
- Pourquoi elle revient :
- Réponse validée (testée) :
- À éviter dans la réponse :
```

---

## 8. Anti-patterns

| Anti-pattern | Pourquoi mauvais | À la place |
|---|---|---|
| **Refaire le travail de marketingskills** | Doublon, qualité moindre | Router et injecter brand |
| **Produire sans brand-voice quand externe** | Copy générique | Créer brand-voice avant |
| **Activer en pass-through inutilement** | Friction sans valeur | Si pass-through évident, juste laisser passer |
| **Charger 3 skills marketingskills d'un coup** | Pollution | 1 skill principal, séquencer si plusieurs nécessaires |
| **Ignorer claims chiffrés non sourcés** | Risque audit BLOCK | research-engine en amont |
| **Style "rédaction scolaire"** dans fallback | Médiocre | Règles §9 |

---

## 9. Règles d'écriture (mode fallback uniquement)

Quand je produis directement (rare), j'applique :

- **Première ligne = travail maximum**. Décide de la lecture.
- **Specific beats generic**. Chiffres précis, références concrètes.
- **Verbes d'action > nominalisations**.
- **Phrases courtes alternées avec une longue**. Rythme.
- **Un seul message par paragraphe**.
- **CTA = 1 seul** quand applicable.
- **Pas de formules creuses** : interdits = « dans un monde où », « aujourd'hui plus que jamais », « solutions », « innovations », « synergies », « disruptif ».
- **Pas de superlatifs non prouvés** : sauf preuve immédiate.
- **Test du concurrent** : remplacer le nom de marque par concurrent → si ça marche encore, trop générique.
- **Test de suppression** : chaque mot supprimable est supprimé.

Je passe le résultat par audit-engine si destiné à publication externe.

---

## 10. Format de handoff (vers marketingskills)

```markdown
## BRIEF → marketingskills:[skill-name]

### Contexte (5 lignes)
[minimum nécessaire]

### Objectif business
[1 phrase, le pourquoi business]

### Cible
- Profil : [rôle, contexte, maturité]
- État mental : [sceptique / intéressé / bloqué]
- Top objection : [la vraie]
- Action attendue : [mesurable]

### Brand context (injecté par writing-engine v2)
- brand-voice.md : [pointeur ou résumé inline]
- Ton : [dominant + secondaire]
- Vocabulaire critique : [3-5 mots à utiliser, 3-5 à éviter]

### Munitions
- Proofs : [pointeur proof-bank.md ou inline]
- Garanties : [liste]
- Chiffres sourcés (research-engine ID) : [si applicable]

### Format attendu
- Longueur : [exacte]
- Support : [email / landing / post / etc.]
- Contraintes : [interdits, obligatoires]

### Definition of Done
- [ ] Critère 1
- [ ] Critère 2
- [ ] Critère 3

### Audit prévu
[Oui/Non — si publication externe]

### Handoff suivant
[audit-engine si externe / retour orchestrator si interne]
```

---

## 11. Cohérence inter-pièces (campagne multi-livrables)

Pour une campagne avec plusieurs pièces (ex : cold email → landing → séquence nurture) :

1. **Définir une fiche de campagne** au début, partagée par tous les skills :
   - Cible unique
   - Promesse unique
   - 3 proof points consistants
   - Vocabulaire campagne (mots-clés, formules-signature)
   - Tons par pièce (peuvent varier mais cohérence d'ensemble)

2. **Routing séquentiel obligatoire** : ne pas router 3 skills en parallèle pour 3 pièces de la même campagne.

3. **Audit final inter-pièces** : audit-engine reçoit la campagne complète, pas chaque pièce isolée.

4. **Trace partagée** : ID campagne (CAMP-YYYYMMDD-XX) référencé dans chaque pièce.

---

## 12. Versioning

Pour les pièces produites :
- **v0.x** : drafts exploration
- **v1.0** : version proposée pour audit
- **v1.x** : révisions post-audit
- **v2.0** : refonte majeure / changement angle

Pour les assets projet (brand-voice.md, etc.) :
- Versionnés via git si dans repo projet
- Sinon : section "changelog" en bas de fichier

---

## 13. Principes non négociables

1. **Marketingskills d'abord.** Je ne refais pas leur boulot.
2. **Brand-voice avant production externe.** Pas de copy générique pour un projet sérieux.
3. **Research avant claims chiffrés.** Toujours.
4. **1 skill cible par tâche.** Pas de chargement multiple.
5. **Cohérence inter-pièces** pour les campagnes.
6. **Audit avant publication externe.** Toujours.
7. **Fallback rare.** Si je produis souvent moi-même, c'est qu'il manque un skill — proposer sa création.
8. **L'objectif business prime.**
9. **Je suis un wrapper, je reste léger.**
10. **Chaque pièce produite est traçable** (ID + brand-voice version + research IDs si applicable).

---

*Fin du SKILL.md — writing-engine v2.0.0*
