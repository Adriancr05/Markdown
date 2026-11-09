# Ejercicios de lógica proposicional

1. La proposición α ⇔ β implica que α y β han de tener los mismos valores de verdad para todos los modelos: o ambas son ciertas o ambas son falsas. Demuestre entonces mediante reglas de inferencia que α ⇔ β ≡ (α ∧ β) ∨ (¬α ∧ ¬β)
2. La regla general de resolución en lógica proposicional afirma que (α ∨ β ∨ γ) ∧ (δ ∨ ¬β ∨ θ) ≡ α ∨ γ ∨ δ ∨ θ. Demuestre esta regla mediante reglas de inferencia.

3. Ash, Misty y Brock han organizado una batalla entre sus pokémon. Se conocen los siguientes datos al respecto:
   - **a.** Uno y sólo uno de los siguientes pokémon fue el vencedor: Pikachu, Bulbasaur, Togepi, Starmie, Vulpix y Onix.
   - **b.** Ash ganó la batalla si el pokémon vencedor fue Pikachu o Bulbasaur.
   - **c.** Si o bien Togepi o bien Starmie fue el vencedor, Misty ganó la batalla.
   - **d.** Brock ganó la batalla si el vencedor fue Onix o Vulpix.
   - **e.** Si Onix fue derrotado, Starmie también.
   - **f.** Bulbasaur fue derrotado.
   - **g.** Si Pikachu fue derrotado, entonces Ash no ganó la batalla.
   - **h.** Brock no ganó la batalla si Bulbasaur fue derrotado.
   - **i.** Si Vulpix fue derrotado, Togepi y Onix también corrieron la misma suerte.

Se pide:

  - 1. Formalizar los datos anteriores en el lenguaje de la lógica proposicional.
  - 2. Para cada fórmula obtenida, escribir un conjunto de cláusulas equivalente.
  - 3. Usando resolución proposicional, demostrar que Ash fue el ganador.

4. Se dispone de la siguiente base de conocimiento:
    1. Los animales con pelo que dan leche son mamíferos.
    2. Los mamíferos que tienen pezuñas son ungulados.
    3. Los ungulados con rayas negras son cebras.

Usando resolución proposicional, averigüe si un animal que tiene pelos, pezuñas y rayas negras es una cebra.

5. Un rey somete a un prisionero a la siguiente prueba: lo enfrenta a dos puertas, de las que el prisionero debe elegir una, y entrar en la habitación correspondiente. Se informa al prisionero que en una de las habitaciones hay un león y en la otra hay un tesoro. Como es natural, el prisionero debe elegir la puerta que le lleva al tesoro (entre otras cosas, para no ser devorado por el león). Para ayudarle, en cada puerta hay un letrero:
  - Puerta 1: "En esta habitación hay un tesoro y en la otra un león."
  - Puerta 2: "En una de estas habitaciones hay un tesoro y en una de estas habitaciones hay un león."

Sabiendo que uno de los carteles dice la verdad y el otro no, demostrar mediante resolución que el tesoro está en la segunda puerta.
