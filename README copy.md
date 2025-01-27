## **MINI PROJET ETL / CLOUD COMPUTING : Construction d'un projet Pipeline de Données de Bout en Bout** <br>
 
![tffgf](./images\Capture.PNG) <br>

Dans le cadre de ce mini-projet, nous nous attaquons à la construction d’un pipeline de données complet en utilisant un jeu de données brut. L’objectif principal est de développer un processus efficace de transformation de données pour répondre à des besoins analytiques spécifiques.

Nous débutons par l'examen du schéma physique du jeu de données initial, nécessitant une dénormalisation pour aboutir à un schéma adapté à un Data Warehouse.

Le projet met l’accent sur l’utilisation de DBT (Data Build Tool) pour orchestrer les transformations de données et concevoir le schéma de l’entrepôt de données, hébergé sur Amazon Redshift. En travaillant avec ces technologies, nous visons à améliorer notre maîtrise de SQL tout en approfondissant notre compréhension du data modeling.<br>

## **Objectifs** <br>

🗂️ **Modélisation des données** : Concevoir un schéma physique du jeu de données brut et le dénormaliser pour répondre aux besoins analytiques.<br>

☁️ **Intégration des données dans Redshift** : Mettre en place un entrepôt de données sur Amazon Redshift et créer un cluster Redshift Serverless.<br>

🔧 **Transformation des données avec DBT** : Utiliser DBT pour orchestrer les transformations nécessaires et structurer les données pour l’analyse.<br>

📊 **Identification de KPI et requêtes analytiques** : Définir les indicateurs clés de performance (KPI) et écrire des requêtes SQL pour répondre aux questions analytiques.<br>

## **Technologies utilisées**<br>

* **DBT (Data Build Tool)**<br>

* **Amazon Redshift** (avec cluster Serverless)

* **SQL**<br>

Ce projet représente une opportunité d’appliquer les concepts de modélisation des données et de renforcer notre expertise en ingénierie des données, tout en veillant à optimiser notre pipeline pour les besoins analytiques de l’organisation. 🚀<br>

## **Contexte**<br>

**ShopSphere**, une entreprise de vente au détail souhaite mieux comprendre ses opérations commerciales, son activité, afin de prendre des décisions plus éclairées en matière de gestion des stocks, d'optimisation des prix, et de maximisation des ventes..<br> 

![tffgf](./images\shopshere.PNG) <br>

Pour se faire, elle nous a fournit un jeu de données qui contient des informations sur les clients, les produits, les commandes, les magasins, et les approvisionnements. Ce jeu de données permet de suivre plusieurs aspects clés du business, tels que :<br>

* **Suivi des commandes clients** : Comprendre qui achète quoi, quand, et dans quel magasin.<br>

* **Gestion des stocks** : Suivre les produits commandés et les niveaux d'approvisionnement en fonction des articles vendus et de leur disponibilité.<bf>

* **Analyse des performances des magasins** : Mesurer l'efficacité des magasins en fonction des commandes traitées, des ventes réalisées, et des taux de taxe appliqués.<br>

* **Optimisation des coûts** : Évaluer les coûts des approvisionnements, particulièrement ceux des articles périssables, pour mieux gérer les marges.<br>

## **Présentation des donnéess**<br>

Nous avons reçu du client six tables distinctes qui se définissent ainsi:<bd>

**raw_customers**<br>
* **id** : Identifiant unique du client (integer).<br>
* **name** : Nom du client (string).<br>

**raw_items**<br>
* **id** : Identifiant unique de l'article (integer).<br>
* **order_id** : Identifiant de la commande à laquelle l'article appartient (integer).<br>
* **sku** : Code de l'unité de gestion des stocks (stock keeping unit) pour l'article (string).<br>

**raw_orders**<br>
* **id** : Identifiant unique de la commande (integer).<br>
* **customer_id** : Identifiant du client qui a passé la commande (integer).<br>
* **ordered_at** : Date et heure de la commande (timestamp).<br>
* **store_id** : Identifiant du magasin où la commande a été passée (integer).<br>

**raw_products**<br>
* **sku** : Code de l'unité de gestion des stocks pour le produit (string).<br>
* **name** : Nom du produit (string).<br>
* **type** : Type de produit (string).<br>
* **price** : Prix du produit (decimal).<br>
* **description** : Description du produit (text).<br>

**raw_stores**
* **id** : Identifiant unique du magasin (integer).<br>
* **name** : Nom du magasin (string).<br>
* **opened_at** : Date et heure d'ouverture du magasin (timestamp).<br>
* **tax_rate** : Taux de taxe applicable au magasin (decimal).<br>

**raw_supplies**
* **id** : Identifiant unique de l'approvisionnement (integer).<br>
* **name** : Nom de l'approvisionnement (string).<br>
* **cost** : Coût de l'approvisionnement (decimal).<br>
* **perishable** : Indique si l'approvisionnement est périssable (boolean).<br>
* **sku** : Code de l'unité de gestion des stocks pour l'approvisionnement (string).<br>

## **Etapes clés du projet**<br>

### **1.** **Data Modeling**<br>
#### **1.1** **Analyser le jeu de données brut.**<br>
* **Présentations des fichiers sources**<br>
![tffgf](./images\descriptif_tables.png) <br>

* **Détection des anomalies**<br>
Pas d'anomalies répérées mais, nous prévoyons de modifier le formatage des dates dans les tables `raw_items`, `raw_orders`, et `raw_stores`(inclusion de l'heure inutilisée).<br>

* **Visualisations globales des données**<br>
**ShopSphere** compte, en tout, **`926 clients`**, et **`6 magasins`**. En explorant les données brutes reçues, nous avons faits diverses observations.<br>

  * *Evolution des commandes*<br>
La majorité des ventes sont réalisées entre les mois de `mars et août`.<br>

   ![tffgf](./images\evolution_commandes.png) <br>

   * *Distribution des prix des produits*<br>
Afin de mieux comprendre la stratégie de prix de l'entreprise, nous avons réalisés le visuel c-dessous qui permet de distinguer trois principales fourchettes de prix:<br>

        * `Fourchette de prix **400-650**`<br>
        * `Fourchette de prix **650-790**`<br>
        * `Fourchette de prix **1050-1400**`<br>

    ![tffgf](./images\distribution_prix.png) <br>

   * *Prix de vente vs coût d'achat*<br>
Nous souhaitons observer la comparaison entre les prix de vente et les coûts d'approvisionnement afin de mieux évaluer la rentabilité des produits .<br>

    ![tffgf](./images\prix_vente_vs_cout_approv.png) <br>

   * *Nombre de commandes par produits et par catégorie*<br>
La catégorie `beverage` comptabilise `71102` commandes.<br>
La catégorie `jaffle` comptabilise `19081` commandes.<br>

    ![tffgf](./images\nombre_commande_par_categorie.png) <br>

#### **1.2** **Concevoir le schéma physique des données.**<br>

***Schéma physique de la BDD opérationelle*** (réalisé avec dbdiagramme?)

![tffgf](./images\schéma_tables.png) <br>

#### **1.3** **Déterminer les besoins de dénormalisation pour créer un schéma adapté au DataWareHouse.**<br>

Dans ce projet, la dénormalisation de la base de données est cruciale pour simplifier les requêtes analytiques et améliorer les performances. Voici les principaux aspects de la dénormalisation et les considérations importantes :

* **Table fact_orders_items**<br>
La table `fact_orders_items` est une table dénormalisée qui combine les informations des commandes et des articles. Elle inclut les éléments suivants:<br>

* **Commandes et Articles** : Cette table fusionne les détails des commandes et des articles pour faciliter les requêtes.<br>

* **Comptage des Produits** : Pour éviter une relation many-to-many avec la table `dim_products_supplies`, la table des faits doit compter le nombre de fois qu’un même produit a été commandé dans le même `order_id`. Cela permet d’éviter la duplication des données et de simplifier les analyses en regroupant les quantités commandées pour chaque produit dans chaque commande.<br>

* **Table dim_products_supplies**<br>
La table `dim_products_supplies` est également dénormalisée pour intégrer les informations sur les produits et les approvisionnements :<br>

    * **Combinaison de Produits et Approvisionnements**: La table combine les informations sur les produits et leurs approvisionnements pour éviter la complexité du schéma snowflake.<br>

    * **Éviter le Schéma Snowflake**: En fusionnant les informations sur les produits et les approvisionnements dans une seule table dimensionnelle, nous réduisons la complexité et évitons un schéma snowflake. Cela facilite la gestion des données et améliore les performances des requêtes analytiques.<br>

* **Table dim_date**<br>
La table `dim_date` est essentielle pour les analyses temporelles et sera générée à l’aide du package DBT. Le code pour créer cette table vous sera fourni, ce qui simplifiera la génération de la dimension temporelle avec des champs comme les trimestres, les jours de la semaine, les années, les mois, etc.<br>

***Schéma la BDD opérationelle dénormalisée*** (réalisé avec DBT?)
#intégrer le schéma#

**2.** **Intégration des Données dans Redshift**<br>

* Mettre en place un entrepôt de données sur Amazon Redshift.<br>
* Créer et configurer un cluster Redshift Serverless.<br>
* Importer les données brutes dans Redshift.<br>

**3.** **Transformation des Données, Testing et Documentation à l'aide de DBT**<br>

* Utiliser DBT pour orchestrer les transformations de données.<br>
* Créer des modèles DBT pour structurer les données selon le schéma dénormalisé.<br>
* Effectuer des tests pour valider les transformations.<br>
* Documenter les transformations et les modèles dans DBT.<br>

**4.** **Requêtes Analytiques**<br>

* Identifier les indicateurs clés de performance (KPI) pertinents.<br>
* Écrire des requêtes SQL pour extraire des insights analytiques à partir des données transformées.<br>
* Créer des rapports et des visualisations basés sur les résultats des requêtes analytiques à l'aide de librairies Python au choix.<br>