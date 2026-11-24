# Althea-systems-data
1. # Contexte et objectif
Althea Systems est une entreprise de vente en ligne de matériel médical destinée aux professionnels de santé. Le jeu de données étudié couvre environ deux années d’activité, d’octobre 2024 à octobre 2026. Il est composé de six fichiers CSV liés entre eux : clients, commandes, lignes de commande, produits, catégories et stock hebdomadaire.
Ces fichiers proviennent d’exports bruts. Certaines informations peuvent être manquantes, mal formatées ou incohérentes.
Le travail consiste à explorer les six fichiers, mesurer les anomalies, corriger en priorité celles qui peuvent fausser les résultats, puis vérifier la qualité des données préparées.

2. # Méthode de travail
L’analyse a été réalisée en Python, avec la bibliothèque pandas, dans un notebook Google Colab. Le projet est versionné sur GitHub (dépôt Althea-systems-data). Les fichiers bruts ont été conservés sans modification. Les traitements ont été effectués sur des copies, notamment commandes_propre.csv, lignes_commande_propre.csv et stock_hebdo_propre.csv.
Pour explorer les données, les principales fonctions utilisées sont read_csv() pour charger les fichiers, head(), info() et describe() pour les examiner, isna().sum() pour compter les valeurs manquantes, duplicated().sum() pour repérer les doublons, value_counts() pour vérifier les valeurs d’une colonne et to_datetime() pour convertir les dates. Les méthodes strip(), lower() et replace() ont servi à harmoniser les textes.

