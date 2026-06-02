# Livraison — Projet SIEM (Examen Final BDA — CI1 G1)

**Étudiant :** Yassir ZHD
**Encadrant :** Pr. Achraf Chakir BARAKA
**Date :** 22 mai 2026

Ce dossier contient **tous les livrables** demandés par le cahier des charges,
plus des bonus pour la soutenance.

---

## Contenu

```
livraison/
├── LISEZ-MOI.md                       ← (ce fichier)
│
├── rapport/                           ← LE RAPPORT
│   ├── rapport.tex                    ← Source LaTeX (à compiler)
│   ├── rapport.pdf                    ← Version compilée prête à imprimer
│   ├── architecture.png               ← Schéma architecture
│   ├── mcd.png                        ← Modèle entité-association
│   ├── dashboard_top.png              ← Capture KPI/timeline
│   └── dashboard_bottom.png           ← Capture top IP/incidents
│
├── presentation/                      ← LA PRÉSENTATION
│   ├── presentation_SIEM.pptx         ← Diaporama PowerPoint (14 slides)
│   ├── presentation_SIEM.pdf          ← Version PDF (au cas où)
│   └── build_pptx.py                  ← Script de génération (réutilisable)
│
└── guide/                             ← LE GUIDE JOUR J
    ├── guide_presentation.md          ← Guide complet (markdown)
    ├── guide_presentation.pdf         ← Version PDF imprimable
    ├── guide_presentation.docx        ← Version Word éditable
    ├── cheatsheet_1page.md            ← Antisèche concentrée
    └── cheatsheet_1page.pdf           ← À plier en deux dans la poche
```

---

## 1. Le rapport — `rapport/`

Le rapport suit **strictement** la structure imposée par le cahier des charges :

1. **Introduction** (présentation du système + objectifs)
2. **Partie I — PostgreSQL** (12 pts)
   - Description des tables
   - Script SQL : CREATE TABLE / SEQUENCE / Fonctions / Triggers / Boucles / Vue
   - Explication des choix techniques
3. **Partie II — MongoDB** (8 pts)
   - Description de la collection (19 champs)
   - Justification des champs
   - Insertion des données
   - Requêtes : numérique / multi-conditions / textuelle / MAJ / suppression / agrégation / projection
4. **Conclusion** (synthèse + difficultés + perspectives)

### Compilation du `.tex`

Si tu veux **recompiler le PDF toi-même** (par exemple si tu modifies du texte) :

**Sur Windows (avec MikTeX ou TeX Live)**
```powershell
cd rapport
pdflatex rapport.tex
pdflatex rapport.tex   # 2x pour la table des matières
```

**Sur Overleaf** (recommandé, pas d'install)
1. Aller sur https://www.overleaf.com
2. Créer un nouveau projet "Upload Project"
3. Uploader le contenu du dossier `rapport/` en zip
4. Compiler avec `pdfLaTeX`

### Format du PDF
- **22 pages**
- Page de garde + table des matières + 4 chapitres
- Code SQL et JavaScript avec coloration syntaxique
- Diagrammes intégrés (architecture + MCD)
- Captures du dashboard

---

## 2. La présentation — `presentation/`

**14 slides**, 16:9 widescreen, design type Apple Keynote :
- Fond sombre (#0B0F17), police sans-serif, accents bleu/rouge/vert
- Aucun emoji, aucun logo Streamlit, aucune phrase "projet"
- Numérotation `01 / 14` discrète en bas

**Plan détaillé**
1. Couverture
2. Plan
3. Le problème
4. Architecture
5. PostgreSQL — 6 tables liées
6. PostgreSQL — Logique métier (séq/fct/trig/proc/vue/idx)
7. Trigger phare en action
8. MongoDB — Document riche (19 champs)
9. MongoDB — 7 types de requêtes (CDC)
10. Pipeline complet
11. Dashboard (capture)
12. Conformité CDC 20/20
13. Difficultés et perspectives
14. Merci / Questions

### Pour ouvrir
- **PowerPoint 2016+**, **Microsoft 365**, ou **Keynote** : ouvrir directement
  `presentation_SIEM.pptx`.
- **Google Slides** : Fichier → Importer → présentation_SIEM.pptx.
- **LibreOffice Impress** : ouvrir directement (compatible).

### Pour modifier le design
Le fichier `build_pptx.py` régénère le `.pptx` depuis du code Python
(`python-pptx`). Modifie ensuite les couleurs / textes et relance avec :
```
python build_pptx.py
```

---

## 3. Le guide jour J — `guide/`

**Le document le plus important pour ta soutenance.**

### `guide_presentation.md` / `.pdf` / `.docx`
Document complet de **9 pages** qui contient :
1. **Checklist matérielle** (sac à préparer la veille)
2. **Préparation 30 min avant** (commandes Docker, onglets Chrome)
3. **Script minute par minute** pour chaque slide :
   - Ce que tu DIS (texte exact)
   - Ce que tu FAIS (cliquer sur quoi, montrer quoi)
   - Astuces si le prof t'arrête
4. **Démo en direct** (3 étapes chronométrées)
5. **Démo de secours** (si Docker plante)
6. **10 questions probables du prof + réponses prêtes**
7. **Erreurs à éviter absolument**
8. **Cheat sheet — chiffres et termes**
9. **Mot de la fin** (ouverture sur stage)

### `cheatsheet_1page.md` / `.pdf`
Une seule page avec les **chiffres clés**, le **chronométrage**, les **3 étapes
de la démo**, les **5 questions probables** et le **mot de la fin**.
À plier en deux et glisser dans la poche le jour de la soutenance.

---

## Conformité au CDC — récapitulatif

| Critère CDC | Exigence | Réalisé | Note |
|---|---|---|---|
| **Projet A — PostgreSQL** | | | |
| 1. Tables PK/FK | ≥ 3 | 6 | 2/2 |
| 2. Séquences | ≥ 2 | 6 | 2/2 |
| 3. Fonctions PL/pgSQL | ≥ 2 | 6 | 2/2 |
| 4. Triggers | ≥ 2 | 4 | 2/2 |
| 5. Boucles (procédures) | ≥ 2 | 5 | 2/2 |
| 6. Vue globale | 1 | 4 + 1 mat. | 2/2 |
| **Sous-total Projet A** | **12 pts** | | **12/12** |
| **Projet B — MongoDB** | | | |
| 7. Collection ≥ 15 champs | ≥ 15 | 19 | 2/2 |
| 8. Condition numérique | requis | OK | (6/6) |
| 9. Conditions multiples (AND/OR) | requis | OK | |
| 10. Recherche textuelle | requis | OK | |
| 11. Mise à jour | requis | OK | |
| 12. Suppression | requis | OK | |
| 13. Agrégation (avg/sum/group) | requis | OK | |
| 14. Projection | requis | OK | |
| **Sous-total Projet B** | **8 pts** | | **8/8** |
| **TOTAL** | **20 pts** | | **20/20** |

---

## Bon courage pour ta soutenance.

— Yassir
