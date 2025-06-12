#  Beev - Intégration & analyse de données sur le marché automobile

##  Contenu du dépôt

| Fichier | Description |
|--------|-------------|
| `docker-compose.yml` | Déploie une base de données PostgreSQL localement via Docker. |
| `script.py` | Script Python pour lire les CSV, créer les tables et insérer les données dans la base. |
| `car_data.csv` | Données sur les voitures (modèle, motorisation, prix, année, etc.). |
| `consumer_data.csv` | Données sur la consommation par pays (volume, rating, etc.). |
| `requetes.sql` | Fichier SQL contenant les requêtes d’analyse demandées. |
| `graph_bonus.py` | Génère deux graphiques (volume et valeur des ventes par type de moteur). |
| `volume_per_year.png` | Graphique : volume annuel des ventes (électrique vs thermique). |
| `value_per_year.png` | Graphique : valeur annuelle des ventes (électrique vs thermique). |
| `jointure_resultat.csv` | Résultat intermédiaire de jointure utilisé pour les analyses. |

---

## Étapes techniques

### 1. Mise en place de la base de données
- Lancement via `docker-compose up -d`
- Arrêt via `docker-compose down`
- Connexion possible via pgAdmin

### 2. Insertion des données
- `script.py` lit les deux fichiers CSV
- Création des tables et de la jointure
- Insertion robuste et automatisée des données

### 3. Requêtes SQL (dans `requetes.sql`)
- a. Nombre total de voitures par modèle et pays
- b. Pays avec le plus d’exemplaires de chaque modèle
- c. Modèles vendus aux USA mais pas en France
- d. Prix moyen par pays et type de moteur
- e. Note moyenne : électrique vs thermique

### 4. Visualisations (bonus)
- `graph_bonus.py` génère deux graphiques :
  - Volume des ventes par an (électrique vs thermique)
  - Valeur totale des ventes par an (prix × volume)

---

## Choix techniques

- **Modèle relationnel souple** : j'ai pris en compte les variantes d’un même modèle sur plusieurs années. Les doublons potentiels sont filtrés de manière raisonnée pour ne conserver que les versions pertinentes à une année donnée.

- **Jointure sur la marque uniquement (`Make`)** :
  - La colonne `Model` dans `consumer_data.csv` était souvent  incohérente.
  - Pour éviter des jointures incorrectes ou trop restrictives, j’ai choisi de **joindre uniquement sur la colonne `Make`**, en considérant que les évaluations consommateurs concernent tous les modèles disponibles d'une marque à une date donnée.

- **Filtrage temporel post-jointure** :
  - Pour garantir la cohérence temporelle, je ne conserve que les lignes où `Production Year ≤ Review Year`.
  - Cela permet de s’assurer que le modèle existait bien au moment de l’évaluation.

- **Dédupliquation raisonnée** :
  - Lorsqu’il existe plusieurs versions d’un même modèle produites avant une review, je conserve **la version produite le plus récemment avant l’année de la review**.
  - Ce choix reflète mieux la réalité du marché au moment de l’évaluation.

- **Nettoyage et standardisation des chaînes de caractères** :
  - J’ai uniformisé toutes les chaînes (`Make`, `Model`) en les mettant en minuscules et en supprimant les espaces superflus (`.str.strip().str.lower()`), afin d’assurer une meilleure qualité des jointures.
---

## Guide d’exécution

```bash
# Étape 1 : Lancer la base
docker-compose up -d

# Étape 2 : Charger les données
python script.py

# Étape 3 : Exécuter les requêtes SQL (via psql)
# Requêtes disponibles dans le fichier requetes.sql

# Étape 4 : Générer les graphiques
python graph_bonus.py
