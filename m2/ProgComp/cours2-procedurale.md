# Programmation Comparée - Cours 2 : Programmation procédurale 

### GOTO: pour ou contre 

Une étiquette peut être la cible d'un nombre arbitraire de ```goto```, donc 
on ne peut pas regarder en arrière. Il est donc difficile de raisonner sur le 
programme.  
Par contre, certains problèmes sont plus efficaces ou lisibles si formulés 
avec un ```goto```. 

## Programmation par raffinements successifs

### Programmation procédurale 

Une **procédure** (routine, sous-routine) est un fragment de code paramétré 
par des arguments formels.  
Un **appel de procédure** est une instanciation particulière de ce fragment de 
code, où des arguments effectifs prennent la place des arguments formels. C'est 
la seule façon d'exécuter le code de la procédure.  
Une procédure ne produit pas une valeur comme résultat, par contre elle modifie 
l'**état** du programme. 

### Programmation impérative 

C'est le paradigme de programmation qui voit le programme un *transformateur 
d'état*, où des appels de procédures s'imbriquent séquentiellement.  
Ainsi, l'exécution d'un tel programme peut être décrite comme un arbre 
d'appels parcouru en profondeur.  
L'**état** d'un programme est surtout formé de sa mémoire, le lieu plus ou 
moins structuré où sont représentées des structures de données accessibles via 
un nom. 

### Conception par raffinements 

Le développement suit globalement le plan suivant : 

* Quoi ? 
  + prendre conscience d'un problème
  + décomposition en sous-problèmes
* Comment ? 
  + Analyse 
  + Conception
  + Programmation 
* Et alors ?
  + Evaluation 

En programmation procédurale et impérative, on sépare clairement les 
algorithmes et les données.  
En fait, la conception **descendante** représente le traitement à effectuer 
par un programme dont le code source contient des opérations abstraites (sans 
implémentation). Une fois qu'on est sûr que la procédure est conforme à la 
spécification, on **raffine** les opérations abstraites.  
Au fur et à mesure des raffinements, on obtient un programme exécutable.  
C'est la base de la *méthode B*. 

## Raisonnement sur les programmes procéduraux 

Les procédures sont un outil pour *hiérarchiser le raisonnement* : un appel 
peut être vu comme une boîte noire qu'on remplace par la spécification de la 
procédure.  
On peut alors ignorer les détails d'implémentation.  
Mais alors, il faut tout de même raisonnement sur les programmes avec 
rigueur.  
On utlise les **triplets de Hoare** : 
```{P} S {Q}```  
Si *P* est vérifiée avant l'exécution de *S*, alors *Q* sera vérifiée après 
l'exécution de *S*. 

## Comparaison avec la POO 

La conception descendante de la programmation procédurale :  

* (+) Raffinement continu
* (+) Facilite le raisonnement grâce aux triplets de Hoare
* (+) Compilation plus simple 
* (-) Forte dépendance entre structures de données (extensibilité difficile)
* (-) Spécification éloignée des vraies données car on suppose une connaissance 
  des structures de données du problème

La programmation orientée objet : 

* (+) Plus flexible sur la nature des données à traiter
* (+) Les objets simulent la réalité, donc on évite l'inadéquation avec le 
  modèle
* (-) Raisonnement complexe, car caché dans les délégations inter-objets