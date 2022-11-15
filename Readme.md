# Prédire la valeur d’un bien immobilier parisien en fonction de l’environnement qui l’entoure

## Business Understanding : 

Quelle est la valeur de mon bien immobilier ? Voilà une question que se pose tout vendeur lorsqu’il doit mettre son bien en vente. 

Parmi toutes les étapes de la mise en vente d’un bien immobilier, l’estimation du prix est une étape cruciale. Dans 70% des cas, le bien est mis en vente en agence immobilière (d’après le figaro immobilier). Ce travail est alors réalisé par un agent immobilier qui va utiliser les données à sa disposition et son expertise du quartier pour proposer une estimation pertinente du bien. 

Dans les 30% de ventes restantes, les vendeurs optent pour des ventes entre particuliers. Pour un particulier, la détermination du prix d’un bien peut être fastidieuse : l’accès à l’information est plus compliqué (non accès aux ventes déjà passées, peu de biens en vente dans certains quartiers…). 
Des outils en ligne ont alors émergé pour offrir une aide à ces particuliers.
Ces estimateurs s’appuient majoritairement sur les caractéristiques intrinsèques des biens immobiliers (surface, nombre de pièces, ascenseur, balcon...) et l’historique des ventes récentes. Sur certaines plateformes, ces estimations automatiques sont vérifiées par des experts, humains, avant d’être envoyées. Il est courant que cette estimation soit transmise sous gage d'une vérification d’identité de l’utilisateur.

<div style="text-align: center">
<img src="/Fichiers_Readme/image001.png" height="400" align="center">
</div>

Cependant dans une même ville, il peut exister des disparités de prix importantes entre deux appartements similaires dans des quartiers différents (parfois même au sein d’un même quartier). 
Un des éléments pouvant expliquer ces différences de prix est l’environnement du bien et la qualité de vie que l’on peut y avoir. 

### Notre proposition de valeur : 

Notre projet est de créer un estimateur de prix des biens immobiliers basé aussi bien sur les caractéristiques intrinsèques des biens (ce qui existe déjà) que sur les caractéristiques du quartier du bien qui s’y trouvent. 

En effet, lors de l’achat d’un bien immobilier, son environnement est un facteur déterminant dans de très nombreuses situations : est-ce qu’il y a un parc à proximité pour y promener mon chien ? Est-ce que le quartier est bien desservi pour que je puisse aller rapidement au travail ? etc..

Afin de pouvoir étudier et représenter ces disparités mais aussi par souci de simplicité, nous avons choisi de restreindre notre étude aux appartements parisiens pour lesquels de nombreuses données sont disponibles. 

### Une mise en situation : 

Nous avons défini 3 cas de figure majeurs dans lesquels notre projet est pertinent avec 3 acteurs différents : 
Un agent immobilier voulant définir le prix d’un bien, pourra utiliser nos prédictions comme une première approximation du prix d’un bien qu’il précisera après la visite en détails du bien
Un vendeur souhaitant obtenir une première estimation du prix de son bien pourra le faire en quelques clics
Un acheteur souhaitant acquérir un bien spécifique pourra voir la pertinence du prix fixé vis-à-vis des prédictions de notre algorithme.

### Critère de validation

Le prix d’un bien immobilier dépend, en effet, de ses caractéristiques intrinsèques et du quartier, mais aussi de nombreux autres facteurs que notre modèle ne prend pas en compte. On peut citer la présence ou non de vis-à-vis, la qualité de l’intérieur, la rareté du bien. On ne peut donc espérer des prédictions exactes.

Le but de notre modèle est donc d’obtenir une première approximation du prix afin de pouvoir ensuite mieux définir son prix en fonction de caractéristiques qui lui sont propres.
Concernant la validation de notre produit, nous avons contacté un agent immobilier parisien qui nous a informé que l’erreur de prédiction de la plupart des estimateurs proposés en ligne est de 15%.
Nous nous fixons donc comme critère de validation une marge d’erreur de 15%. 

## Et concrètement ? 
### Nos datasets 

<div style="text-align: center">
<img src="/Fichiers_Readme/image007.png" height="300" align="center">
</div>

Le premier dataset regroupe toutes les ventes immobilières de Paris en 2019. Ce dataset contient des informations comme le prix, la localisation, la surface et le nombre de pièces d’un bien.
Les quatres autres datasets vont nous permettre de décrire le voisinage de chaque bien immobilier. Le dataset Base des équipements (BPE) dénombre tous les équipements et les services de France, nous avons sélectionné seulement ceux de la ville de Paris. Le nombre d’équipements étant très important, nous les avons regroupés par catégorie. Ainsi la catégorie “Commerce alimentaire” regroupe les supermarchés, hypermarché mais aussi les épiceries, boucherie, etc..
Pour caractériser l’environnement de chaque appartement, nous avons décidé d’énumérer les différents équipements qui sont dans ce voisinage. Nous avons choisi de définir le voisinage avec un rayon de 500 m autour de chaque appartement car c’est une distance qui permet d’englober convenablement l’ensemble des biens facilement accessibles à pied.
Pour chaque équipement (dont nous connaissons les coordonnées GPS), nous calculons sa distance au bien étudié grâce à la métrique de Haversine qui renvoie la distance en km entre deux coordonnées GPS. Ainsi, si cette distance est inférieure à 500 m, nous incrémentons un compteur lié à la catégorie de l’équipement.

Prenons un cas concret : 

<div style="text-align: center">
<img src="/Fichiers_Readme/image009.png" height="400" align="center">
</div>

Nous notons que l’appartement étudié est proche de 2 espaces verts, de 2 bouches de métro et d’un monument. Nous incrémenterons donc les compteurs “Espaces Verts”, “Métro” et “Monuments” de cet appartement en conséquence.

Toutes ces informations sont intégrées au dataset contenant les ventes des biens immobiliers.

### Vers un grand nettoyage

Une fois les features personnalisées ajoutées, on réalise un nettoyage des données. Tout d’abord on nettoie les lignes. Pour cela, on supprime les lignes contenant des valeurs manquantes ainsi que les lignes avec des valeurs aberrantes (outliers).

Une fois ce travail réalisé, nous allons aborder la phase de nettoyage des colonnes. On supprime celles qui sont les plus corrélées et qui ne seront pas utiles pour l’apprentissage de nos modèles.

Puis, on décide de ne garder que les appartements dont le prix au m2 est compris entre 5 000€ et 23 000€ et dont la surface est inférieure à 300 m2 pour supprimer les valeurs extrêmes.

### Pour une bonne préparation des datasets

Nous modifions certaines features afin qu’elles puissent être utilisables par les algorithmes. Ainsi, on réalise un One Hot Encoding (voir figure ci-dessous) sur l’arrondissement des appartements pour que cela n’affecte pas les modèles d’apprentissage et pour que l’on puisse garder cette information.

<div style="text-align: center">
<img src="/Fichiers_Readme/image011.png" height="300" align="center">
</div>

Ainsi nous avons notre jeu de données final.


## En route vers le machine learning

###  Un problème de régression

Dans ce problème, nous cherchons à prédire une valeur : la valeur foncière d’un appartement à partir des caractéristiques principales du bien mais aussi des caractéristiques de son voisinage. Nous sommes donc dans le cadre d’un problème dit de régression.

<div style="text-align: center">
<img src="/Fichiers_Readme/image013.png" height="400" align="center">
</div>

### Direction les algorithmes

Nous avons implémenté un grand nombre d’algorithmes de régression sur notre jeu de données : LASSO, Ridge, SVR, …

Puis, on a comparé ceux qui minimisaient au maximum le MAPE (“Mean Absolute Percentage Error”). Après ce travail de sélection, on a gardé uniquement les 3 modèles suivants : SVR Linéaire, Gradient Boosting, XG Boosting.

Toutefois, souhaitant améliorer nos prédictions, nous avons décidé de réaliser des clusters (originaires d’un arbre de décision) en amont afin de faciliter le travail des modèles en leur fournissant des données plus similaires et permettant ainsi d’augmenter leur prédiction.

Pour résumer, voici un schéma expliquant la démarche expliquant notre démarche :

<div style="text-align: center">
<img src="/Fichiers_Readme/image015.png" height="450" align="center">
</div>

### Fiabilité de notre modèle

**Attention les résultats ci-dessous correspondent aux résultats du dataset de 2019 (et non 2020 comme dans les notebooks).** 

<div style="text-align: center">
<img src="/Fichiers_Readme/image017.png" height="300" align="center">
</div>

D’après le tableau ci-dessus, on remarque que le gradient boosting, après séparation en clustering, donne un MAPE plus faible que celui du dataset initial.

On peut finalement noter que notre MAPE vaut 17,6% dans les conditions optimales ce qui est supérieur à notre critère de validation de 15%. Notre algorithme est donc moins performant que la norme des estimateurs en ligne.

Néanmoins, cette erreur peut s’expliquer par le manque de caractéristiques intrinsèques du bien immobilier comme la présence d’un ascenseur, d’une terrasse, le numéro de l’étage… 

## Si notre modèle voit le jour…. 

Si le projet se concrétise, plusieurs points sont à prendre en compte.

En effet, il serait pertinent de créer une plateforme en ligne, permettant une utilisation plus ergonomique de notre produit par le client.

Il serait également nécessaire de mettre en place une base de données dynamique, grâce à un algorithme récupérant automatiquement les données depuis des plateformes de mise en ventes en ligne (web scraping).

De plus, il serait judicieux de collaborer avec :
  - Des agents immobiliers, pour profiter de leur expertise
  - Des plateformes de mise en vente de biens immobiliers (pour la récupération automatique des données).
