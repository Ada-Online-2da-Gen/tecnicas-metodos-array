# Trabajando con métodos de array

Un primer criterio a chequear para decidir que método utilizar es, dado el array sobre el que estamos operando,
  * ¿tenemos que mantener la cantidad de items?
    - SI -> MAP
    - NO -> FILTER / REDUCE
      - ¿el resultado tiene que ser un array? -> FILTER / REDUCE
          - ¿tenemos que chequear una condición para ver si un elemento queda en el array resultante? -> FILTER
          - ¿el array resultante puede contener un elemento, ninguno o varios? -> FILTER (si hay condición)
          - ¿el array resultante puede contener varios elementos? -> REDUCE
      - ¿el resultado tiene que ser algo distinto a un array (objeto, string, número, booleano)? -> REDUCE
      - ¿el resultado tiene que ser un elemento? -> REDUCE

## `map`

### Llamar a una función por cada ítem

```js
const numeros = [5, 8, 19, 22]

const duplicar = x => x * 2

const resultado = numeros.map(duplicar)

//resultado
[10, 16, 38, 44]
```
<br>

### Extraer los valores de la propiedad que nos interesa

```js
const personas = [
  {nombre: "Juan", dinero: 550},
  {nombre: "María", dinero: 1200},
  {nombre: "Carlos", dinero: 889}
]

const aDinero = persona => persona.dinero

const resultado = personas.map(aDinero)

// resultado
[550, 1200, 889]
```
<br>

### Formatear objetos

```js
const animales = [
  { nombre: "conejo", comida: "zanahoria" },
  { nombre: "tortuga", comida: "lechuga" },
  { nombre: "canario", comida: "semillas" }
]

const aAnimalConComida = animal => ({[animal.nombre]: animal.comida})

const resultado = animales.map(aAnimalConComida)

// resultado
[
  { conejo: "zanahoria" },
  { tortuga: "lechuga" },
  { canario: "semillas" }
]
```
<br>

### Modificar objetos

Podemos agregar propiedades, borrar propiedades, modificar propiedades. Combinar con uso del destructuring y el spread.

```js
const personas = [
  { nombre: "Juan", dinero: 550 },
  { nombre: "María", dinero: 1200 },
  { nombre: "Carlos", dinero: 889 }
]

const aDineroAumentado = persona => {...persona, dinero: dinero.persona + 1000}

const resultado = personas.map(aDineroAumentado)

// resultado
[
  { nombre: "Juan", dinero: 1550 },
  { nombre: "María", dinero: 2200 },
  { nombre: "Carlos", dinero: 1889 }
]
```
<br>

### Convertir un tipo de dato en otro

```js
const palabras = ["javascript", "map", "filter", "array"]

const aCantidadDeLetras = palabra => ({ palabra, letras: palabra.length })

const resultado = palabras.map(aCantidadDeLetras)

// resultado
[
  { palabra: "javascript", letras: 10 },
  { palabra: "map", letras: 3 },
  { palabra: "filter", letras: 6 },
  { palabra: "array", letras: 5 }
]
```
<br>
<br>

## `reduce`

### Reducir un conjunto de valores a un único valor del mismo tipo, realizando una operación sobre ellos

```js
const numeros = [1, 3, 4, 10]

const aSumaTotal = (sumaParcial, numero) => sumaParcial + numero

const resultado = numeros.reduce(aSumaTotal)

// resultado
18
```
<br>

### Obtener el promedio

```js
const aPromedio = (total, numero, index, array) => {
  total += numero
  return index !== array.length - 1 ? total : total / array.length 
}

[1, 2, 3].reduce(aPromedio) // 2 
```
<br>

### Reducir un array 2D a uno 1D

```js
const numeros = [
  [2, 4, 3],
  [7, 1, 9],
  [0, 5, 6]
]

const aArray1D = (arrayParcial, array) => [...arrayParcial, array]

const resultado = numeros.reduce(aArray1D)

// resultado
[2, 4, 3, 7, 1, 9, 0, 5, 6]
```
<br>

### Contar la cantidad de repeticiones de cada elemento

```js
const paises = ["Argentina", "Uruguay", "Bolivia", "Argentina", "Argentina", "Uruguay", "Chile"]

const aCantidad = (cuentaParcial, pais) => {
  cuentaParcial[pais] = cuentaParcial[pais] + 1 || 1
  return cuentaParcial
}

const resultado = paises.reduce(aCantidad)

// resultado
{
  Argentina: 3,
  Uruguay: 2,
  Bolivia: 1,
  Chile: 1
}
```
<br>

## Encontrar el elemento que tenga el mayor o menor valor

```js
const numeros = [33, 2, 5, 17, 88 ,29]

const aNumeroMayor = (mayor, numero) => numero > mayor ? numero : mayor

const numeroMayor = numeros.reduce(aNumeroMayor) // 88

// ------------------------------------------

// Con objetos

const personas = [
  { nombre: "Juan", dinero: 550 },
  { nombre: "María", dinero: 1200 },
  { nombre: "Carlos", dinero: 889 }
]

const aPersonaConMasDinero = (personaConMasDinero, persona) => 
  persona.dinero > personaConMasDinero.dinero ? persona : personaConMasDinero 

const resultado = personas.reduce(aPersonaConMasDinero)

// resultado
{
  nombre: "María",
  dinero: 1200
}
```
<br>

### Array de objetos a objeto con keys

```js
const animales = [
  { nombre: "conejo", comida: "zanahoria" },
  { nombre: "tortuga", comida: "lechuga" },
  { nombre: "canario", comida: "semillas" }
]

const aAnimalConComida = (animales, animal) => ({...animales, [animal.nombre]: animal.comida})

const resultado = animales.map(aAnimalConComida)

// resultado
{ 
  conejo: "zanahoria",
  tortuga: "lechuga",
  canario: "semillas"
]
```
<br>

### Reducir una estructura de datos compleja

```js
const lista = [
  { colores: ["azul", "verde"] }, 
  { colores: ["verde", "negro", "naranja", "azul"] }, 
  { colores: ["verde", "rojo"] }
]

const aColores = (listaParcial, item) => [...listaParcial, ...item.colores]

const resultado = lista.reduce(aColores, [])

// resultado
["azul", "verde", "verde", "negro", "naranja", "azul", "verde", "rojo"]
```
<br>

### Eliminar valores repetidos

```js
const colores = ["azul", "verde", "verde", "negro", "naranja", "azul", "verde", "rojo"]

const aValoresUnicos = (lista, item) => lista.includes(item) ? lista : [...lista, item]

const resultado = colores.reduce(aValoresUnicos, [])

// resultado
["azul", "verde", "negro", "naranja", "rojo"]
```
