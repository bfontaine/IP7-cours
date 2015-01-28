# Typage - Cours 1 : Introduction

Un **système de type** est une méthode *syntaxique* effective pour prouver
l'absence de certains *comportements* de programmes grâce à la *classification*
des phrases selon les *types de valeurs* qu'elles calculent.  
On retrouve généralement les *types de base*, les *structures* de données, et
les *fonctions*.  

Le typage peut être **dynamique** (à l'exécution) ou **statique** (à la
compilation). Dans ce dernier cas, on veut éviter d'interdire trop de
programmes pourtant corrects.  

On cherche certaines garanties de correction :

- préservation des types (*subject reduction*) ou évolution par sous-typage
- absence de situation aberrant (*progression*)
- parfois la *terminaison*

Le typage a plusieurs intérêts :

- il détecte des erreurs
- il donne plus de sûreté au langage
- définit le niveau d'abstraction
- permet la documentation
- facilite la maintenance
- implique une compilation efficace
- correspond au côté *safe* du langage (typage faible, dans lequel on
peut caster, ou typage fort, accordant souvent des *backdoor*)

Parfois le typage est insuffisant :

- en C : ```if (x=0) ...``` mélange booléen et entier
- en OCaml : ```let a = Array.make 10 0 in a.(10)``` provoque un dépassement de
bornes à l'exécution

Le typage peut être **polymorphe**, c'est à dire plus ou moins *générique*. Un
typage **monomorphe** implique la *surcharge* de fonctions.  
