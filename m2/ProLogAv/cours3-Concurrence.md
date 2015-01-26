# Programmation Logique Avancée - Cours 3 : Concurrence

## Rappel

* La mémoire (*store*) est un système résolu d'équations
* L'exécution consiste en une suite de paires (environnement, instruction)
* L'environnement lie des identificateurs ) des variables de mémoire
* La mémoire lie des variables à des valeurs (éventuellement partielles)
* La liaison est statique

## Listes de différences

Une **liste de différence** est une paire de listes *(l, r)* où *r* est un
reste de *l*. Les deux peuvent être des listes partielles.  
Cette paire représente une liste étant la différence entre *l* et *r*. Si on
les voit comme des pointeurs, ce sont les éléments entre les positions des deux
pointeurs.

## Queues

Une **queue** est représentée par sa longueur, et une liste de différence pour
son contenu : ```q(0 X X)``` ou ```q(2 [a b] X)```.  

## Mémoire

La mémoire contient deux modèles de liaisons :

* La liaison de variable à variable (```x=y```), qui ne doivent pas être
cycliques. En suivant toutes les liaisons, on obtient son représentant
*v(x)* (égal à *v(y)*).
* La liaison de variable à structure (```x = f(x1..xn)```), qui permet les
cycles mais où toutes les variables (à la fois liée et dans la structure) sont
des représentants (ie *x = v(x)*). Toutes les variables à gauche doivent être
différentes.
```
x1 = x3
x2 = x3
y1 = y2
x3 = f(y2, a)
y2 = g(b, z)
```

### Tell

On peut ajouter une équation à la mémoire avec l'opération ```tell```, qui
correspond en gros à une instruction d'affectation de variable. Si les
équations deviennent incohérentes, l'opération échoue. On a trois cas.

#### Ajouter x = y

Si *v(x)=v(y)* alors il n'y a rien à faire.  
Sinon on ajouter à la mémoire l'équation *v(x)=v(y)* ou l'inverse. Il faut
normaliser les liaisons variable-structure. Si la mémoire contient
```
v(x) = f(x1 .. xn)
v(y) = g(y1 .. ym)
```
alors
```
tell f(x1 .. xn) = g(y1 .. ym)
```

#### Ajouter x = f(x1 .. xn)

Si *v(x)* n'est pas liée à une structure, on ajoute
l'équation ```v(x) = f(v(x1)  .. v(xn))```.  
Si il y a déjà une liaison *v(x) = g(y1 .. ym)*
alors ```tell f(v(x1) .. v(xn)) = g(y1 .. ym)```

#### Ajouter f(x1 .. xn) = g(y1 .. ym)

Si f≠g ou n≠m, cela échoue.  
Sinon, ```tell x1 = y1, ..., tell xn = yn```.  

### Ask

L'opération ```ask``` va faire une tentative de ```tell``` pour vérifiée qu'une
équation est impliquée logiquement par la mémoire.  
Si le ```tell``` échoue, il y a une **incohérence**. Si il réussit, il y a
deux cas de figure : si pour toute nouvelle liaison entre variables *z1 = z2*,
les deux variables étaient auparavant liées à des structures, on a
l'**implication**. Sinon **on ne sait pas** et le thread est suspendu.  
```
x = f(x1), y=f(y1), x1=a, y1=b
ask(x=y)
% tell x=y échoue, c'est incohérent

x=f(y,z),y=f(x,z)
ask(x=y)
% tell n'échoue pas et ajoute x=y.
% x et y sont liées à des structures, au départ -> oui

x=f(y,z),y=f(x,y)
ask(x=y)
% tell x=y n'échoue pas et ajoute x=y et y=z
% z n'est pas liée à une structure -> on ne sait pas encore
```

### Mémoire monotone

Pendant ces calculs, la mémoire croît de façon *monotone*. Si à un moment
*t1* la mémoire vaut *σ1*, et qu'à *t2>t1* elle vaut *σ2*, alors **σ2 ⊨ σ1**.  
Les réponses de *tell* et *ask* sont dûes à des implications de la mémoire :

* Echec de *tell(s=t)* :  σ ⊨ ¬(s=t)
* *ask(s=t)* affirmatif : σ ⊨ ∀x1,..xn s=t
* *ask(s=t)* négatif :σ ⊨ ∀x1,..,xn s≠t

## Programmation concurrente en dataflow

On ajouter un nouveau thread avec ```thread <s> end```. Il y a plusieurs piles
d'exécution, mais tous les threads utilisent la même mémoire. Toute requête est
exécutée dans son propre thread (y comprise *Browse*).  

Certaines instructions peuvent bloquer quand la mémoire n'est pas suffisante
pour conclure. Dans ce cas, le thread est **suspendu**.  
Si un thread bloque et attend des informations sur une variable qui n'est plus
accessible, le *garbage collector* va détruire le thread.  

Il est possible de capturer les erreurs :
```
local X Y in
  try
    thread X=a end
    thread X=b end
  catch failure then case X
    of a then Y=1
    [] b then Y=2
    end
  end
end
```

## Producteurs et consommateurs

Oz utilise la **synchronisation par offre** : le consommateur doit attendre le
producteur.  
On veut créer des thread paresseux (exécutés seulement quand nécessaire). Le
mot-clé correspondant est ```ByNeed```.  

On peut aussi faire des listes paresseuses, avec ```lazy``` :  
```
local
  fun lazy {GenAux Curr}
    Curr|{GenAux Curr+1}
  end
in
  {GenAux 2}
```
