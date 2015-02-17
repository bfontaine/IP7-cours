# Programmation Logique Avancée - Cours 7 : Blocage de propagateurs & Recherche multidimensionnelle

## N Reines avec les contraintes de domaine fini

## Propagateurs pour la diségalité

* {FD.distinct *Dv} : tous les éléments de *Dv* sont distincts deux à deux
* {FD.distinctOffset *Dv +Iv} : toutes les sommes *D<sub>i</sub>*
et *I<sub>i</sub>* sont distinctes deux à deux
* {FD.atMost *D *Dv +I} : au plus *D* éléments de *Dv* sont égaux à *I*
* {FD.atLeast *D *Dv +I}
* {FD.exactly *D *Dv +I}

## N Reines

```
fun {Queens N}
  proc {$ Row}
    L1N = {MakeTuple c N}
    LM1N = {MakeTuple c N}
  in
    {FD.tuple queens N 1#N Row}
    {For 1 N 1 proc {$ I} L1N.I=I LM1N.I=~I end}
    {FD.distinct Row}
    {FD.distinctOffset Row LM1N}
    {FD.distinctOffset Row L1N}
    {FD.distribute generic(value:mid) Row}
  end
end
```

L'appel ```{Queens 8}``` donne le script pour 8 dames :
```
{Browse {SearchAll {Queens 8}}}
{ExploreOne {Queens 8}}
```

## Propagateurs qui bloquent

```
declare
proc {D L}
  X Y Z
in
  L=X#Y
  [X Y]:::1#9
  Z::2#4
  X mod Z = Y
  {FD.distribute ff [X Y Z]}
end

{Browse {SearchAll D}} % donne une paire de deux domaines
```
Les propagateurs *FD.plus* et *FD.modI* peuvent bloquer.  
Les solutions possibles sont d'énumérer avant de tester, ou d'utiliser des
propagateurs.

```
% énumération
declare
proc {D L}
  X Y Z
in
  L=X#Y
  [X Y]:::1#9
  Z::2#4
  {FD.distribute ff [Z]}
  X mod Z = Y
  {FD.distribute ff [X Y]}
end

{Browse {SearchAll D}} % donne 3 paires de domaines
```

Il faut encore utiliser un propagateur : ```X mod Z = Y  => {FD.modI X Z Y}```  
Une solution alternative :  
```
declare
proc {D L}
  X Y Z
in
  L=X#Y
  [X Y]:::1#9
  Z::2#4
  local P1 P2 in
    [P1 P2]:::0#9
    {FD.times Z P1 P2}%P2 = Z*P1
    P2+Y =: X
    Y <: Z
    {FD.distribute ff [X Y Z]}
  end
end
```

## Recherche multidimensionnelle

**Exemple : Colloque**  
On veut faire l'emploi du temps d'un colloque selon un nombre donné de
sessions. L'emploi du temps consiste en plusieurs créneaux. Plusieurs sessions
peuvent se faire en parallèle (avec un nombre maximal en contrainte). On peut
contraindre une session *n* à être avant une session *m* ou ne pas être
parallèle à *m*.  
L'objectif est d'avoir une solution qui occupe un nombre minimal de créneaux.  

On fait deux recherches : d'abord le nombre de créneaux, puis on les affecte
aux sessions.  

```
Data = data(nbSessions:11
         nbParSessions:3
           constraints: [
           before(4 11) before(5 10) before(6 11)
           disjoint(1 [2 3 5 7 8 10])
           disjoint(2 [3 4 7 8 9 11])
           disjoint(3 [5 6 8])
           disjoint(4 [6 8 10])
           disjoint(6 [7 10])
           disjoint(7 [8 9])
           disjoint(8 [10])
           ] )
```
On va engendrer les propagateurs à partir des données (*constraints*). Il faut
faire attention à l'ordre : *FD.int* suspend quand les bornes ne sont pas des
entiers.  
```
declare
Colloque = data(nbSessions:.. nbParSessions:.. constraints:..)

declare
fun {Conference Data}
  NbSessions = Data.nbSessions
  NbParSessions = Data.nbParSessions
  Constraints = Data.constraints
  MinNbSlots = NbSessions div NbParSessions
in
  proc {$ Plan}
    NbSlots = {FD.int MinNbSlots#NbSessions}
  in
    {FD.distribute naive [NbSlots]}
    {Browse NbSlots}
    %% Plan: Session --> Slot
    {FD.tuple plan NbSessins 1#NbSlots Plan}
    %% at most NbParSession per slot
    {For 1 NbSlots 1
    proc ($ Slot) {FD.atMost NbParSessions Plan Slot} end}
    %% constraints
    {ForAll Constraints
    proc {$ C}
      case C
      of before(X Y) then
        Plan.X <: Plan.Y
      [] disjoint(X Ys) then
        {ForAll Ys proc {$ Y} Plan.X \=: Plan.Y end}
      end
    end}
    {FD.distribute ff Plan}
  end
end
{Browse {SearchOne {Conference Colloque}}}
```
