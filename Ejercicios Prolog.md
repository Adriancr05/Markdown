# Ejercicios Prolog
---
## Índice

- [Ejercicios 1](#ejercicios-1)
- [Ejercicios 2](#ejercicios-2)
- [Ejercicios 3](#ejercicios-3)
- [Ejercicios 4](#ejercicios-4)
- [Ejercicios 5](#ejercicios-5)

---

## Ejercicios 1

```prolog
% Base de datos de hechos
femenino(petra).
femenino(carmen).
femenino(maria).
femenino(rosa).
femenino(ana).
femenino(cande).
masculino(angel).
masculino(pepe).
masculino(juan).
masculino(pedro).
masculino(enrique).
masculino(raul).
masculino(alfonso).
progenitor(petra, juan).
progenitor(petra, rosa).
progenitor(carmen, pedro).
progenitor(maria, ana).
progenitor(maria, enrique).
progenitor(rosa, raul).
progenitor(rosa, alfonso).
progenitor(rosa, cande).
progenitor(angel, juan).
progenitor(angel, rosa).
progenitor(pepe, pedro).
progenitor(juan, ana).
progenitor(juan, enrique).
progenitor(pedro, raul).
progenitor(pedro, alfonso).
progenitor(pedro, cande).
```

---

### Pregunta 1

Realizar las reglas necesarias para definir los siguientes predicados distinguiendo el sexo:
- `hijo/2, hija/2`
- `nieto/2, nieta/2`

```prolog
% hijo/2, hija/2
hijo(X, Y) :- progenitor(Y, X), masculino(X).
hija(X, Y) :- progenitor(Y, X), femenino(X).

% nieto/2, nieta/2
nieto(X, Y) :- progenitor(V, X), progenitor(Y, V), masculino(X).
nieta(X, Y) :- progenitor(V, X), progenitor(Y, V), femenino(X).
```

---

### Pregunta 2

¿Qué dificultades encuentras al tratar de definir reglas para los siguientes predicados con lo dado hasta ahora?
- `hermano/2, hermana/2`
- `sobrino/2, sobrina/2`
- `primo/2, prima/2`

```prolog
% hermano/2, hermana/2
hermano(X, Y) :- progenitor(P, X), progenitor(P, Y), X \= Y, masculino(X).
hermana(X, Y) :- progenitor(P, X), progenitor(P, Y), X \= Y, femenino(X).
```

Aparecen dos problemas: Juan y rosa tienen los mismos dos progenitores (angel y petra), así que hermana(rosa, juan) se satisface dos veces, una por cada progenitor común; y la regla da true si comparten algún progenitor, para exigir ambos habría que complicar la regla.

```prolog
% sobrino/2, sobrina/2
sobrino(X, Y) :- progenitor(P, X), (hermano(P, Y) ; hermana(P, Y)), masculino(X).
sobrina(X, Y) :- progenitor(P, X), (hermano(P, Y) ; hermana(P, Y)), femenino(X).
```

Se hereda el problema de los duplicados y además solo cubrimos sobrinos consanguíneos. Si Y es hermano del cónyuge de P, no hay forma de representarlo porque no existe un hecho pareja/2 o casado/2.

```prolog
% primo/2, prima/2
primo(X, Y) :- progenitor(P, X), progenitor(Q, Y), (hermano(P, Q) ; hermana(P, Q)), masculino(X).
prima(X, Y) :- progenitor(P, X), progenitor(Q, Y), (hermano(P, Q) ; hermana(P, Q)), femenino(X).
```

Se multiplican las respuestas repetidas, porque hay varias formas de llegar a la misma pareja.

---

## Ejercicios 2

---

### Pregunta 1

Realizar un predicado `'pertenece/3'` que detecte si un número real (primer argumento) pertenece al intervalo en la recta real determinado por el límite inferior, segundo argumento, y el límite superior, tercer argumento. Por ejemplo, `pertenece(5.5, 1.3, 7.2)` habría de devolver `true`.

```prolog
% pertenece
pertenece(X, Inf, Sup) :- X >= Inf, X =< Sup.
```

---

### Pregunta 2

Realizar un predicado `'generado_int/3'` que genere en sucesivas resatisfacciones los enteros, primer argumento, que estén dentro de un intervalo determinado por el límite inferior, entero del segundo argumento, y el límite superior, entero del tercer argumento. Por ejemplo, `generado_int(X, 2, 5)` habría de devolver `X=2; X=3; X=4; X=5`.

```prolog
% generado_int

generado_int(X, Inf, Sup) :-
  Inf =< Sup,
  X = Inf.
  
generado_int(X, Inf, Sup) :-
  Inf < Sup,
  Inf1 is Inf + 1,
  generado_int(X, Inf1, Sup).
```

---

### Pregunta 3

Realizar los predicados `'mcm/3'` y `'mcd/3'` que devuelva en el primer argumento el **mínimo común múltiplo** y el **máximo común divisor** respectivamente de los dos últimos argumentos. Por ejemplo, `mcm(X, 2, 5)` habría de devolver `X=10`.

```prolog
% mcd
mcd(X, A, 0) :- X = A.
mcd(X, A, B) :- B > 0, R is A mod B, mcd(X, B, R).

% mcm
mcm(X, A, B) :- mcd(D, A, B), X is A * B // D.
```

---

### Pregunta 4

Realizar un programa prolog para el cálculo del día de la semana para fechas modernas mediante el [método de Lewis Carroll](http://www.xtec.cat/~aparra1/astronom/articulos/calendars/051act01.htm). Ha de generarse un predicado `'dia_semana/4'` cuyos tres primeros argumentos sean números con el día, mes y año de una fecha, y el último argumento debe instanciarse al átomo del día de la semana, uno entre `lunes, martes, miercoles, jueves, viernes, sabado, domingo`, correspondiente a dicha fecha, por ejemplo:

- `? - dia_semana(31, 12, 2022, X).`
- `X = sabado`

```prolog
% Valor de cada mes según Carroll (mes, valor)
mes_valor(1, 0).
mes_valor(2, 3).
mes_valor(3, 3).
mes_valor(4, 6).
mes_valor(5, 1).
mes_valor(6, 4).
mes_valor(7, 6).
mes_valor(8, 2).
mes_valor(9, 5).
mes_valor(10, 0).
mes_valor(11, 3).
mes_valor(12, 5).

% Año bisiesto: divisible por 400, o divisible por 4 pero no por 100
bisiesto(A) :- R is A mod 400, R = 0.
bisiesto(A) :- R4 is A mod 4, R4 = 0, R100 is A mod 100, R100 \= 0.

% Corrección: se resta 1 en enero o febrero de un año bisiesto
correccion(A, M, 1) :- M =< 2, bisiesto(A).
correccion(A, M, 0) :- M > 2.
correccion(A, M, 0) :- M =< 2, \+ bisiesto(A).

% Paso 1: los siglos
valor_siglos(S, V) :- R is S mod 4, V is ((3 - R) * 2) mod 7.

% Paso 2: los años (docenas + resto + cuatros del resto)
valor_anyo(A, V) :-
  Docenas is A // 12,
  Resto is A mod 12,
  Cuatros is Resto // 4,
  V is (Docenas + Resto + Cuatros) mod 7.

% 0 = domingo (equivale al 7 de Carroll), 1 = lunes, ... 6 = sabado
nombre_dia(0, domingo).
nombre_dia(1, lunes).
nombre_dia(2, martes).
nombre_dia(3, miercoles).
nombre_dia(4, jueves).
nombre_dia(5, viernes).
nombre_dia(6, sabado).

dia_semana(D, M, Anyo, Dia) :-
  S is Anyo // 100,
  A is Anyo mod 100,
  valor_siglos(S, VS),
  valor_anyo(A, VA),
  mes_valor(M, VM),
  correccion(Anyo, M, C),
  N is (VS + VA + VM + D - C) mod 7,
  nombre_dia(N, Dia).
```

---

## Ejercicios 3

---

### Pregunta 1

Definir la relación `'min_lista(+L, ?X)'` que se verifique si **X** es el mínimo de la lista de números **L**. Por ejemplo, `min_lista([9, 3, 1, 5] , X)` debe dar `X = 1`.

```prolog
% Caso base: el mínimo de una lista con un solo elemento es ese elemento.
min_lista([X], X).

% Caso recursivo: se calcula el mínimo de la cola y se compara con la cabeza.
min_lista([H|T], M) :-
  T \= [],
  min_lista(T, M1),
  M is min(H, M1).
```

---

### Pregunta 2

Definir la relación `'cuadrado(+L1, ?L2)'` donde **L1** es una lista constituida por números y que se verifique si **L2** está constituida por los números equivalentes de la lista **L1** elevados al cuadrado. Por ejemplo, `cuadrado([1, 2, 3], L2)` debe dar `L2 = [1, 4, 9]`.

```prolog
% Caso base: el cuadrado de los elementos de la lista vacía es la lista vacía.
cuadrado([], []).

% Caso recursivo: se eleva al cuadrado la cabeza y se procesa la cola.
cuadrado([H|T], [H2|T2]) :- H2 is H * H, cuadrado(T, T2).
```

---

### Pregunta 3

Definir la relación `'entrelaza(?L1, ?L2, ?L)'` que se verifique si la lista **L** está constituida por los elementos entrelazados de las listas **L1** y **L2**, que han de tener igual longitud. Por ejemplo, `entrelaza([1, 3, 5], [2, 4, 6], L)` debe dar `L = [1, 2, 3, 4, 5, 6]`.

```prolog
% Caso base: dos listas vacías dan la lista vacía.
entrelaza([], [], []).

% Caso recursivo: las cabezas H1 y H2 van juntas al inicio de L y el resto se
% obtiene entrelazando las colas.
entrelaza([H1|T1], [H2|T2], [H1,H2|T]) :- entrelaza(T1, T2, T).
```

---

### Pregunta 4

Definir la relación `'subtramo(?L1, ?L2)'` que se verifique si la lista **L1** es un subtramo ordenado listas **L2**. Por ejemplo, `subtramo([3, 4, 5], [1, 2, 3, 4, 5, 6])` debe dar `true`.

```prolog
% Un subtramo es un prefijo de algún sufijo de L2.
subtramo(L1, L2) :- prefijo(L1, S), sufijo(S, L2).

% Prefijo: la lista vacía es prefijo de cualquiera; si las cabezas coinciden, se
% sigue con las colas.
prefijo([], _).
prefijo([H|T1], [H|T2]) :- prefijo(T1, T2).

% Sufijo: L2 mismo, o L2 sin su cabeza.
sufijo(L, L).
sufijo(S, [_|T]) :- sufijo(S, T).
```

---

### Pregunta 5

Definir la relación `'previo(?X, ?Y, +L)'` que se verifique si **X** está antes que **Y** en la lista **L**. Por ejemplo, `previo(b, e, [a, b, c, d, e, f])` debe dar `true`.

```prolog
% X está antes que Y si, al encontrar X, Y aparece en el resto de la lista.
previo(X, Y, [X|T]) :- pertenece(Y, T).
previo(X, Y, [_|T]) :- previo(X, Y, T).

% Y pertenece a la lista si es su cabeza o está en la cola.
pertenece(X, [X|_]).
pertenece(X, [_|T]) :- pertenece(X, T).
```

---

### Pregunta 6

Definir la relación `'aplana(+L1, ?L2)'` que se verifique si **L2** es la lista equivalente a **L1** donde se han eliminado todos su anidamientos. Por ejemplo, `aplana([a, b, [c, [d], [], e], f], L)` debe dar `L = [a, b, c, d, e, f]`.

```prolog
% Caso base: la lista vacía se aplana a la lista vacía.
aplana([], []).

% Si la cabeza no es una lista, se conserva.
aplana([H|T], [H|LT]) :-
  \+ is_list(H),
  aplana(T, LT).

% Si la cabeza es una lista, se aplana y se concatena con el aplanado de la cola.
aplana([H|T], L) :-
  is_list(H),
  aplana(H, LH),
  aplana(T, LT),
  append(LH, LT, L).
```

---

## Ejercicios 4

---

### Pregunta 1

Definir la relación `'invertir(?L1,?L2)'` que se verifique si la lista **L2** es equivalente a la **L1** pero con los elementos en orden inverso. Utilizar la técnica del acumulador para ello. Por ejemplo, `invertir([9, 3, 1, 5] , L)` debe dar `L = [5, 1, 3, 9]`.

```prolog
% invertir(?L1, ?L2): L2 es L1 en orden inverso.
invertir(L1, L2) :- invertir_acc(L1, [], L2).

% Caso base: al agotar la lista, el acumulador es el resultado.
invertir_acc([], A, A).

% Caso recursivo: la cabeza pasa al inicio del acumulador.
invertir_acc([H|T], A, L) :- invertir_acc(T, [H|A], L).
```

---

### Pregunta 2

Definir la relación `'suma(+L,?X)'` y `'suma_acc(+L,?X)'` que se verifiquen si **X** es la suma de los elementos de la lista de números **L**. Ha de implementarse el primero sin acumulador y el segundo con. Por ejemplo, `suma([9, 3, 1, 5], X)` debe dar `X = 18`.

```prolog
% ---------- Sin acumulador ---------- %
% Caso base: la suma de una lista de un elemento es dicho elemento.
suma([X], X).

% Caso recursivo: se suma la cabeza a la suma de la cola.
suma([H|T], X) :- suma(T, XT), X is H + XT.

% ---------- Con acumulador ---------- %
suma_acc(L, X) :- suma_acc_aux(L, 0, X).

% Caso base: al agotar la lista, el acumulador es el resultado.
suma_acc_aux([], A, A).

% Caso recursivo: se suma la cabeza al acumulador.
suma_acc_aux([H|T], A, X) :- A1 is A + H, suma_acc(T, A1, X).
```

---

### Pregunta 3

Implementar la búsqueda de solución para el juego [Torres de Hanói](https://es.wikipedia.org/wiki/Torres_de_Han%C3%B3i) con tres postes y un número indefinido **N** de discos, de forma que `'hanoi(+N,?L)'` devuelva en **L** una lista de movimientos para resolver el problema. Ha de implementarse tres variantes: sin acumulador `'hanoi/2'`, con acumulador `'hanoi_acc/2'`y con estructura en diferencia `'hanoi_dif/2'`. Por ejemplo, `hanoi(3,L)` debe dar `L = [mover(1, 3), mover(1, 2), mover(3, 2), mover(1, 3), mover(2, 1), mover(2, 3), mover(1, 3)]`.

```prolog
% ---------- Sin acumulador ---------- %
hanoi(N, L) :- mueve(N, 1, 3, 2, L).

% mueve(N, Origen, Destino, Auxiliar, Movimientos)
mueve(0, _, _, _, []).
mueve(N, O, D, A, L) :-
  N > 0,
  N1 is N - 1,
  mueve(N1, O, A, D, L1),     % N-1 discos de O a A
  mueve(N1, A, D, O, L2),     % N-1 discos de A a D
  append(L1, [mover(O, D)|L2], L).


% ---------- Con acumulador ---------- %
hanoi_acc(N, L) :- hanoi_acc(N, 1, 3, 2, [], L).

% Acc contiene los movimientos que vienen después.
hanoi_acc(0, _, _, _, Acc, Acc).
hanoi_acc(N, O, D, A, Acc, L) :-
  N > 0,
  N1 is N - 1,
  hanoi_acc(N1, A, D, O, Acc, L2),     % se construye primero el final
  hanoi_acc(N1, O, A, D, [mover(O, D)|L2], L).


% ---------- Con estructura en diferencia ---------- %
hanoi_dif(N, L) :- hanoi_dif(N, 1, 3, 2, L-[]).

hanoi_dif(0, _, _, _, L-L).
hanoi_dif(N, O, D, A, L-F) :-
  N > 0,
  N1 is N - 1,
  hanoi_dif(N1, O, A, D, L-[mover(O, D)|M]),
  hanoi_dif(N1, A, D, O, M-F).
```

---

## Ejercicios 5

---

### Pregunta 1

Modificar el predicado `alterar/2` estudiado en el tema "Estructuras de datos y listas" para que solo genere una única solución. Por ejemplo, `alterar([tu, eres, un, ordenador], X)` debe dar unicamente `X = [yo, [no, soy], un, humano]` sin posibilidad de resatisfacción.

```prolog
% alterar.prolog
cambiar(hago, [no, hago]).
cambiar(tu, yo).
cambiar(eres, [no, soy]).
cambiar(tonto, listo).
cambiar(c, prolog).
cambiar(ordenador, humano).
cambiar(humano, ordenador).
cambiar(X,X).
alterar([],[]).
alterar([H|T],[X|Y]) :- cambiar(H,X), alterar(T,Y).
```

El problema está en cambiar/2: la última cláusula cambiar(X, X) también se aplica a palabras 
que ya tienen cambio específico. Por eso alterar(\[tu, ...], X) da primero yo y, al pedir más 
soluciones, también tu.

```prolog
% Solución: corte en las cláusulas específicas
cambiar(hago, [no, hago]) :- !.
cambiar(tu, yo) :- !.
cambiar(eres, [no, soy]) :- !.
cambiar(tonto, listo) :- !.
cambiar(c, prolog) :- !.
cambiar(ordenador, humano) :- !.
cambiar(humano, ordenador) :- !.
cambiar(X, X).

alterar([], []).
alterar([H|T], [X|Y]) :- cambiar(H, X), alterar(T, Y).
```

---

### Pregunta 2

Modificar el predicado `prince/2` estudiado en el tema "Aritmética" para que, usando el generador de enteros de la pregunta 2 de Ejercicios 2, cuando sea llamando con el primer argumento instanciado y su segundo argumento sin instanciar, devuelva en las sucesivas resatisfacciones los diferentes años de reinado. En los restantes casos el predicado debe seguir funcionando como lo hacía, sin necesidad de realizar mas iteraciones. Por ejemplo, `prince(cadwallon, X)` debe retornar `X = 985` y `X = 986`, y para `prince(X, 845)` debe seguir dando `X = rhodri`.

```prolog
% reigns
reigns(rhodri,844,878).
reigns(anarawd,878,916).
reigns(hywel_dda,916,950).
reigns(lago_ap_idwal,950,979).
reigns(hywel_ap_ieuaf,979,985).
reigns(cadwallon,985,986).
reigns(maredudd,986,999).

% generado_int
generado_int(X, Inf, Sup) :- Inf =< Sup, X = Inf.
generado_int(X, Inf, Sup) :-
  Inf < Sup,
  Inf1 is Inf + 1,
  generado_int(X, Inf1, Sup).

% Primer argumento instanciado y segundo libre: se generan los años.
prince(X, Y) :-
  nonvar(X), var(Y), !,
  reigns(X, Inf, Sup),
  generado_int(Y, Inf, Sup).

% Resto de casos: comportamiento original.
prince(X, Y) :-
  reigns(X, A, B),
  Y >= A,
  Y =< B.
```

---

### Pregunta 3

Definir la relación `'suma_acc10(+L, ?N)'` que se verifique si **N** es la suma de la lista de números **L** que no supere 10. Si la suma supera 10 el predicado debe fallar. Ha de implementarse utilizando la técnica del acumulador, siendo una variante de la pregunta 2 de Ejercicios 4. Por ejemplo, `suma_acc10([5, 3, 2, 1, 4, 1], N)` debe fallar y `suma_acc10([5, 3, 2], N)` debe dar `N = 10`.

```prolog
suma_acc10(L, N) :- suma_acc10(L, 0, N).

% Caso base: al agotar la lista, el acumulador es el resultado si no supera 10.
suma_acc10([], Acc, Acc) :- Acc =< 10.

% Caso recursivo: se suma la cabeza al acumulador.
suma_acc10([H|T], Acc, N) :- Acc1 is Acc + H, suma_acc10(T, Acc1, N).
```

---

### Pregunta 4

Definir la relación `'primo(+N)'` que se verifique si **N** es un número primo. Para ello siga el [algoritmo para determinar si un número es primo](https://patriciaemiguel.com/pseudocodigo/2019/09/06/algoritmo-nros-primos.html), deteniendo las interaciones en cuanto se pueda. Por ejemplo, `primo(769)` debe dar éxito.

```
# Algoritmo para definir si un número natural es primo o no

primo ← true
repetir para i desde 2 hasta número - 1:
  si número % i == 0:
	  primo ← false
    interrumpir repetición
```

```prolog
primo(N) :- N > 1, primo(N, 2).

% Caso base: se han probado todos los divisores de 2 a N-1.
primo(N, N).

% Caso recursivo: si Y no divide a N, se prueba el siguiente.
% Si Y divide a N, la cláusula falla y se detiene la búsqueda.
primo(N, Y) :-
  Y < N,
  N mod Y =\= 0,
  Y1 is Y + 1,
  primo(N, Y1).
```

---

### Pregunta 5

Definir la relación `'primer_primo(+L, ?N)'` que se verifique si **N** es el primer número primo de entre los números generados con las distintas permutaciones de cifras contenidas en la lista **L**. Para ello utilice el predicado `primo/1` del ejercicio anterior y el predicado interno [`permutation/2`](https://swish.swi-prolog.org/pldoc/doc_for?object=permutation/2). Por ejemplo, `primer_primo([1,5,6,8], N)` debe dar `N = 5861`.

```prolog
% primo(+N)
primo(N) :- N > 1, primo(N, 2).
primo(N, N).
primo(N, Y) :- Y < N, N mod Y =\= 0, Y1 is Y + 1, primo(N, Y1).

% aplana_cifras(+Cifras, -Numero): [5,8,6,1] -> 5861
aplana_cifras(L, N) :- aplana_cifras(L, 0, N).
aplana_cifras([], Acc, Acc).
aplana_cifras([H|T], Acc, N) :- Acc1 is Acc * 10 + H, aplana_cifras(T, Acc1, N).

% primer_primo(+L, ?N): primer primo entre las permutaciones de L.
primer_primo(L, N) :- permutation(L, P), aplana_cifras(P, N), primo(N), !.
```