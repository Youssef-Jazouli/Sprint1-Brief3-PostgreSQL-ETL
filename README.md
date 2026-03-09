# 🛒 Superstore Data Pipeline - Sprint 2

## 📝 Objectif du Projet
Transformer un dataset CSV brut en une base de données relationnelle **PostgreSQL** normalisée, prête à être connectée à des outils de Business Intelligence (Power BI / Tableau).

---

## 1️⃣ Création de la base et connexion
- Création de la base de données `superstore_db` sous PostgreSQL.
- Configuration de la connexion Python → PostgreSQL automatisée en utilisant l'ORM **SQLAlchemy** et le driver `psycopg2`.

## 2️⃣ Conception du modèle normalisé (3NF)
Pour éviter la redondance et garantir l'intégrité absolue des données, le dataset a été décomposé en 4 entités (Modèle en Étoile) :
- **`customers`** : PK = `Customer ID`.
- **`products`** : PK = `Product ID`.
- **`geography`** : PK = `geo_id` (Création d'un ID unique pour la localisation).
- **`orders` (Table de faits)** : PK = `Row ID` | FK = `Customer ID`, `Product ID`, `geo_id`.

## 3️⃣ Chargement des données (Pipeline ETL)
- **Séparation** : Le dataset plat a été divisé à l'aide de Pandas pour correspondre aux tables créées.
- **Nettoyage** : Gestion stricte des doublons avec `drop_duplicates(subset=[...])` sur les IDs pour respecter les contraintes `UNIQUE` de PostgreSQL.
- **Insertion** : Chargement des DataFrames vers SQL via la méthode `.to_sql()`.

## 4️⃣ Transformations complémentaires (Vues SQL)
Calcul des métriques demandées directement côté base de données pour alléger les futurs Dashboards :
- **`vue_sales_analysis`** : Agrégation du Total Sales par Catégorie et par Région.
- **`vue_profit_analysis`** : Calcul du Profit moyen par Client avec tri décroissant.

## 5️⃣ Préparation pour la semaine suivante
- **Intégrité validée** : Les données ont été insérées sans perte (Vérification par `COUNT`).
- **Data Viz Ready** : Les relations (Primary Keys / Foreign Keys) sont opérationnelles, permettant des jointures simples et optimisées pour Power BI ou Tableau.

---
**🛠️ Stack Technique :** Python (Pandas) | PostgreSQL | SQLAlchemy | Jira (Agile)
