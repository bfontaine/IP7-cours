# Typage - Cours 4 : Extensions de Mini-ML

## N-uplets

On a déjà les paires, il suffit de généraliser les constructeurs
de valeurs *(__,__)* et de type *__ ⨯ __* :  
![4.1-1](4_1.png)  
On donne des opérateurs de projection **proj<sub>i,n</sub>** pour extraire la
*i-ème* composante du *n*-uplet.  
Les règles de typage et de réduction sont :  
![4.1-1](4_2.png)  

## Types concrets

Aussi appelés **types sommes** ou **variants**. Ils se présentent sous la forme
de plusieurs cas (les *constructeurs*) qui portent d'éventuels *arguments*.  
La forme générale : ```type (α1, .., αp) t = C1 of τ1 | ... | Cp of τp```.  
On veut évidemment **L(τi) ⊆ {α1, .., αp}**.  
Tout ceci étend le langage :  
![4.2-2](4_3.png)  
Ici, **F<sub>t</sub>** est l'opérateur de filtrage du type *t*.  
Sa règle de réduction est ![4.2-2](4_4.png)

Les types des opérateurs *C<sub>k</sub>* et *F<sub>t</sub>* :  
![4.2-2](4_5.png)  

## Enregistrements déclarés

Un *record* est un *n*-uplet où les composantes sont nommées. Ils sont traités
comme les types concrets. On déclare un enregistrement avec la forme générale :  
![4.3-2](4_6.png)  
Il faut étendre le langage :  
![4.3-2](4_7.png)  
où *.étiq* sont les fonctions d'accès aux champs (en fait ```a.e``` est
équivalent à ```.e(a)```).  
L'opérateur *M<sub>t</sub>* permet de construire l'enregistrement.  
Les règles de typage et de réduction :  
![4.3-2](4_8.png)  

## Contraintes de types

L'expression ```(a: τ)``` s'évalue comme *a* mais le force à avoir le type
*τ*.  En fait, ```(a: τ)``` est vue comme l'application d'un opérateur
```contr_τ(a)``` qui s'évalue :  
![4.3-2](4_9.png)  
Si *τ* contient des variables de types, on ne force pas *a* à avoir *τ*, mais
une instance de *φ(τ)*.  
