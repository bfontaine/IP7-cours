# Programmation Comparée - Cours 3 : Programmation fonctionnelle 

## Programmation fonctionnelle

La programmation fonctionnelle voit le code comme une **fonction**, qui 
attend et produit des données. Elle se rapproche donc de la programmation 
procédurale, mais en diffère par deux aspects : 

- restriction : un programme ne modifie plus l'état de la machine, mais 
engendre de nouvelles données (persistence)
- extension : le domaine des données inclue désormais les fonctions. 

Une fonction peut être représentée efficacement par une **fermeture** : on 
y capture la valeur des variables dont un fragment de code dépend.  

On peut construire des fonctions  

* par définition : la façon la plus simple, on écrit le code de la fonction.
* par composition : on peut composer deux fonctions afin d'en obtenir une 
  nouvelle qui unit leurs comportements. 
* par spécialisation : via la currification, on transforme une fonction à 
  plusieurs arguments en une fonction qui attend 1 argument et renvoie une 
  fonction.  
* par induction : favori du style fonctionnel, on crée un point fixe des 
fonctions définies par cas sur l'induction. 

Les fonctions écrites dans le style fonctionnel ne dépendent **que de leur 
entrée**, on peut donc faire plus facilement des *tests unitaires* que sur les 
procédures (qui dépendent de l'état du programme).  
De fait, on a la **transparence référentielle** : on peut remplacer une 
sous-expression par son résultat, sans modifier le sens de l'expression 
englobante.  
Enfin, les fonctions définies par induction sont prouvable par un raisonnement 
inductif.  
Pour conclure, la programmation fonctionnelle permet d'écrire des composants 
plus *modulaires*. Elle est *très expressive* grâce aux fonctions de première 
classe. Elle permet aussi d'écrire des programmes efficaces : ils se 
parallélisent facilement, on peut transformer des programmes vu qu'on contrôle 
les effets de bord, et le haut-niveau permet l'implémentation d'algorithmes 
complexes car le développeur se focalise sur les aspects importants. 

## Discipliner l'état

L'**état** brut est modélisé comme une mémoire de taille finie à laquelle 
on accède via des lectures et écritures d'adresses.  
C'est sur ces états qu'un programme assembleur travaille, et c'est au 
programmeur d'organiser l'agencement des données dans un segment mémoire.  
Toutefois, cette gestion manuelle montre ses limites quand les programmes 
deviennent compliqués. On a donc introduit des modèles de mémoire abstraits, 
qui fournissent des *garanties* et des *primitives* : arithmétique des 
pointeurs, gestion d'un tas via ```malloc/free```, structures dynamiques, flots 
de données.  

### Monades 

Une monade **M t** est un type abstrait paramétré représentant un calcul. Elle 
doit être fournie avec au moins deux opérations : 

- unité (*return*) de type ```t -> M t``` qui plonge une valeur de type *t* 
dans le modèle de calcul de la monade
- liaison (*bind*) de type ```M t -> (t -> M u) -> M u``` qui décrit la façon 
dont les calculs se composent

```
(* Monade des listes *)
type 'a m = 'a list
let return x = [x]
let bind l f = List.flatten (List.map f l)
let (>>=) l f = bind l f
```

## Libérer le contrôle 

### Ecrire ses propres opérateurs de contrôle 

Grace aux fonctions d'ordre supérieur, le programmeur n'est plus limité 
aux seules boucles ```while``` pour contrôler le flot. N'importe quel protocole 
peut être défini pour transmettre le contrôle. On se base sur le fait qu'une 
fonction ne doit pas nécessairement rendre la main, mais peut continuer 
son exécution à l'infini. 

#### Co-routines 

Une co-routine est une sous-routine qui prend en main le contrôle et en fait ce 
qu'elle veut : redonner la main à l'appelant, ou donner la main à une autre 
co-routine.  

- ```yield e``` : retourne *e* à l'appelant et mémorise l'endroit où on se 
trouve
- ```resume e``` : redonne la main à la coroutine, qui se poursuit là où on 
l'avait laissée. 

On peut faire des diagrammes *d'états-transitions* de façon modulaire. 

#### Co-structures 

On se base sur la même idée de continuation. On observe la structure pour 
décider si on continue le calcul ou pas. 
```
type ('s, 'x) stream = 
| Stream of ('s -> ('x, 's) step) * 's

and ('x, 's) step = 
| Done 
| Yield of 'x * 's
| Skip of 's

let map f = 
  fun (Stream(next0, s0)) ->
    let next = fun s -> match next0 with
      | Done -> Done
      | Yield (x, s') -> Yield (f x, s')
      | Skip s' -> Skip s'
    in
    Stream (next, s0)
```
Toutefois on a exposé le type de l'état du flux (*step*), et on voudrait le 
cacher. 

#### Types existentiels 

On veut fair oublier au typeur la définition exacte d'un type. On le cache 
en fait dans un module (avec un *record polymorphe*)

### Explorer d'autres stratégies d'évaluation 

### Créer et utiliser de nouveaux modèles de calcul