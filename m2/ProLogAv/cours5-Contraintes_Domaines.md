# Programmation Logique Avancée - Cours 5 : Contraintes de domaine fini en Oz

## Domaines, contraintes et propagateurs

Les contraintes sur un domaine fini associent à toute variable, un **choix
fini** de valeurs.  
C'est utile pour les problèmes où il faut trouver une affectation à des
variables selon des contraintes.  

Un *domaine D* est une fonction partielle qui associe à une variable, un
ensemble fini d'entiers. On donne *vars(D)* l'ensemble des variables pour
lesquelles *D* est défini.
Il y a un nouveau type : l'*ensemble fini* (*finite domain*).

* Un domaine a **échoué** si il existe une variable dont l'ensemble de valeurs
est vide (aucune valeur possible).
* Un domaine **fixe** une variable si elle n'a qu'une valeur possible
* Un domaine *D1* est **plus fort** qu'un domaine *D2* si
*vars(D1)=vars(D2)* et *D1(X) ⊆ D2(X)* pour tout *X ∈ vars(D1)*

Une **contrainte** est une formule logique, une *formulation mathématique* du
problème. Elle peut prendre la forme d'une équation, inégalité, diséquation...

Un **propagateur** est un thread qui peut renforcer le domaine, c'est la
*réalisation opérationnelle* d'une contrainte.  

Pour résoudre un problème de contraintes :

* on applique les propagateurs tant que possible, jusqu'à un point fixe
* si on n'échoue pas et qu'il reste une variable *X* non fixée, on crée un
noeud dans l'arbre de recherche et on coupe le domaine de *X* en deux.
* quand on descend sur une branche, on ré-itère.

## Définir les domaines

En Oz, on peut donner le domaine avec ```X::1#9``` ou ```{FD.int 1#9 X}```.  
Cas spécial : imposer à *X* le domaine [0..Max] : ```{FD.decl X}```.  
*L* est une liste de *I* variable de domaine fini : ```{FD.list I 1#9 L}```.  
*T* est un i-uplet de variables, de label *L* : ```{FD.tuple L I 1#9 T}```.  
*R* est un enregistrement de variables, avec une liste *F* de features et un
label *L* : ```{FD.record L F 1#9 R}```.  
Restreindre les domaines de toutes les variables d'un vecteur *V* :
```V:::1#9``` ou ```{FD.dom 1#9 V}```

## Propagateurs en Oz

Les progateurs de bornes arithmétiques : ```=: \=: <: =<: >: >=:```.  
Propagation pour la distance : ```{FD.distance X Y Prop Z}```.  
Propager la différence des valeurs : ```{FD.distinct V}```.  
Propager la différence d'un vecteur *V* par rapport à un décalage *D* :
```{FD.distinctOffset V D}``` avec V.i+D.i ≠ V.j+D.j.  

## Script

Pour résoudre des contraintes de domaine fini, on écrit un **script** pour un
problème donné : c'est une procédure à un seul argument (la *racine*). Le
script doit lier sa racine à une solution du problème (quand l'arbre de
recherche est construit).
```
proc {MonScript Res}
  % déclaration de variables
  sol(X Y)=Res
in
  % définition des domaines
  X::1#9
  Y::1#9
  % propagateurs
  X=<:Y
  % distribution
  {FD.distribute naive Res}
end
{Browse {SearchAll MonScript}}
{ExploreAll MonScript}
```
ou
```
proc {MonScript Res}
  local
    X Y
  in
    Res=sol(x:X y:Y)
    ...
  end
end
```
