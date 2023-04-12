# Rapport
# Schools: ranking school examination results using multivariate hierarcical models

## Présentation de l'étude et du jeu de données

Dans cette étude, nous analysons et comparons les résultats de plusieurs écoles Londoniennes à des examens. L'objectif final est de classer les écoles entre elles.

Pour cela, nous disposons des résultats moyens standardisés de 1978 élèves répartis dans un total de 38 écoles.  
On introduit plusieurs facteurs influent sur le résultat des élèves : 
- D'une part, des facteurs relatifs aux élèves : leur genre, leur résultat au *London Reading Test* LRT et une note de *verbal reasoning* VR (1,2 ou 3, 1 étant le meilleur niveau).
- D'autre part, des facteurs relatifs à l'école dans laquelles ils étudient : Le genre des élèves de l'école (école de garçons, de filles ou mixte) ainsi que la dénomination de l'école (Church of England, Roman Catholic, State school ou other).

## Modèle utilisé

On note $Y_{ij}$ les résultats moyens standardisés de l'élève i étudiant dans l'école j.  
Ces résultats sont modélisés comme étant issus d'une loi normale dont la moyenne de la précision dépendent à la fois de l'élève et de son école :  
$Y_{ij}$ $\sim Normal(\mu_{ij},\tau_{ij})$

La moyenne est une combinaison de tous les facteurs introduits précedemment :

$\mu{ij} = \alpha_{1j} + \alpha_{2j}LRT_{ij} + \alpha_{3j}VR_{1ij} + \beta_1LRT_{ij}^2 + \beta_2VR_{2ij} + \beta_3Girl_{ij} + \beta_4Girls' school_j + \beta_5Boys' school_j + \beta_6CE school_j + \beta_7RC school_j + \beta_8 other school_j $  ,  
où $Girl_{ij}$ vaut 1 si l'élève est une fille et 0 si c'est un garçon.  

Si la corrélation entre les scores de l'élève à différents tests sur ses résultats moyens est évidente, il est également pertinent de tenir compte des facteurs relatifs à l'école. En effet, l'environnement dans lequel l'élève étudie peut avoir un impact sur ses résultats.

On modélise par ailleurs la précision (inverse de la variance) de la manière suivante :  
$log \tau_{ij} = \theta + \phi LRT_{ij}$  
D'après cette modélisation, meilleurs sont les résultats d'un élève au test LRT, plus la précision est grande et par conséquent la variance est faible.  
L'interprétation que nous pourrions faire de cette relation est qu'un élève qui a de très bons résultats, généralement un élève sérieux dans son travail, aura tendance à avoir des résultats plus réguliers qu'un élève qui a de moins bons résultats et ne travaille pas, d'où une variance plus faible.

On obtient finalement le graphe du modèle suivant :  

![graphe](images\graphe_modele.jpg)  
*Figure 1 : Graphe du modèle*

## Echantilloneur de Gibbs et Metropolis Hastings

## Résultats