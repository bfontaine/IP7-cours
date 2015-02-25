# Programmation Logique Avancée - Cours 2 : Le modèle d'exécution d'Oz

## Fonctions

Les **fonctions** sont des raccourcis pour les *procédures*.
```fun {P A B ... Z} i1 ... im expr end``` est un raccourci
pour ```proc {F A B .. Z R} i1 ... im R=exp end```.  
Exemple :
```
fun {Max X Y} if X > Y then X else Y end end
proc {Max X Y Z} if X>Y then Z=X else Z=Y end end
```
Un appel de procédure est une instruction, un appel de fonction est une
expression et peut donc être utilisé là où on attend une expression.  
Simplement, si la procédure *P* attend *(n+1)* arguments, alors la fonction *P*
prend *n* arguments. C'est équivalent à
```
local R in
  {P A .. Z R}
  context[R]
end
```

Il n'y a pas d'application partielle : il faut la simuler avec une autre
fonction, qui appelle la fonction de base avec quelques arguments.

### Binding

Certaines instructions entre ```declare/local``` et ```in``` :  
```
local
  P = proc {$ Y} {Browse Y+Y} end
  X = 5
in
  {P X}
end
```
On a déclaré toutes les variables sur le côté gauche de l'équation, et la
variable directement après ```proc/fun```.  
Ici, c'est *Y* et *X* qui ont été déclarées.

### Procédures et fonctions anonymes

L'utilisation de **$** à la 1ère position de l'entête d'une procédure/fonction
crée une expression dont la valeur est cette procédure/fonction. C'est ainsi
qu'on fait des **fonctions anonymes**.  
```
declare
fun {Map F L}
  case L of
    nil then nil
  [] H|R then (F H)|(Map F R)
  end
end

{Browse {Map fun ($ X) X+X end [1 2 3 4]}}
% ici, la fonction de somme est anonyme et passée à Map
```

## Mini-Oz

**Mini-Oz** est le langage noyau. Il est utilisé seulement pour définir la
sémantique.  
Une instruction ```<s>``` peut être :

- ```skip```
- ```<s>1 <s>2```
- ```local <x> in <s> end```
- ```<x> = <t>``` (*t* peut être une procédure)
- ```if <x> then <s>1 else <s>2 end```
- ```case <x> of <p> then <s>1 else <s>2 end```
- ```{ <x> <y>1 .. <y>n}```

Un terme peut être :

- un identificateur ```x```
- un terme composé ```f(<t>1 .. <t>n)```
- une procédure ```proc {$ y1 .. yn} <s> end```

Donc pas d'enregistrements (même si l'algorithme d'unification se généralise
aux enregistrements).  

### Raccourcis syntaxiques

On peut traduire le langage *Oz* complet vers *mini-Oz*.  

*local avec plusieurs variables* :
```
local X Y Z in <i> end
% traduit
local X in
  local Y in
    local Z in
      <i>
    end
  end
end
```
*declare* :
```
declare X in <i>
% traduit
local X in <i> end
```
*local avec affectation d'une valeur* :
```
local proc {P X} <i1> end in <i2> end
% traduit
local P in
  P = proc {$ X} <i1> end
  <i2>
end
```
*termes à la place des variables* :
```
if <t> then <i1> else <i2> end
% traduit, où X est un nouvel identificateur
local X in
  X = <t>
  if X then <i1> else <i2> end
end
```
*instructions d'unification* :
```
s=t
% traduit
local X in
  X = s
  X = t
end
```
