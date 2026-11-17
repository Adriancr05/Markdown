# Prolog

Realizar un predicado `'generado_int/3'` que genere en sucesivas resatisfacciones los enteros, primer argumento, que estén dentro de un intervalo determinado por el límite inferior, entero del segundo argumento, y el límite superior, entero del tercer argumento. Por ejemplo, `generado_int(X, 2, 5)` habría de devolver `X=2; X=3; X=4; X=5`.

```prolog
generado_int(X, Inf, Sup) :- Inf =< Sup, X = Inf.
generado_int(X, Inf, Sup) :- Inf < Sup, Inf1 is Inf + 1, generado_int(X, Inf1, Sup).
```

---

Realizar los predicados `'mcm/3'` y `'mcd/3'` que devuelva en el primer argumento el **mínimo común múltiplo** y el **máximo común divisor** respectivamente de los dos últimos argumentos. Por ejemplo, `mcm(X, 2, 5)` habría de devolver `X=10`.

```prolog
% mcd
mcd(X, A, 0) :- X = A.
mcd(X, A, B) :- B > 0, R is A mod B, mcd(X, B, R).

% mcm
mcm(X, A, B) :- mcd(D, A, B), X is A * B // D.
```

---

Definir la relación `'min_lista(+L, ?X)'` que se verifique si **X** es el mínimo de la lista de números **L**. Por ejemplo, `min_lista([9, 3, 1, 5] , X)` debe dar `X = 1`.

```prolog
min_lista([X], X).
min_lista([H|T], M) :- T \= [], min_lista(T, M1), M is min(H, M1).
```

---

Definir la relación `'cuadrado(+L1, ?L2)'` donde **L1** es una lista constituida por números y que se verifique si **L2** está constituida por los números equivalentes de la lista **L1** elevados al cuadrado. Por ejemplo, `cuadrado([1, 2, 3], L2)` debe dar `L2 = [1, 4, 9]`.

```prolog
cuadrado([], []).
cuadrado([H|T], [H2|T2]) :- H2 is H * H, cuadrado(T, T2).
```

---

Definir la relación `'entrelaza(?L1, ?L2, ?L)'` que se verifique si la lista **L** está constituida por los elementos entrelazados de las listas **L1** y **L2**, que han de tener igual longitud. Por ejemplo, `entrelaza([1, 3, 5], [2, 4, 6], L)` debe dar `L = [1, 2, 3, 4, 5, 6]`.

```prolog
entrelaza([], [], []).
entrelaza([H1|T1], [H2|T2], [H1,H2|T]) :- entrelaza(T1, T2, T).
```

---

Definir la relación `'subtramo(?L1, ?L2)'` que se verifique si la lista **L1** es un subtramo ordenado listas **L2**. Por ejemplo, `subtramo([3, 4, 5], [1, 2, 3, 4, 5, 6])` debe dar `true`.

```prolog
subtramo([],_).
subtramo([X],[X|_]).
subtramo([X,Y|L1],[X,Y|L2]) :- subtramo_aux([Y|L1],[Y|L2]).
subtramo(L1, [_|L2]) :- subtramo(L1, L2).
```

---

Definir la relación `'previo(?X, ?Y, +L)'` que se verifique si **X** está antes que **Y** en la lista **L**. Por ejemplo, `previo(b, e, [a, b, c, d, e, f])` debe dar `true`.

```prolog
pertenece(X, [X|_]).
pertenece(X, [_|T]) :- pertenece(X, T).

previo(X, Y, [X|T]) :- pertenece(Y, T).
previo(X, Y, [_|T]) :- previo(X, Y, T).
```

---

Definir la relación `'aplana(+L1, ?L2)'` que se verifique si **L2** es la lista equivalente a **L1** donde se han eliminado todos su anidamientos. Por ejemplo, `aplana([a, b, [c, [d], [], e], f], L)` debe dar `L = [a, b, c, d, e, f]`.

```prolog
aplana([], []).
aplana([H|T], [H|LT]) :- \+ is_list(H), aplana(T, LT).
aplana([H|T], L) :- is_list(H), aplana(H, LH), aplana(T, LT), append(LH, LT, L).
```

---

Definir la relación `'invertir(?L1,?L2)'` que se verifique si la lista **L2** es equivalente a la **L1** pero con los elementos en orden inverso. Utilizar la técnica del acumulador para ello. Por ejemplo, `invertir([9, 3, 1, 5] , L)` debe dar `L = [5, 1, 3, 9]`.

```prolog
invertir(L1, L2) :- invertir_acc(L1, [], L2).
invertir_acc([], A, A).
invertir_acc([H|T], A, L) :- invertir_acc(T, [H|A], L).
```

---

Definir la relación `'suma(+L,?X)'` y `'suma_acc(+L,?X)'` que se verifiquen si **X** es la suma de los elementos de la lista de números **L**. Ha de implementarse el primero sin acumulador y el segundo con. Por ejemplo, `suma([9, 3, 1, 5], X)` debe dar `X = 18`.

```prolog
% sin acumulador
suma([X], X).
suma([H|T], X) :- suma(T, XT), X is H + XT.

% con acumulador
suma_acc(L, X) :- suma_acc_aux(L, 0, X).
suma_acc_aux([], A, A).
suma_acc_aux([H|T], A, X) :- A1 is A + H, suma_acc(T, A1, X).
```
