# Comparaison PostgreSQL vs MongoDB dans ce projet

> Ce document fait partie du rapport de soutenance.
> Il justifie pourquoi les deux SGBD sont **complémentaires** et non concurrents.

---

## 1. Pourquoi deux bases de données ?

Dans la vraie vie, un SIEM (Splunk, ELK, Wazuh) doit gérer deux exigences contradictoires :

| Exigence | Conséquence |
|---|---|
| **Garantir l'intégrité des règles, des incidents, des traces d'audit** | Besoin d'un SGBD relationnel ACID (PostgreSQL) |
| **Indexer/agréger des millions d'événements pour le dashboard** | Besoin d'un store rapide en lecture analytique (MongoDB) |

Aucun SGBD ne fait bien les deux. La solution est l'**architecture hybride** :

```
┌────────────┐  ETL/Streaming   ┌──────────────┐  Lecture rapide  ┌────────────┐
│ PostgreSQL │  ─────────────►  │   MongoDB    │  ──────────────► │ Dashboard  │
│ (master)   │                  │ (read-model) │                  │ Streamlit  │
└────────────┘                  └──────────────┘                  └────────────┘
   Source de vérité                Affichage / KPI                    UI Web
```

C'est exactement le pattern **CQRS** (Command/Query Responsibility Segregation).

---

## 2. Tableau comparatif

| Aspect | PostgreSQL (master) | MongoDB (display) |
|---|---|---|
| **Modèle** | Relationnel (tables + jointures) | Documents JSON imbriqués |
| **Schéma** | Strict (DDL, contraintes, types) | Flexible (chaque document peut différer) |
| **ACID** | Oui, complet (atomicité, isolation, durabilité) | Limité au document unique |
| **Jointures** | Naturel (`JOIN` SQL) | Possible (`$lookup`) mais coûteux |
| **Agrégations** | `GROUP BY`, `WINDOW`, `WITH` | Pipeline `$group` / `$facet` / `$bucket` |
| **Indexation** | B-Tree, GIN, GiST, partiel, expression | B-Tree, multi-clé, geo, TTL, texte |
| **Cas d'usage** | Données critiques structurées | Logs, time series, dashboards, IoT |
| **Scaling** | Vertical principalement (un gros serveur) | Horizontal natif (sharding) |
| **Dans ce projet** | Tables `alerte`, `incident`, `regle_detection`, `journal_audit` | Collections `alertes`, `dashboard_soc`, `top_ip_attaquantes`, `incidents` |

---

## 3. Rôle de chaque SGBD dans le projet

### PostgreSQL (la BD maître)

- **Tables relationnelles normalisées** (3FN) : pas de duplication des règles ou des équipements
- **Contraintes** : FK avec `ON DELETE CASCADE`, `CHECK`, `UNIQUE`, types stricts (`INET`)
- **Triggers** : calcul automatique du score, création automatique d'incident, audit signé
- **Procédures** : escalade, archivage, assignation round-robin
- **Vue matérialisée** : `mv_dashboard_soc` pré-calcule les statistiques pour le rafraîchissement périodique
- **`pgcrypto`** : signature cryptographique des lignes du journal d'audit (chaîne de hashes type blockchain)
- **`LISTEN/NOTIFY`** : publication d'événements pour le streaming temps réel

### MongoDB (la couche d'affichage)

- **Documents imbriqués** : une alerte = une unité atomique avec ses sous-documents (`source`, `equipement`, `regle`, `payload`)
  → **pas de jointure nécessaire à la lecture**, donc rapide
- **Agrégations analytiques** : `$group`, `$facet`, `$lookup`, `$bucket`, `$unwind`
- **Index multi-niveau** : sur `source.ip`, `regle.severite`, `payload.url`
- **Flexibilité** : on peut ajouter des champs sans migration (utile quand le format des événements évolue)

---

## 4. Le pont conceptuel : la colonne `JSONB`

PostgreSQL **a aussi** un type `JSONB` avec un index `GIN`. C'est le pont conceptuel :

```sql
-- Dans PostgreSQL (table alerte)
payload JSONB

CREATE INDEX idx_alerte_payload_gin
    ON alerte USING GIN (payload jsonb_path_ops);

-- Recherche dans le JSON imbriqué
SELECT * FROM alerte
 WHERE payload @> '{"url":"/api/login"}'::jsonb;
```

```javascript
// Dans MongoDB (collection alertes)
db.alertes.find({ "payload.url": "/api/login" })
```

→ Le projet montre que **PostgreSQL peut aussi stocker du JSON** (cas hybride).
→ Mais quand le ratio écriture/lecture devient déséquilibré (beaucoup plus de lectures), MongoDB devient nécessaire pour la performance et le scaling horizontal.

---

## 5. Résumé pour la soutenance

> *« PostgreSQL est la mémoire fiable du SIEM : il garantit qu'aucun incident ne sera perdu, qu'aucune règle ne sera violée, et que tout audit est signé. MongoDB est la vitrine du SIEM : il sert le tableau de bord du SOC à la milliseconde, peut indexer des millions d'événements, et accepte n'importe quel format d'alerte. Le script Python est le pont qui transporte la vérité de PostgreSQL vers la vitrine MongoDB. »*

C'est exactement l'architecture utilisée par les vrais SIEM en production.
