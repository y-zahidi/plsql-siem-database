# Cheatsheet — soutenance SIEM (1 page)

**À garder en main pendant la présentation. Plié en deux dans la poche.**

## Chronométrage (12 min total)
| # | Slide | Durée | Point clé |
|---|---|---|---|
| 1  | Cover           | 0:15 | "SIEM = système de centralisation alertes cybersécurité" |
| 2  | Plan            | 0:20 | Annoncer le déroulé |
| 3  | Problème        | 1:15 | 10 000+ alertes/jour, score auto, < 1 sec |
| 4  | Architecture    | 1:00 | PG = vérité ACID, Mongo = affichage rapide |
| 5  | 6 tables PG     | 1:00 | "INET, JSONB, pgcrypto" |
| 6  | Logique métier  | 1:00 | 6 séq, 6 fct, 4 trig, 5 proc, 4+1 vues, 16 idx |
| 7  | Trigger phare   | 1:30 | BEFORE = score · AFTER = incident · pg_notify |
| 8  | Doc Mongo 19    | 1:00 | sous-documents, 5 index |
| 9  | 7 requêtes      | 1:30 | numérique / AND-OR / regex / update / delete / agg / projection |
| 10 | Pipeline        | 1:00 | Insertion → détection → diffusion → affichage |
| 11 | Dashboard       | 0:30 | Bascule en démo après |
| -  | DÉMO LIVE       | 2:30 | dashboard → mongo express → CALL rapport |
| 12 | Conformité 20/20| 1:00 | "Tous les critères CDC sont remplis" |
| 13 | Difficultés     | 1:00 | choix modèle, ordre triggers, INET→Mongo, Docker WSL2 |
| 14 | Merci           | 0:15 | "Questions ?" |

## Chiffres à retenir
**6 / 6 / 6 / 4 / 5 / 5 / 16** — tables / séq / fct / trig / proc / vues / idx (PostgreSQL)
**19 champs / 350 docs / 5 index** — MongoDB
**Score 0–100  ·  Seuil incident = 5 critiques/h  ·  Latence < 1 s  ·  Note CDC = 20/20**

## Démo en 3 étapes (chronométrer ! max 3 min)

```
1. Onglet 1 — Streamlit (http://localhost:8501)
   ▸ "Voici 379 alertes 7 jours, 141 critical, 2 incidents auto"
   ▸ Filtre 24h → 1h "le dashboard se recalcule"

2. Onglet 2 — Mongo Express (http://localhost:8081)
   ▸ "Voici les documents enrichis avec sous-documents"

3. Onglet 3 — pgAdmin/DBeaver
   ▸ CALL rapport_risque_equipements();
   ▸ "boucle FOR avec curseur implicite"
```

## Top 5 questions probables — réponse en 1 phrase

| Q | Réponse en 1 phrase |
|---|---|
| Pourquoi 2 SGBD ? | PG = ACID pour incidents, Mongo = lecture rapide pour dashboard |
| Vue vs vue matérialisée | Vue = requête sauvegardée live, mat. = résultat stocké rafraîchi à la demande |
| Pourquoi INET ? | Validation auto IPv4/IPv6, opérateurs réseau natifs, plus compact |
| Index GIN sur JSONB ? | Recherche dans payload JSON en quelques ms au lieu de Seq Scan |
| LISTEN/NOTIFY ? | Pub/sub natif PG, dashboard MAJ en < 1 s sans polling |

## Si la démo plante
> *"Pour les besoins de la soutenance, j'utilise les captures du dashboard que j'ai prises hier soir. La logique est la même, je peux montrer le code juste après."*
> Bascule sur VS Code → `01_schema.sql`, `03_triggers.sql`, `08_mongodb_requetes.js`.

## Erreurs à éviter
- Ne pas dire "projet d'école"
- Ne pas lire les slides
- Ne pas faire de démo > 3 min
- Ne pas dire "j'ai utilisé l'IA"
- Si tu sais pas : *"je n'ai pas exploré, c'est une piste pour la v3"*

## Mot de la fin (si tu sens le prof attentif)
> *"Un vrai SIEM en entreprise fonctionne exactement comme ça, juste à plus grande échelle. C'est un sujet que j'aimerais approfondir en stage de fin d'année."*

---
**Bon courage. Tu connais ton sujet.**
