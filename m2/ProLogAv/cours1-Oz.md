# Programmation Logique Avancée - Cours 1 : Le langage Oz

## Utiliser Oz

Oz est un langage **multi-paradigmes** à typage dynamique : il permet la
programmation fonctionnelle, par objets, logique, concurrente, distribuée...
On le lance avec ```oz``` dans un terminal, qui va lancer *emacs* et deux
fenêtres : le **compilateur** et l'**émulateur**.  
Les principaux raccourcis du mode *oz* :

- C-. C-l : exécuter la ligne
- C-. C-r : exécuter la région
- C-. C-p : exécuter le paragraphe (entre lignes vides)
- C-. C-b : exécuter le tampon

## Déclaration de variables

Déclaration de *X, Y* et *Z* avec comme portée *S* :
```
local X Y Z
in S
end
```
Déclaration de *X, Y* et *Z* globales :
```
declare X Y Z
in S
```
Les noms des variables doivent commencer par une **lettre majuscule**. Elles ne
contiennent pas de type. Ce sont les **valeurs** qui les ont (partie droite
de l'affectation).  
Les types communs sont :

- *Number* (avec comme sous-types *Float* et *Int*, avec comme sous-type *Char*)
- *Record* (avec comme sous-type *Tuple*)
- *Procedure*

Le **moins** s'écrit avec **~**. Les flottants s'écrivent avec un **point**
et supportent la notation scientifique (avec **E** ou **e**). Il n'y a pas de
conversion implicite entre flottants et entiers.

## Littéraux

Il y a deux sortes de littéraux :

- atomes : chaîne alphanumérique commençant par une minuscule, ou une chaîne
arbitraite entre guillemets simples (**'**)
- noms : des mots, engendrables via la procédure **NewName**

## Enregistrements et tuples

Ce sont des étiquettes liées à une séquence de paires *clé-valeur*. Tous les
champs doivent être définis au même moment. On peut avoir la même clé dans
des enregistrements distincts.  
Si les clés sont *1,2,...*, alors on n'a pas besoin des les marquer
explicitement : on a un tuple.
```
tree(key:I value:Y left:LT right:RT)
tutu(1:A 2:B 3:C)
tutu(A B C)
```

Les opérations sur les enregistrements (exemple su *x*):

- accès à la valeur associée à la clé *c* : ```x.c```
- arité (liste des clés) : ```{Arity x}```
- étiquette : ```{Label x}```
- ajouter une paire : ```{AdjoinAt R F X}``` donne R avec en plus
l'association *F->X* (écrase la valeur si la clé était déjà présente)

## Listes

Le type des listes est un type secondaire avec deux syntaxes :  
```
1|2|3|nil
[1 2 3]
```
Les chaînes sont des listes de caractères :  
```
"OZ 3.0"
[&O &Z &  &3 &. &0]
[79 90 32 51 46 48]
```

## Procédures

Les **procédures** (comme les *méthodes*) s'appliquent avec ```{P a1 a2}```.  
Il faut lier une procédure à un identificateur, on doit donc le déclarer
avant. On lie ensuite statiquement avec ```proc {ID A B} ... end```.  
```
declare Max in
 local X Y Z in
  proc {Max X Y Z}
   if X >= Y then Z = X else Z = Y end
  end
  X = 5
  Y = 10
  {Max X Y Z}
  {Browse Z}
 end
end
```

## Filtrage par motif

```
case E of P1 then S1
[] P2 then S2
[] P3 then S3
else S
end
```
La partie ```else``` est optionnelle.  
Les variables déclarées dans le motif sont locales (sauf si on les précède
de **!**). 
