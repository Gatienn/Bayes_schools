# Rapport
# Schools: ranking school examination results using multivariate hierarcical models

## Présentation de l'étude et du jeu de données

Dans cette étude, nous analysons et comparons les résultats de plusieurs écoles Londoniennes à des examens. L'objectif final est de classer les écoles entre elles.

Pour cela, nous disposons des résultats moyens standardisés de 1978 élèves répartis dans un total de 38 écoles.  
On introduit plusieurs facteurs influent sur le résultat des élèves : 
- D'une part, des facteurs relatifs aux élèves : leur genre, leur résultat au *London Reading Test* LRT et une note de *verbal reasoning* VR (1,2 ou 3, 1 étant le meilleur niveau).
- D'autre part, des facteurs relatifs à l'école dans laquelles ils étudient : Le genre des élèves de l'école (école de garçons, de filles ou mixte) ainsi que la dénomination de l'école (Church of England, Roman Catholic, State school ou other).

## Modèle utilisé

On note $Y_{ij}$ les résultats moyens standardisés de l'élève $i$ étudiant dans l'école $j$.  
Ces résultats sont modélisés comme étant issus d'une loi normale dont la moyenne de la précision dépendent à la fois de l'élève et de son école :  
$$Y_{ij}\sim \mathcal{N}(\mu_{ij},\tau_{ij})$$

La moyenne est une combinaison de tous les facteurs introduits précedemment :

$$\mu{ij} = \alpha_{1j} + \alpha_{2j}LRT_{ij} + \alpha_{3j}VR_{1ij} + \beta_1LRT_{ij}^2 \hspace{3em} \quad \quad$$ $$\hspace{4em}+ \beta_2VR_{2ij} + \beta_3Girl_{ij} + \beta_4Girls' school_j + \beta_5Boys' school_j $$ $$+ \beta_6CE school_j + \beta_7RC school_j + \beta_8 other school_j $$ 
où $Girl_{ij}$ vaut 1 si l'élève est une fille et 0 si c'est un garçon.  

Si la corrélation entre les scores de l'élève à différents tests sur ses résultats moyens est évidente, il est également pertinent de tenir compte des facteurs relatifs à l'école. En effet, l'environnement dans lequel l'élève étudie peut avoir un impact sur ses résultats.

La variable $\alpha_{1j}$ est importante puisqu'elle représente le 'résidu' pour l'école $j$ après ajustement avec les facteurs associés aux élèves et à l'école elle-même. Nous verrons s'il est intéréssant de l'utiliser afin de classer les performances des écoles.

On modélise par ailleurs la précision (inverse de la variance) de la manière suivante :  
$$\log \left(\tau_{ij}\right) = \theta + \phi LRT_{ij}$$
D'après cette modélisation, meilleurs sont les résultats d'un élève au test LRT, plus la précision est grande et par conséquent la variance est faible.  
L'interprétation que nous pourrions faire de cette relation est qu'un élève qui a de très bons résultats, généralement un élève sérieux dans son travail, aura tendance à avoir des résultats plus réguliers qu'un élève qui a de moins bons résultats et ne travaille pas, d'où une variance plus faible.

Enfin, pour $j$ fixé, les $(\alpha_{kj})$ sont supposés être issus d'une loi normale multivarié de moyenne $\gamma$ et de matrice de covariance $\Sigma$ :
$$\alpha_{kj}\sim \mathcal{N}\left(\gamma,\Sigma\right)$$

On obtient finalement le graphe du modèle suivant :  

![graphe](images\graphe_modele.jpg)  
*Figure 1 : Graphe du modèle*

## Echantilloneur de Gibbs et Metropolis Hastings

Présentation des loi a priori et a posteriori
### Les lois a priori
 - $\pi\left(\beta_{k}\right) \sim \mathcal{N}\left(0, 10^{4}\right) \quad \forall k \epsilon \left[1 \dots 8\right]$
 - $\pi\left(\theta\right) \sim \mathcal{N}\left(0, 10^{4}\right)$
 - $\pi\left(\phi\right) \sim \mathcal{N}\left(0, 10^{4}\right)$
 - $\pi\left(\alpha_{j}\right) \sim \mathcal{N}_{3}\left(\gamma, \Sigma\right) \quad \forall j$ 
 - $T = \Sigma^{-1} \sim Wishart\left(R\right)$
 - $\pi(\gamma) \sim \mathcal{N}_{3} \left(\mu^{\ast}, \Sigma^{\ast} \right)$

### Les lois a posteriori
On pose $a=0$ et $b^2=10^4$
 - $\pi\left(\beta_{k}|\dots\right) \propto \exp\left(-\frac{\left(\beta_{k}-a\right)^2}{2b^2}\right) \times \exp\left(-\sum_{i,j}\frac{(Y_{ij}-\mu_{ij})^2}{2}\tau_{ij}\right) \quad \forall k \epsilon \left[1 \dots 8\right] $
 
Cette expression peut se simplifier pour obtenir une loi normale $\pi\left(\beta_{k}|\dots\right) \sim \mathcal{N}\left(\tilde{\mu}, \tilde{\sigma}^2\right)$

où $\tilde{\sigma}^2 = \left(\frac{1}{b^2}+\sum_{i,j}LRT_{ij}^2\tau_{ij}\right)^{-1}$ et $\tilde{\mu} = \tilde{\sigma}^2\left(\frac{a}{b^2}+\sum_{i,j}\left(Y_{ij} -\mu_{ij}+\beta_{k}LRT_{ij}^2\right)LRT_{ij}\tau_{ij}\right)$

 - $\pi\left(\theta|\dots\right) \propto \exp(-\frac{\left(\theta - a\right)^2}{2b^2}) \times \exp\left(-\sum_{i,j}\frac{\left(Y_{ij} - \alpha_{ij}\right)^2}{2}e^{\theta}e^{\phi LRT_{ij}}\right)$
 - $\pi\left(\phi|\dots\right) \propto \exp(-\frac{\left(\theta - a\right)^2}{2b^2}) \times \exp\left(-\sum_{i,j}\frac{\left(Y_{ij} - \alpha_{ij}\right)^2}{2}e^{\theta}e^{\phi LRT_{ij}}\right)$
 - $\pi\left(\alpha_{j}|\dots\right) \propto \exp\left(-\frac{1}{2}\left(\alpha_{j}-\gamma\right)^TT\left(\alpha_{j}-\gamma\right)\right) \times \exp\left(-\sum_{i}\frac{\left(Y_{ij}-\mu_{ij}\right)^2}{2}e^{\theta}e^{\phi LRT_{ij}}\right)$


Pour les lois a posteriori non identifiées, on a réalisé un échantillonnage par l'algorithme de Metropolis-Hastings. Bien que la loi des $\beta_{k}$ soit connue, on a utilisé la première expression, et donc l'algorithme M-H, qui nous a donné un meilleur résultat.

## Résultats

Le temps d'éxécution étant relativement important, on commence par générer des chaines de taille 50 à 100 afin d'ajuster le taux d'acceptation des propositions générées par la marche aléatoire. L'objectif est d'avoir un taux d'acceptation d'environ 30%, ce qui est un taux standard pour cet algorithme.

Après avoir ajusté le taux d'acceptation, une chaîne de taille 1000 est générée. On peut si besoin retirer les premières valeurs de la chaînes (burning) ou n'en conserver qu'une sur un certain nombre de réalisations (thining) afin de limiter la dépendance d'un état de la chaîne avec l'état suivant.  

On obtient des chaînes stationnaires pour la plupart des paramètres : 

![graphe](images\chaine_theta_phi_beta.jpg)
![graphe](images\chaine_M_T.jpg)  
*Figure 2 : Allure des chaînes stationnaires obtenues*  

En revanche, nous rencontrons des difficultés pour ajuster la chaîne des $\alpha_j$ : 

![graphe](images\chaine_alpha.jpg)  
*Figure 3 : Chaines des $\alpha_j$ obtenues*

L'allure des chaines suggère que la marche aléatoire est trop petite. Toutefois, celle-ci a été ajustée pour atteindre un taux d'acceptation de 30% environ. En augmentant la marche aléatoire, on obtient un taux d'acceptation trop faible et la chaîne reste souvent constante.

Une piste d'analyse des résultats de ce modèle serait d'étudier la capacité des $\alpha_{1j}$ à classer les écoles entre elles.  
En particulier, on pourrait comparer le classement obtenu avec les $\alpha_{1j}$ à une autre manière de classer les écoles, par exemple en classant selon les performances des élèves ($Y_{ij} ou LRT_{ij}$).
