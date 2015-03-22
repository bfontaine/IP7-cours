# Programmation Comparée - Cours 1 : Introduction

## Programmation 

La programmation est l'**activité** qui construit une version *exécutable* de 
la solution à un problème.  

### Langage de programmation 

Un langage de programmation est un langage *formel* défini par une **syntaxe** 
et une **sémantique**. Il est *expressif* si il fournit de nombreux mécanismes 
distincts.  
Le langage est une abstration du code machine qui sera exécuté au final. Plus 
le langage est proche de la formulation du problème analysé, plus il est 
*déclaratif*.  
Le **compilateur** traduit un programme vers un langage plus bas niveau, qui 
est plus adapté à une exécution efficace par la machine (soit nativement, soit 
via une machine virtuelle).  
Un **interprète** évalue le code source sans passer par une phase de 
traduction.  
Un **analyseur statique** est un programme qui fournit des informations sur 
le code source sans l'exécuter (comme un typeur). 


## Langage assembleur 

Les langages d'**assemblages** sont des langages bas niveau, liés aux 
instructions d'un processeur en particulier. Ils possèdent généralement 3 
classes de syntaxe : 

* mnémoniques d'instruction : opérations effectuées par le processeur 
* directives d'assemblage : donne des informations externes sur la production 
du code exécutable
* sections de données : description des constantes utilisées dans le programme

L'assembleur ne fournit pas d'abstraction au dessus du processeur, mais tous 
ses mécanismes de calcul sont accessibles. Ainsi, l'assembleur est très 
expressif du point de vue de l'utilisation optimale de la machine.  
De plus, grâce à sa structure libre, certains mécanismes sont réalisables alors 
qu'ils ne le sont pas dans des langages haut-niveau : 

* factorisation de code maximale : les conventions d'appel de procédure créent 
des barrières empêchant la factorisation de fragments communs entre procédures 
* code auto-généré et auto-modifiant : un programmeur assembleur peut écrire 
dans son segment de code, et donc se modifier pour s'optimiser ou se décrypter

L'assembleur est de nos jours peu utilisé car les compilateurs produisent du 
code presque aussi efficace que ce qu'on pourrait écrire à la main (allocation 
des registres...). De plus, l'assembleur est difficile à écrire et maintenir, 
car moins concis et modulaire qu'un langage haut-niveau. Enfin, il n'est pas 
portable.  
Toutefois il reste encore utilisé : 

* certaines instructions microprocesseur spécifiques, non gérées par les 
  compilateurs
* les *drivers* reposent sur un intéraction avec les ports E/S et les registres 
  spéciaux non contrôlable dans un langage haut-niveau

## Programmation structurée 

La crise du logiciel des années 60 a amené une réflexion sur la méthode de 
construction des programmes. Cela à conduit au *génie logiciel*. De nouveaux 
mécanismes pour **structurer** les programmes sont apparus : 

- structures de contrôle : ```while```, ```if-then-else```, ... 
- procédures et fonctions de 2<sup>nde</sup> classe
- supression du **goto** (point d'entrée et de sortie unique)
- minimisation des variables globales

De plus, la méthodologie de conception se base maintenant sur des raffinements 
successifs descendants. 

## Modularité : Programmation en grand 

La **programmation en grand** consiste à diviser le système en plus petits 
sous-programmes (*modules*), facilitant l'interconnection des composants 
logiciels. Le but est de limiter les interdépendances.  
  
Le coeur de la programmation modulaire consiste à considérer le code comme 
un *objet de 1<sup>ère</sup> classe. Cela permet un gain en expressivité 
puisque le programmeur peut définir ses propres opérateurs de contrôle. 

## Programmation générique / métaprogrammation 

La **méta-programmation** consiste à écrire des programmes qui manipulent le 
code source d'autres programmes. Certains transformations sont utiles pour 
éviter au programmeur l'écritur de code répétitif.  
Les langages d'ordre supérieur ne permettent pas, par exemple, d'inspecter une 
fonction pour l'optimiser ou la faire traiter un nouveau type.  

## Programmation concurrente 

Le programme est vu comme un ensemble de composants concurrents. Ils 
communiquent via une mémoire partagée ou s'envoient des messages sur des 
canaux. Ils peuvent être synchrone ou asynchrone. Il existe le modèle 
optimiste et le modèle pessimiste. 

## Programmation logique 

Un programmation logique décrit un problème exprimé dans une logique 
particulière. La solution au problème est trouvée via l'exécution d'un 
*prouveur*.  
On exprime souvent le problème via des clauses et des contraintes, puis on 
montre la satisfaisabilité de relations entre données. 

## Comparaison de programmes 

Il existe de nombreuses façons de comparer deux programmes. On regarde en 
général : 

- la capacité fonctionnelle (réponse aux besoins, précision, interopérabilité, 
  conformité)
- facilité d'utilisation 
- fiabilité (tolérance aux pannes)
- performance
- maintenabilité, extensibilité 
- portabilité
