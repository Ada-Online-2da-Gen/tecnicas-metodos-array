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

Cuando tenemos un array, y queremos realizar un conjunto de operaciones sobre cada uno de los ítems del array, y obtener el resultado de cada una de esas operaciones en el array.

```js
// datos iniciales
const numeros = [5, 8, 19, 22]

// operación a realizar por cada ítem
// es el callback de map que devuelve el resultado de tomar el ítem y realizarle una operación
const duplicar = x => x * 2

// mapeamos el array original a un nuevo array con los resultados de cada una de las operaciones
const resultado = numeros.map(duplicar)

//resultado
[10, 16, 38, 44]
```
<br>

### Extraer los valores de estructura de datos complejas

Cuando tenemos estructuras de datos complejas (array de objetos, a veces donde cada objeto tiene anidados otros objetos o arrays) a veces nos conviene primero "limpiar" los datos que no nos interesan y quedarnos sólo con aquellos que necesitemos manipular, de esta forma es más fácil trabajar con ellos.

Para eso mapeamos de los objetos del array original a una de sus propiedades

```js
// datos iniciales
const personas = [
  {nombre: "Juan", dinero: 550},
  {nombre: "María", dinero: 1200},
  {nombre: "Carlos", dinero: 889}
]

// mapeamos cada objeto a una de sus propiedades
// para eso el callback del map toma un ítem y devuelve esa propiedad
const aDinero = persona => persona.dinero

const resultado = personas.map(aDinero)

// resultado
[550, 1200, 889]
```
<br>

### Formatear objetos

Podemos cambiar el formato o la estructura de cada objeto, por una que nos resulte más prática para trabajar. Por ejemplo, en el caso siguiente, si de `animales` quisiéramos encontrar "conejo", tendríamos que usar `find` y pasarle un callback, en cambio si "conejo" fuese una propiedad, solo tendríamos que hacer `animales["conejo"]`

Para eso, mapeamos el array original, devolviendo un nuevo objeto con la estructura que necesitemos. Podemos usar spread o destructuring si lo requiere.

```js
// datos originales
const animales = [
  { nombre: "conejo", comida: "zanahoria" },
  { nombre: "tortuga", comida: "lechuga" },
  { nombre: "canario", comida: "semillas" }
]

// mapeamos cada ítem a un nuevo objeto
// en este caso, el objeto tiene como propiedad el valor de una de sus propiedades, que se asigna dinámicamente
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

Podemos agregar propiedades, borrar propiedades y modificar propiedades de un objeto, usando spread y destructuring si hace falta.

```js
// datos originales
const personas = [
  { nombre: "Juan", dinero: 550 },
  { nombre: "María", dinero: 1200 },
  { nombre: "Carlos", dinero: 889 }
]

// callback del map que se llama por cada objeto
// devolvemos un nuevo objeto con las propiedades iniciales, cambiándole el valor de una de ellas
const aDineroAumentado = persona => ({...persona, dinero: persona.dinero + 1000})

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

Podemos tomar un array de un tipo de datos, y mapearlo a un array de otro tipo de datos. Por ejemplo, en este caso, de un array de strigs pasamos a un array de objetos que contienen el dato original e información extra.

```js
// datos originales
const palabras = ["javascript", "map", "filter", "array"]

// callback del map
// cada string es mapeado a un objeto
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

### Realizar una operación sobre un conjunto de valores

Cuando tenemos varios datos del mismo tipo, y queremos hacer un operación con todos ellos (sumarlos, multiplicarlos) y obtener el resultado total

```js
// datos originales
const numeros = [1, 3, 4, 10]

// callback del reduce
// sumaParcial es el acumulador, que comienza con el primer ítem del array, y numero comienza con el segundo
// en cada vuelta, devolvemos la suma del numero actual con la sumaParcial (es decir, con la suma de todos los números anteriores al actual)
const aSumaTotal = (sumaParcial, numero) => sumaParcial + numero

const resultado = numeros.reduce(aSumaTotal)

// resultado
18
```
<br>

### Obtener el promedio

Para obtener el promedio con un único `reduce`, necesitamos sus parámetros extras, que son el índice y el array. Lo que hacemos es sumar todos los números, y chequear si el índice es igual al último del array (`array.length - 1`), si lo es devolvemos la suma dividido la cantidad de ítems del array, sino devolvemos la suma hasta el momento

```js
const aPromedio = (total, numero, index, array) => {
  total += numero
  return index !== array.length - 1 ? total : total / array.length 
}

[1, 2, 3].reduce(aPromedio) // 2 
```
<br>

### Reducir un array 2D a uno 1D

Para poder hacerlo, necesitamos devolver siempre un array (que va siendo el valor de `arrayParcial`). Este array contiene todos los ítems de `arrayParcial` hasta el momento y todos los ítems del array que estamos recorriendo (que los insertamos mediante spread). Como siempre estamos devolviendo un array, al finalizar la iteración nos va a quedar un único array.

```js
const numeros = [
  [2, 4, 3],
  [7, 1, 9],
  [0, 5, 6]
]

const aArray1D = (arrayParcial, array) => [...arrayParcial, ...array]

const resultado = numeros.reduce(aArray1D)

// resultado
[2, 4, 3, 7, 1, 9, 0, 5, 6]
```

En la primera iteración, `arrayParcial` es `[2, 4, 3]`, `array` es `[7, 1, 9]` (ítems del array que estamos recorriendo). Como devolvermos un nuevo array con los ítems de ambos, en la siguiente vuelta `arrayParcial` es `[2, 4, 3, 7, 1, 9]`, `array` es `[0, 5, 6]`. Al devolver de nuevo un nuevo array con la concatenación de ambos, el resultado final nos queda en un array único con todos los ítems.

Esto es exactamante lo mismo que usar el método `flat()` sobre números, pero es interesante cómo podemos lograrlo con `reduce`, ya que a veces los arrays se encuentran dentro de propiedades de objetos, y en ese caso no nos sirve `flat`.

Cuando no podemos usar `flat`, por ejemplo porque es un array de objetos, podemos usar la misma técnica:

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

### Contar la cantidad de repeticiones

Podemos obtener un objeto donde obtengamos la cantidad de ítems en un array (o la cuenta de algo), donde cada cosa que queremos contar o medir es la propiedad, y el dato su valor. No solo podemos obtener la cuenta de algo, sino el resultado de ciertas operaciones, como el promedio, la suma, etc.

Para eso tenemos que: 
 1. pasarle a `reduce` un segundo parámetro, ya que lo que queremos devolver es distinto a lo que estamos iterando.
 2. `cuentaParcial` comienza teniendo este objeto vacío, y como vamos devolver en cada vuelta `cuentaParcial`, siempre va a ser un objeto
 3. `pais` es cada uno de los strings/ítems que estamos recorriendo
 4. en cada iteración, a la propiedad correspondiente al item en el objeto (que lo accedemos de forma dinámica `cuentaParcial[pais]`), le sumamos 1. 
 5. en caso de que esa propiedad no exista, `cuentaParcial[pais]` va a devolver `undefined`, por lo tanto si sumamos 1 nos va a dar `NaN`, que se va a asignar a dicha propiedad, y toda suma siguiente va a dar `NaN`
 6. por lo tanto, si obtenemos `NaN`, como es un valor falso, podemos usar el circuito de evaluación corto para devolver un valor es caso de que `NaN` ocurra, en este caso 1, porque sería la primera que nos encontramos con dicho valor que queremos medir
 7. otra forma de escribir `cuentaParcial[pais] + 1 || 1` es `(cuentaParcial[pais] || 0) + 1`

```js
const paises = ["Argentina", "Uruguay", "Bolivia", "Argentina", "Argentina", "Uruguay", "Chile"]

const aCantidad = (cuentaParcial, pais) => {
  cuentaParcial[pais] = cuentaParcial[pais] + 1 || 1
  return cuentaParcial
}

const resultado = paises.reduce(aCantidad, {})

// resultado
{
  Argentina: 3,
  Uruguay: 2,
  Bolivia: 1,
  Chile: 1
}
```
<br>

## Encontrar el elemento con mayor o menor valor

La estrategia consiste en que el callback devuelva el elemento actual o el mayor hasta el momento, dependiendo de cúal sea el más grande (o chico, si medimos eso). Como siempre devolvemos un elemento (el que mejor cumpla la condición), en la próxima vuelta vamos a tener ese elemento para compararlo con el próximo.

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

Suponiendo el primer ejemplo:
- Iteración 1: `mayor` es 32, `numero` es 2. Devolvemos 32.
- Iteración 2: `mayor` es 32, `numero` es 5. Devolvemos 32.
- Iteración 3: `mayor` es 32, `numero` es 17. Devolvemos 32.
- Iteración 4: `mayor` es 32, `numero` es 88. Devolvemos 88.
- Iteración 5: `mayor` es 88, `numero` es 29. Devolvemos 88.

<br>

### Array de objetos a objeto con keys

Muchas veces tenemos una cierta estructura de datos (un array de objetos), y necesitamos hacer operaciones constantemente sobre ciertos elementos, buscándolos por alguna propiedad. Para eso podemos usar `find`, pero también podémos reducir el array a un objeto, donde cada valor de la propiedad que nos interesa sea una propiedad del objeto, de modo que podamos recorrerlas y accederlas dinámicante de forma más sencillamente.

Para eso necesitamos un nuevo objeto como segundo parámetro del `reduce` (ya que la estructura del resultado que queremos obtener es distinta del ítem que recorremos), y por cada ítem, devolvemos un nuevo objeto con todo lo que contenía el objeto parcial hasta el momento, usando spread, y una propiedad nueva con su valor. Dicha propiedad, como es dinámica, necesitamos definirla con la notación de corchetes.

```js
const animales = [
  { nombre: "conejo", comida: "zanahoria" },
  { nombre: "tortuga", comida: "lechuga" },
  { nombre: "canario", comida: "semillas" }
]

const aAnimalConComida = (animales, animal) => ({...animales, [animal.nombre]: animal.comida})

const resultado = animales.map(aAnimalConComida, {})

// resultado
{ 
  conejo: "zanahoria",
  tortuga: "lechuga",
  canario: "semillas"
]
```
<br>

### Eliminar valores repetidos

Para eliminar valores repetidos en un array, la estrategia es comenzar con un array vacío, ya que queremos obtener un array y estamos recorriendo otros ítems. En cada vuelta, chequeamos si el item se encuentra en el array, si lo está, devolvemos el array tal cual, sino devolvemos un nuevo array con todos los ítems que teníamos hasta el momento (usando spread) y el item que estamos recorriendo

```js
const colores = ["azul", "verde", "verde", "negro", "naranja", "azul", "verde", "rojo"]

const aValoresUnicos = (lista, item) => lista.includes(item) ? lista : [...lista, item]

const resultado = colores.reduce(aValoresUnicos, [])

// resultado
["azul", "verde", "negro", "naranja", "rojo"]
```

En el ejemplo:
- Iteración 1: `lista` es `[]`, item es `"azul"`, devolvemos `["azul"]`
- Iteración 2: `lista` es `["azul"]`, item es `"verde"`, devolvemos `["azul", "verde"]`
- Iteración 3: `lista` es `["azul", "verde"]`, item es `"verde"`, devolvemos `["azul", "verde"]`
- Iteración 4: `lista` es `["azul", "verde"]`, item es `"negro"`, devolvemos `["azul", "verde", "negro"]`
- Iteración 5: `lista` es `["azul", "verde", "negro"]`, item es `"naranja"`, devolvemos `["azul", "verde", "negro", "naranja"]`
- Iteración 6: `lista` es `["azul", "verde", "negro", "naranja"]`, item es `"azul"`, devolvemos `["azul", "verde", "negro", "naranja"]`
- Iteración 7: `lista` es `["azul", "verde", "negro", "naranja"]`, item es `"azul"`, devolvemos `["azul", "verde", "negro", "naranja"]`
- Iteración 8: `lista` es `["azul", "verde", "negro", "naranja"]`, item es `"rojo"`, devolvemos `["azul", "verde", "negro", "naranja", "rojo"]`
<br>

### Obtener mayor cantidad de un dato en una estructura compleja

- Reducimos el array original a un objeto con los nombres de los animales como propiedades y la cantidad de repeticiones como valores, con la técnica de *Contar la cantidad de repeticiones*, para lo cual usamos el callback `aCantidadDeAnimales` que recorre el array de objetos original, y devuelve un objeto con cada nombre de animal como propiedad y la cantidad como valor. Ese objeto se guarda en `cantidades`, y queda así:
```js
{
 Sapo: 2,
 Leon: 1,
 Foca: 1
}
```
- Obtenemos las keys de dicho objetos, es decir, los nombres de los animales, con `Object.keys(obj)`. Eso nos devuelve el array `["Sapo", "Leon", "Foca"]`, que lo guardamos en `nombreAnimales`
- Con esas keys, podemos acceder a la cantidad de cada animal de forma dinámica, p. ej.: `animales[animal]`
- Tenemos que usar la técnica de *Encontrar el elemento con mayor o menor valor*, pero tenemos un problema: el valor que queremos devolver es un string (la propiedad), y el queremos contrastar es el valor de dicha propiedad
- Para eso nuestro callback necesita un dato extra, el objeto con las cantidades. Lo que vamos a hacer es recorrer los nombres de los animales (las keys), e ir obteniendo el valor de cada animal. 
- Como nos interesa solo el nombre, lo que vamos a hacer es ir recorriendo cada nombre. Por cada nombre, accedemos de forma dinámica al valor que contiene el objeto en dicha propiedad, y lo comparamos con el valor del nombre guardado hasta el momento (en el acumulador). Dependiendo de qué valor es más alto, devolvemos uno u otro nombre. Eso lo hacemos en la función `conMayorCantidad`
- Como el tercer parámetro de `conMayorCantidad` es algo que no se encuentra en el array que vamos a recorrer (`nombresAnimales`), no podemos llamar a la función directamente con `.reduce(conMayorCantidad)`

```js
const animales = [
  { nombre: 'Sapo' }, 
  { nombre: 'Leon' },
  { nombre: 'Foca' },
  { nombre: 'Sapo' }
]

const animaConMasCantidad = (animales) => {
  const aCantidadDeAnimales = (cuenta, animales) => {
    cuenta[animales.nombre] = cuenta[animales.nombre] + 1 || 1
    return cuenta
  }
  
  const conMayorCantidad = (masCantidad, animal, animales) => 
    animales[animal] > animales[masCantidad] ? animal : masCantidad

  const cantidades = animales.reduce(aCantidadDeAnimales, {})
  
  const nombresAnimales = Object.keys(cantidades)
    
  return nombresAnimales.
    reduce((masCantidad, animal) => conMayorCantidad(masCantidad, animal, cantidades))  
}
```
