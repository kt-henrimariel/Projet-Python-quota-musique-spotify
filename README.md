# Les quotas de diffusion changent-ils ce que l'on écoute ?

**Musique nationale et langue locale dans les classements Spotify de dix pays**

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Notebook-Jupyter-F37626?logo=jupyter&logoColor=white)
![Analyse](https://img.shields.io/badge/Analyse-descriptive-1f4e79)
![Données](https://img.shields.io/badge/Donn%C3%A9es-420%20000%20lignes-eb6834)

Projet de groupe de statistique appliquée : analyse descriptive des classements hebdomadaires Spotify Top 200 de dix pays, enrichis de la nationalité des artistes et de la langue des chansons.

---

## Sommaire

1. [Contexte et question de recherche](#1-contexte-et-question-de-recherche)
2. [Données](#2-données)
3. [Structure du dépôt](#3-structure-du-dépôt)
4. [Installation et utilisation](#4-installation-et-utilisation)
5. [Méthode et plan du notebook](#5-méthode-et-plan-du-notebook)
6. [Résultats principaux](#6-résultats-principaux)
7. [Limites](#7-limites)
8. [Contributeurs](#8-contributeurs)
9. [Sources et références](#9-sources-et-références)

---

## 1. Contexte et question de recherche

Pendant longtemps, la musique entendue dans un pays dépendait des radios, des maisons de disques et des disquaires, en grande partie nationaux. Plusieurs pays ont encadré cette programmation par des **quotas de diffusion** (obligation de réserver une part du temps d'antenne à certains contenus). La mondialisation, puis internet et le streaming, ont modifié ce cadre : un même catalogue est accessible partout, et les playlists de plateformes jouent un rôle proche de celui d'un programmateur.

Les quotas ont été conçus pour la radio, et leur effet sur la musique réellement écoutée reste discuté. Ils sont aussi de natures différentes : certains portent sur l'**origine des artistes** (Argentine : 30 % de musique d'origine nationale), d'autres sur la **langue des chansons** (France : 40 % de chansons d'expression française ; Portugal : 30 % de musique portugaise).

> **Question centrale.** Les pays qui imposent des quotas de diffusion ont-ils, dans les classements Spotify, une répartition des écoutes plus favorable aux artistes nationaux et aux chansons en langue locale que les pays qui n'en imposent pas ?

Trois analyses préalables situent les marchés : la dynamique et le renouvellement des classements, la concentration des écoutes, et la ressemblance entre les classements des pays.

L'étude est **purement descriptive** : elle décrit des écarts entre pays et ne prétend pas mesurer l'effet causal d'une politique.

## 2. Données

| Élément | Description |
|---|---|
| Source principale | Classements hebdomadaires **Spotify Top 200**, 10 pays : Allemagne, Argentine, Corée du Sud, Égypte, France, Grèce, Japon, Maroc, Portugal, Thaïlande |
| Période | 210 semaines, du 6 octobre 2022 au 8 octobre 2026 (un classement par jeudi) |
| Taille | 419 891 lignes (une ligne = un titre dans le classement d'un pays une semaine donnée), environ 23 000 titres distincts |
| Enrichissement | Nationalité des artistes et langue des chansons, via l'API MusicBrainz et des compléments vérifiés manuellement |
| Quotas | Fichier `QUOTAS.xlsx` (présence d'un quota par pays), complété par les textes de loi pour la nature du quota |

### Colonnes de `base_complete.csv`

| Colonne | Signification |
|---|---|
| `rank` | Rang dans le Top 200 du pays cette semaine-là (1 à 200) |
| `uri` | Identifiant Spotify du titre |
| `artist_names`, `track_name`, `source` | Artistes, titre, label |
| `peak_rank`, `previous_rank`, `weeks_on_chart` | Meilleur rang, rang de la semaine précédente, nombre de semaines dans le classement |
| `streams` | Nombre d'écoutes du titre dans le pays cette semaine-là |
| `pays`, `semaine` | Pays et date de la semaine |
| `nationalite` | Code(s) pays de la nationalité des artistes (plusieurs codes séparés par `;`) |
| `langue` | Code(s) ISO 639-3 de la langue de chant (par exemple `fra`, `eng`, `spa`) |

### Variables construites dans le notebook

| Variable | Définition |
|---|---|
| `National` | Vaut 1 si au moins un artiste du titre a la nationalité du pays du classement |
| `LangueLocale` | Vaut 1 si le titre est chanté dans une langue locale du pays |
| `Anglais` | Vaut 1 si le titre est chanté en anglais |

## 3. Structure du dépôt

> Arborescence à adapter à l'état réel du dépôt.

```text
.
├── README.md                      Présentation du projet (ce fichier)
├── requirements.txt               Dépendances Python
├── notebooks/
│   └── Fichier_Final.ipynb        Notebook principal : analyse complète, de l'introduction à la conclusion
├── data/
│   ├── base_complete.csv          Classements enrichis (nationalité et langue)
│   ├── QUOTAS.xlsx                Présence d'un quota de diffusion par pays
│   └── enrichissement/
│       ├── artistes_nationalites.csv   Nationalité par artiste
│       ├── chansons_langues.csv        Langue par titre
│       └── remplissages_manuels.csv    Valeurs complétées à la main, avec leur fondement
├── scripts/
│   └── langue_chant.py            Langue d'un morceau via l'API MusicBrainz
└── docs/
    └── plan_detaille.md           Plan détaillé du projet
```

## 4. Installation et utilisation

### Prérequis

- Python 3.10 ou plus récent
- Jupyter (JupyterLab ou Jupyter Notebook)

### Installation

```bash
git clone https://github.com/<organisation-ou-compte>/<nom-du-depot>.git
cd <nom-du-depot>
python -m venv .venv
source .venv/bin/activate        # Windows : .venv\Scripts\activate
pip install -r requirements.txt
```

Contenu de `requirements.txt` :

```text
numpy
pandas
matplotlib
scipy
openpyxl
jupyter
requests        # uniquement pour scripts/langue_chant.py
```

### Lancer l'analyse

1. Placer `base_complete.csv` et `QUOTAS.xlsx` à l'emplacement attendu (dossier `data/`).
2. **Adapter les deux chemins de la cellule « Importation de la base données »** du notebook. Ils pointent par défaut vers l'ordinateur d'un contributeur :

   ```python
   base = pd.read_csv("data/base_complete.csv")
   quotas = pd.read_excel("data/QUOTAS.xlsx", engine="openpyxl")
   ```

3. Ouvrir le notebook et exécuter toutes les cellules (`Kernel`, `Restart & Run All`). L'exécution complète prend quelques minutes, à cause des graphiques.
4. Le **plan du travail** en tête du notebook permet de naviguer entre les parties par des liens cliquables.

> Dans JupyterLab 4, si certains liens du plan restent muets, désactiver le chargement partiel des cellules : `Settings`, `Settings Editor`, `Notebook`, `Windowing mode` à `none`. Le sommaire intégré (`Table of Contents`) reconnaît les mêmes titres.

### Exporter en PDF

L'export direct par LaTeX perd la mise en forme. Le plus simple : `File`, `Save and Export Notebook As`, `HTML`, puis ouvrir le fichier dans Chrome ou Edge, `Ctrl + P`, **Enregistrer au format PDF** avec l'option **Graphiques d'arrière-plan** cochée.

### Retrouver la langue d'un morceau (facultatif)

```bash
python scripts/langue_chant.py "Stromae" "Alors on danse"
python scripts/langue_chant.py "Stromae" "Alors on danse" --json
```

Le script interroge l'API publique de MusicBrainz (environ une requête par seconde) et lit la langue des paroles sur l'œuvre liée ; à défaut, il se replie sur la langue déclarée de l'album, source plus indicative. Remplacer l'adresse de contact dans `HEADERS` avant usage : MusicBrainz impose un `User-Agent` identifiant l'application.

## 5. Méthode et plan du notebook

| Partie | Contenu |
|---|---|
| **Introduction** | Contexte (mondialisation, radios, internet), question de recherche, plan |
| **1. Revue de littérature** | Biais domestique, circulation internationale de la musique, quotas, rôle des plateformes |
| **2. Données** | Source Spotify Charts, enrichissement (nationalité, langue), construction des variables, qualité |
| **3. Volume, dynamique et renouvellement** | Évolution des écoutes, saisons, semaines exceptionnelles, durée de séjour des titres |
| **4. Structure des classements** | 4.1 concentration des écoutes ; 4.2 ressemblance entre classements (similarité de Jaccard) et groupes de pays |
| **5. Préférence nationale, langues et quotas** | Parts nationales et linguistiques, comparaison selon la présence et la nature du quota, avant et après au Portugal, indice de biais domestique |
| **6. Discussion et conclusion** | Lecture des résultats, autres explications, limites, réponse à la question sur les quotas |

Quelques définitions utiles :

- **Part des écoutes** : écoutes des titres d'une catégorie divisées par les écoutes totales du Top 200, calculées semaine par semaine puis moyennées.
- **Indice de biais domestique** : part de la musique nationale dans le classement du pays, divisée par la part de cette musique dans les classements des neuf autres pays. Supérieur à 1, un pays écoute plus sa musique que les autres ne le font.
- **Similarité de Jaccard** : part de titres communs à deux classements (titres communs divisés par titres des deux classements réunis).
- **Rapport des médianes** : écoute médiane d'un titre d'une catégorie divisée par celle d'un titre hors de la catégorie, la même semaine.

## 6. Résultats principaux

- **Pas d'effet visible des quotas.** Pour les quotas de langue (France, Portugal), la part de langue locale est de 61,1 % en moyenne, contre 70,0 % dans les cinq pays sans quota. Pour le quota de nationalité (Argentine), la part nationale est de 55,6 %, contre 70,2 %.
- **Le Portugal avant et après le quota.** Le rétablissement du quota de 30 % en septembre 2023 ne s'accompagne d'aucune rupture de la part de langue locale (62,4 % avant, 61,3 % après).
- **La musique nationale pèse beaucoup, avec ou sans quota.** Le Japon (87,0 % d'écoutes nationales) et l'Égypte (81,3 %) n'ont pas de quota dans le fichier ; le Portugal (24,8 %) et l'Allemagne (45,7 %) sont les plus ouverts.
- **Ce poids vient du nombre de titres.** Par titre, l'avantage national reste faible dans la plupart des pays (écoute médiane de 4 à 7 % supérieure dans six pays sur dix).
- **Des classements très différents.** Deux pays n'ont en moyenne que 3,5 % de titres en commun, avec des ressemblances régionales.

## 7. Limites

- Les quotas concernent la radio, alors que les données sont des écoutes Spotify.
- La plupart des quotas existaient avant le début des données ; seuls deux mouvements ont lieu pendant la période (rétablissement du quota portugais en 2023, loi grecque de 2024).
- Dix pays seulement, très différents par leur langue et leur marché, dont deux pour les quotas de langue et un pour le quota de nationalité.
- Le Top 200 ne représente que les titres les plus visibles, et les playlists de la plateforme influencent les classements.
- Le classement du Portugal en quota de langue repose sur la définition légale (langue ou patrimoine culturel, quelle que soit la nationalité des artistes) ; il se modifie dans le dictionnaire `nature_quota` du notebook.

## 8. Contributeurs

| Contributeur | Rôle | Profil GitHub |
|---|---|---|
| **Mohamed AMISI LOHATA** | Construction et enrichissement de la base (partie 2), renouvellement des classements (3.2), refonte et harmonisation du notebook | [@mohamed-github](https://github.com/mohamed-github) |
| **[Prénom NOM]** | [Rôle, par exemple : concentration des écoutes (4.1)] | [@pseudo-2](https://github.com/pseudo-2) |
| **[Prénom NOM]** | [Rôle, par exemple : ressemblance entre classements (4.2)] | [@pseudo-3](https://github.com/pseudo-3) |
| **[Prénom NOM]** | [Rôle, par exemple : préférence nationale et quotas (5)] | [@pseudo-4](https://github.com/pseudo-4) |

> Chaque lien pointe vers la page de profil GitHub du contributeur : `https://github.com/<pseudo>`. Pour afficher les avatars de tous les contributeurs, GitHub propose aussi la page `https://github.com/<organisation-ou-compte>/<nom-du-depot>/graphs/contributors`.

## 9. Sources et références

**Données et outils**

- Classements hebdomadaires Spotify Top 200 (Spotify Charts)
- [MusicBrainz](https://musicbrainz.org/doc/MusicBrainz_API) : nationalité des artistes et langue des œuvres

**Textes réglementaires sur les quotas**

- France : loi n° 94-88 du 1er février 1994 modifiant la loi n° 86-1067 du 30 septembre 1986 (article 28), 40 % de chansons d'expression française
- Portugal : loi de la radio n° 54/2010, modifiée par la loi n° 16/2024 du 5 février 2024, 30 % de musique portugaise
- Argentine : loi n° 26.522 de 2009, 30 % de musique d'origine nationale
- Grèce : loi n° 5103/2024, 40 % de chansons en grec dans certains lieux publics

**Littérature**

- Aguiar, L. et Waldfogel, J. (2021). « Platforms, Power, and Promotion: Evidence from Spotify Playlists ». *The Journal of Industrial Economics*, 69(3), 653-691.
- Bacache, M., Louail, T., Renault, T. et Senik, C. (2026). *Do Cultural Quotas Shape Listening Preferences? Evidence from Radio Regulation and Music Streaming*. Document de travail n° 2026-20, HAL halshs-05683146.
- Bello, P. et Garcia, D. (2021). « Cultural Divergence in Popular Music: The Increasing Diversity of Music Consumption on Spotify across Countries ». *Humanities and Social Sciences Communications*, 8, article 182.
- Ferreira, F. et Waldfogel, J. (2013). « Pop Internationalism: Has Half a Century of World Music Trade Displaced Local Culture? ». *The Economic Journal*, 123(569), 634-664.
- Matrosova, K., Marey, L., Salha-Galvan, G., Louail, T., Bodini, O. et Moussallam, M. (2024). « Do Recommender Systems Promote Local Music? A Reproducibility Study Using Music Streaming Data ». *Proceedings of the 18th ACM Conference on Recommender Systems*, 148-157.
- Perona, M. (2011). « Comment les quotas de diffusion radiophonique nuisent à la diversité ». *Revue économique*, 62(3), 511-520.

---

*Projet réalisé dans un cadre pédagogique. Licence : [à préciser, par exemple MIT pour le code, usage pédagogique pour les données].*
