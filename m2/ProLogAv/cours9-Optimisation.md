# Programmation Logique Avancée - Cours 9 : Optimisation

## Branch And Bound

On lance une recherche spécialisée avec deux arguments : le script, et une
fonction de comparaison entre deux solutions du script.  
La machine va checher une solution du script optimale (ici: maximale).  
A chaque fois qu'une solution *X* est trouvée, on ajoute dans les autres noeuds
de l'arbre un propagateur spécifiée que la solution doit être meilleure de *X*.

### Exemple : photo de groupe

Reprendre l'exemple du cours précédant et enlever la ligne
```
{FD.distribute generic(order:naive value:max) [Satisfaction]}
```  
On doit définir la fonction de comparaison :
```
declare
proc {PhotoOrder Old New}
  Old.satisfaction <: New.satisfaction
end
```
On lance la recherche avec des procédures spéciales :  
```
{ExploreBest Photo PhotoOrder}
{Browse {SearchBest Photo PhotoOrder}}
```
