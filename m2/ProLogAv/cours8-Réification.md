# Programmation Logique Avancée - Cours 8 : Réification

## Contraintes réifiées

Les **contraintes réifiées** permettent d'exprimer des combinaisons entre
contraintes au delà de la conjonction : négation, équivalence, implication,
disjonction.  
On peut exprimer des contraintes plus souples (*soft constraints*), opposées
aux contraintes dures qui doivent impérativement être satisfaites.  
On peut ainsi exprimer le fait qu'on veuille satisfaire un nombre maximal de
contraintes souples. C'est obligatoire si le problème est *sur-contraint*.

## Propagateurs pour contraintes réifiées

La **réification** d'une contrainte est la formule logique :  
```(C ↔ x =1)  avec D(x)={0,1} et x∉vars(C)```  

* Si la mémoire implique *x=1* alors le propagateur devient un propagateur pour
*C*.
* Si la mémoire implique *x=0* alors le propagateur devient un propagateur pour
*¬C*
* Si la mémoire implique *C* alors le propagateur pose *x=1* et disparaît
* Si la mémoire n'est pas consistante avec *C* alors le propagateur pose *x=0*
et disparaît

Quelques propagateurs Oz :

* ```(X<:Y)=B``` (*B* vaut 1 si *X* est inférieur à *Y*)
* ```(X+Y+Z=:0)=B```
* ```(X\=:Y)=B```
* ```(X::0#10)=B```
* ```{FD.reified.distance X Y '=:' Z B}``` (il existe une version réifiée pour
  chaque contrainte)
* ```{FD.conj X Y Z}``` (pour *(X∧Y)=Z*), puis *disj*, *impl*, *equi*, *nega*

## Variables et contraintes pseudo-booléennes

On appelle **variable booléenne** (ou *variable 0/1*) une variable dont le
domaine est [0,1].  
Une contrainte est **pseudo-booléenne** si les variables sont booléennes
mais qu'on les utilise dans une expression dont la valeur n'est pas forcément
booléenne : ```X1 + X2 + X3 + X4 >= 2```.  
On peut ainsi avoir des contraintes sur *le nombre de contraintes satisfaites*.  

## Exemple : la photo de groupe

On veut prendre une photo avec Betty, Chris, Donald, Fred, Gary, Mary et Paul
avec les contraintes suivantes :

* Betty veut être à côté de Mary et Gary
* Chris veut être à côté de Betty et Gary
* Fred veut être à côté de Mary et Donald
* Paul veut être à côté de Fred et Donald

Les préférences de chacun sont contradictoires et le problème insoluble.  
On va utiliser des contraintes souples et tenter de maximiser le nombre de
préférences qui sont satisfaites.  
```
declare
proc {Photo Root}
  Persons = [betty chris donald fred gary mary paul]
  Prefs = [betty#gary betty#mary
           chris#betty chris#gary
           fred#mary fred#donald
           paul#fred paul#donald]
  Alignment % solution
  Satisfaction % nb de préférences satisfaites
  fun {Satisfied P#Q}
    {FD.reified.distance Alignment.P Alignment.Q '=:' 1}
    % contrainte réifiée : renvoie 1 si P et Q sont côte à côte
  end
in
  Satisfaction :: 0#{Length Preferences}
  Alignment = {FD.recored alignment Persons 1#{Length Persons}}
  Root = r(satisfaction: Satisfaction
           alignment : Alignment)
  {FD.distinct Alignment} % on ne veut pas 2 personnes à la même position
  {FD.sum {Map Preferences Satisfied} '=:' Satisfaction}
  % on crée une variable 0/1 par préférence.
  % chacune de ces variables est contrainte au fait que la préférence
  % correspondante est satifaite.
  % Donc Satisfaction est contrainte au nombre de préférences satisfaites
  Alignment.fred <: Alignment.betty % pour empêcher la symétrie
  {FD.distribute generic(order:naive value:max) [Satisfaction]}
  {FD.distribute split Alignment}
end

{Browse {SearchOne Photo}}
{ExploreOne Photo}
```
