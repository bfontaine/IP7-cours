# Compilation Avancée - Cours 2 : Compilation d'un langage de premier ordre

## Le flot de contrôle

Un programme utilie produit généralement un résultat **en fonction** de ses
entrées.  
On va donc étendre le langage des expressions arithmétiques :  
![p4](img/cours2/2_1.png)  
La dernière ligne correspond à la décision d'une propriété. On
choisit ```R ∈ {<, >, ≤, ≥, =}```.  
Le résultat *v* de l'évaluation d'une expression peut être *n*, *true* ou
*false*.  
Voici la sémantique opérationnelle à grands pas :  
![p5](img/cours2/2_2.png)  

Les valeurs en OCaml :  
```
type value =
| VInt of int
| VBool of bool

exception NotInt

let as_int = function
| VInt x -> x
| _ -> raise NotInt

exception NotBool

let as_bool = function
| VBool b -> b
| _ -> raise NotBool
```
L'AST :  
```
type binop = Add |Mul | Div | Sub
type comparison = Le|Ge|Lt|Gt|Eq
type variable = string
type t =
| Int of int
| Bool of bool
| Binop of binop * t * t
| Let of variable * t * t
| Var of variable
| If of t * t * t
| Dec of comparison * t * t

let comparison = function
| Le -> (<=)
| Ge -> (>=)
| Lt -> (<)
| Gt -> (>)
| Eq -> (=)
```
L'interprète en OCaml :  
```
let rec eval env = function
| Int i -> VInt i
| Bool b -> VBool b
| Binop (op, e1, e2) -> VInt ((binop op) (as_int (eval env e1)) (as_int (eval env e2)))
| Let (x, e1, e2) -> eval (Env.bind env x (eval env e1)) e2
| Var x -> Env.lookup env x
| Dec (c, e1, e2) -> VBool((comparison c) (as_int (eval env e1)) (as_int (eval env e2)))
| If (c, e1, e2) -> if as_bool (eval_env c) then eval env e1 else eval env e2
```


### Typage

Le **typage** permet d'exclure des programmes qu'on sait incorrects par
certaines propriétés, notamment le blocage de certaines expressions.  
Un **jugement de typage** ```Γ ⊢ e:τ``` se lit "sous l'environnement de typage
*Γ*, l'expression *e* a le type *τ*".  
On doit définir des **règles de typage** :  
![p13](img/cours2/2_3.png)  
On note que le type de *x* dans ```let``` peut être déduit de celui de *e1*.  
Typage en OCaml :  
```
let rec type_check env = function
| Var x -> TyEnv.lookup env x
| Int _ -> TInt
| Bool _ -> TBool
| If (cond, e1, e2) ->
  let tycond = type_check env e1 in
  type_check (TyEnv.bind env x ty1) e2
| Binop (_, e1, e2) ->
  let ty1 = type_check env e1 and ty2 = type_check env e2 in
  if (ty1 != TInt || ty2 != TInt) then raise IIITyped;
  TInt
| Dec (_, e1, e2) ->
  let ty1 = type_check env e1 and ty2 = type_check env e2 in
  if (ty1 != TInt || ty2 != TInt) then raise IIITyped;
  TBool
```

### Compilation vers une machine virtuelle

En cas de branchement, on ne veut exécuter qu'un *sous-ensemble* des
instructions, au lieu de toutes les évaluer de la première à la dernière.  
C'est le rôle des **opérateurs de flots de contrôle**.  
Langage de la machine virtuelle :  
![p17](img/cours2/2_4.png)  
Les étiquettes *l* sont des noms pour les emplacements des instructions dans
le programme. *branch* et *branchif* sont des opérateurs de contrôle.  

La pile de la machine virtuelle est influencée par ces opérateurs de
contrôle : ```instruction: (pc, ζv, ζr) -> (pc', ζv', ζr')```.  

Sémantique (petits pas) des instructions :  
![p20](img/cours2/2_5.png)  

Interprète en OCaml :  
```
let rec eval_instr labels vm = function
| Branch l -> goto labels vm l
| Branchif (l1, l2) ->
  let x = Stack.inspect 0 vm.val_stack in
  let vm = {vm with val_stack = Stack.pop vm.val_stack}
  in
  (match x with
    | VBool true -> goto labels vm l1
    | VBool false -> goto labels vm l2
    | _ -> raise NotBool)
| ...
and goto labels vm l =
  eval_instr labels vm (snd (vm.prog.(pos_of_label labels l)))
```

## Les fonctions
