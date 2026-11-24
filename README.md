# Althea-systems-data
1. Contexte et objectif
Althea Systems est une entreprise de vente en ligne de matériel médical destinée aux professionnels de santé. Le jeu de données étudié couvre environ deux années d’activité, d’octobre 2024 à octobre 2026. Il est composé de six fichiers CSV liés entre eux : clients, commandes, lignes de commande, produits, catégories et stock hebdomadaire.
Ces fichiers proviennent d’exports bruts. Certaines informations peuvent être manquantes, mal formatées ou incohérentes.
Le travail consiste à explorer les six fichiers, mesurer les anomalies, corriger en priorité celles qui peuvent fausser les résultats, puis vérifier la qualité des données préparées.
2. Méthode de travail
L’analyse a été réalisée en Python, avec la bibliothèque pandas, dans un notebook Google Colab. Le projet est versionné sur GitHub (dépôt Althea-systems-data). Les fichiers bruts ont été conservés sans modification. Les traitements ont été effectués sur des copies, notamment commandes_propre.csv, lignes_commande_propre.csv et stock_hebdo_propre.csv.
Pour explorer les données, les principales fonctions utilisées sont read_csv() pour charger les fichiers, head(), info() et describe() pour les examiner, isna().sum() pour compter les valeurs manquantes, duplicated().sum() pour repérer les doublons, value_counts() pour vérifier les valeurs d’une colonne et to_datetime() pour convertir les dates. Les méthodes strip(), lower() et replace() ont servi à harmoniser les textes.
3. Exploration et anomalies détectées
L’exploration des six fichiers a permis de repérer plusieurs problèmes de qualité. Les valeurs manquantes ne sont pas toutes des erreurs : les dates d’expédition et de livraison peuvent être vides lorsqu’une commande n’a pas encore été expédiée ou livrée. En revanche, certaines absences et incohérences nécessitent une attention particulière.
Fichier	Nombre de lignes	Colonnes
clients	3 590	15
commandes	18 250	16
lignes_commande	52 123	6
produits	320	12
categories	9	4
stock_hebdo	33 310	3

Les principales anomalies observées sont :
des valeurs manquantes : 20 identifiants clients et 20 devises dans les commandes, ainsi que 20 valeurs de stock ; la colonne id_session est vide à 71 % ;
30 relevés de stock en double ;
des écritures différentes pour une même information, notamment les statuts de commande et les pays (par exemple « livree », « Livrée », « delivered » ou « FR », « fr », « France ») ;
des liens incorrects entre les tables : 35 commandes sans client valide, 40 lignes liées à un produit absent et 10 produits associés à une catégorie inconnue ;
des dates illisibles ou incohérentes, des montants négatifs ou nuls et des lignes de commande comportant des quantités, prix ou remises impossibles.
4. Mesure et importance des anomalies
Toutes les anomalies n’ont pas le même impact. Les plus sensibles sont celles qui peuvent modifier le chiffre d’affaires ou fausser les analyses par statut et par pays.
Anomalie	Lignes concernées	Part indiquée
Montant HT différent de la somme des lignes (commandes en euros)	4 824	29,2 %
Commandes en francs suisses mélangées aux euros	1 706	9,3 %
Statuts mal écrits	120	0,7 %
Montants HT négatifs ou nuls	70	0,4 %
Pays mal écrits	60	0,3 %
Lignes de commande invalides	57	0,1 %
Dates de commande illisibles	50	0,3 %
Produits inexistants / clients absents ou inconnus	40 / 35	0,1 % / 0,2 %
Doublons de stock	30	0,1 %

Les écarts de montants, le mélange des devises, les statuts mal écrits et les montants négatifs sont les anomalies les plus susceptibles de fausser les indicateurs commerciaux. Les problèmes de liens entre tables peuvent également limiter certaines analyses ciblées.
5. Nettoyage réalisé et bilan
Le nettoyage a été ciblé : seules les anomalies ayant un impact direct sur les analyses prévues ont été corrigées. Chaque traitement a été effectué sur une copie des fichiers.
Traitement effectué	Justification
Harmonisation des statuts : espaces supprimés, texte mis en minuscules et valeur anglaise « delivered » traduite en « livree ». Six valeurs distinctes sont conservées.	Éviter qu’un filtre sur un statut oublie des commandes à cause d’une différence d’écriture.
Harmonisation des pays en codes ISO : par exemple « France » et « fr » deviennent « FR ». Onze pays sont obtenus.	Regrouper correctement les ventes par pays.
Conversion des dates : 40 dates ont été récupérées grâce au format JJ/MM/AAAA. 20 dates restent vides car elles sont impossibles ou situées en 2027.	Ne pas inventer de dates ; les 20 commandes concernées sont écartées de l’analyse mensuelle.
Passage en valeur absolue des montants HT, TVA et TTC négatifs sur 70 commandes.	Les montants négatifs correspondaient à une erreur de signe ; 52 commandes retrouvent un montant HT égal à la somme de leurs lignes.
Suppression de 57 lignes de commande invalides (quantité ou prix nul/négatif, remise négative ou supérieure à 100 %).	Ces valeurs ne représentent pas des ventes exploitables et ne peuvent pas être corrigées de manière fiable.
Suppression de 30 doublons strictement identiques dans le stock hebdomadaire.	Éviter de compter deux fois les mêmes relevés, sans perte d’information.

Table nettoyée	Avant	Après
commandes	18 250	18 250
lignes_commande	52 123	52 066
stock_hebdo	33 310	33 280

Au total, les six tables représentent 107 602 lignes avant nettoyage. 87 lignes ont été supprimées, soit environ 0,08 % : 57 lignes de commande invalides et 30 doublons de stock. Aucune commande n’a été supprimée ; certaines valeurs ont été corrigées ou laissées vides lorsque leur fiabilité ne pouvait pas être garantie.
6. Anomalies non corrigées et contrôles de qualité
Certaines anomalies ont été conservées et devront être prises en compte lors des analyses :
les 1 706 commandes en CHF et les 4 824 écarts entre le montant HT et la somme des lignes : les montants facturés ne sont pas corrigés, car le chiffre d’affaires du projet est calculé à partir des lignes de commande, dont les prix sont déjà en euros ;
les 35 commandes sans client valide, les 40 lignes liées à des produits inexistants et les 10 produits avec une catégorie inconnue : les lignes sont conservées pour ne pas perdre de chiffre d’affaires et pourront être regroupées sous la valeur « inconnu » dans les analyses ;
les 20 devises manquantes, les 20 valeurs de stock manquantes, les 20 dates non exploitables et la colonne id_session, vide à 71 % ;
les 32 commandes sans ligne de détail (25 n’en avaient déjà pas et 7 ont perdu toutes leurs lignes après suppression des lignes invalides) ;
cinq prix de catalogue très élevés, compris entre 30 250 € et 305 550 €. Ils ne sont pas corrigés car le chiffre d’affaires est calculé à partir des prix des lignes de commande, qui ne présentent pas ces valeurs aberrantes.
Enfin, trois contrôles automatiques ont été effectués sur les données nettoyées et sont validés :
les statuts de commande correspondent aux six valeurs attendues et les pays aux onze codes ISO ;
il ne reste aucun montant HT négatif ou nul, ni aucune ligne de commande invalide ;
aucun doublon n’est présent sur les identifiants de commandes et de lignes, ni dans les relevés de stock.
