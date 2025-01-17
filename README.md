# Lotería pero en su lengua

El juego de cartas de lotería mexicana.

## Implementacíon

Por ahora realizado con `React`, y custom `js` en `./src/Game`. Antes con `boardgame.io` pero no tuvimos éxito porque nuestra concepto del juego no tiene ni turnos ni fases (aparte del cantor que nomás aumenta la gama de elecciones válidas).

## Tareas

- [X] leer (join) / elegir (host) la baraja antes de iniciar

- [X] ganar
	- [X] la verificación parece tardar hasta el próximo `.cantar`
	- [X] ROTO ya que muchas veces no se llega a verificar hasta las cartas muy últimas
	- [X] uno puede declararse ganador después del empate

- [X] lobby
	- [X] mostrar los jugadores en el lobby
	- [X] solo host inicia el juego
	- [X] mostrar algo para cada jugador (imagen, cuadro)
	- [X] separar la configuración del inicio en `.iniciar`
	- [X] los joiners leen la barajaId cuando conectan
	- [X] crea el host y se juntan los joiners

- [X] DRY los objetos switch/case if/else en Juego, App

- [X] router
	- [X] app de una sola página con `react-router-dom`
	- [X] pasar referencia al documento del depósito
	- [X] repensar porque el enrutador no se relaciona bien con la registración
		- arreglar atribución retrasada de `g` (`g === undefined`)
		- `history.push` en `useEffect(f, [..., g])`
		- leer params de la ruta en `useEffect(f, [])`

- [ ] búsqueda
	- [X] revisar funciones db para leer colección entera (lista de docs)
	- [X] componente de búsqueda
	- [ ] buscar juegos actuales y unirse

- [ ] prohibir más conexiones si tiene cierto estatus (¿`jugar`/`ganar`/`empate`?)

- [ ] demo
	- [X] crear imágenes, audio para la baraja
	- [ ] haz que el setTimeout/.timer espere por el audio
	- [ ] elegir baraja, opciones en una página de arranque

- [ ] diseño
	- ...tareas CSS/HTML

- [ ] ¿límite de jugadores en `Juego`, db, buscar?

- [ ] menú
	- jugar solo: juego público (crear/unirse)
	- jugar con amigos: crear juego privado (unirse con enlace)

- [X] acceso público, privado en el depósito

- [ ] selección de baraja/idioma
	- [ ] datos del objeto: nombre de baraja, audio, corre/buena
	- [ ] modo de enfatizar los idiomas
	- [X] usar `localStorage`
	- [ ] ¿en opciones o al iniciar?
	- [ ] solo buscar juego que tenga la misma baraja

- [ ] ARREGLAR: navegador se desconecta y `g === null` al cliquear en otro app
	- ¿usar `localStorage` una vez más para grabar la `g` entera?

- [X] ARREGLAR: usuario agrega nuevos jugadorIds cuando refresque la página
	- esto pasa si la ruta ya tiene param `:juegoId`
	- leer `jugadorId` en `localStorage`

- [ ] ARREGLAR: si el host refresca la página ya no hay host

## Aprendizaje improvisado

- [X] pero ¿por qué una vez funcionó el reductor con las entradas al revés?
	- ver la función `Juego.verificar` (_un aparte_)
```
// Ejemplo del error semántico
const c = [[0, 1, 2], [1, 2, 3], [2, 3, 4]];
const a = [true, false, true, true, true];
const listOfWins = c.reduce((nextC, accA) => ( 	// en vez de pensar en (acc, next)
	[
		...accA,
		!([...nextC.map(i => a[i])].includes(false))
	]
), []);
// [2, 3, 4, false] en vez de [false, false, true]
console.log(listOfWins);
const didWin = listOfWins.includes(true);
// false en vez de true
console.log(didWin);

// Hipótesis: la penúltima condición determina el valor del booleano final
// las otras nomás almacenan o agregan números
//
// Demostración:
//
// entradas:
//  c = [[0, 1, 2], [1, 2, 3], [2, 3, 4]];
// 	a = [false, false, false, false, false];
// salidas:
// ([], [0, 1, 2]) => [0, 1, 2, false !in []]
// ([0, 1, 2, true], [1, 2, 3])  => [1, 2, 3, false !in [a[0], a[1], a[2], a[true]]]
// ([1, 2, 3, false], [2, 3, 4]) => [2, 3, 4, false !in [a[1], a[2], a[3], a[false]]]
// => [2, 3, 4, false] => false
//
// entradas:
//  c = [[0, 1, 2], [1, 2, 3], [2, 3, 4]];
// 	a = [true, true, true, true, true];
// salidas:
// ([], [0, 1, 2]) => [0, 1, 2, false !in []]
// ([0, 1, 2, true], [1, 2, 3])  => [1, 2, 3, false !in [a[0], a[1], a[2], a[true]]]
// ([1, 2, 3, true], [2, 3, 4]) => [2, 3, 4, false !in [a[1], a[2], a[3], a[false]]]
// => [2, 3, 4, true] => true
//
// entradas:
//  c = [[0, 1, 2], [1, 2, 3], [2, 3, 4]];
// 	a = [false, false, true, true, true];
// salidas:
// ([], [0, 1, 2]) => [0, 1, 2, false !in []]
// ([0, 1, 2, true], [1, 2, 3])  => [1, 2, 3, false !in [a[0], a[1], a[2], a[true]]]
// ([1, 2, 3, false], [2, 3, 4]) => [2, 3, 4, false !in [a[1], a[2], a[3], a[false]]]
// => [2, 3, 4, false] => false
//
// entradas:
//  c = [[0, 1, 2], [1, 2, 3], [2, 3, 4]];
// 	a = [false, true, true, true, false];
// salidas:
// ([], [0, 1, 2]) => [0, 1, 2, false !in []]
// ([0, 1, 2, true], [1, 2, 3])  => [1, 2, 3, false !in [a[0], a[1], a[2], a[true]]]
// ([1, 2, 3, false], [2, 3, 4]) => [2, 3, 4, false !in [a[1], a[2], a[3], a[false]]]
// => [2, 3, 4, true] => true

// probar - seleccionar el botón GANAR después de intercambiar los (acc, next) de nuevo
// - no marcar nada => false
// - marcar cada condición menos Juego.condiciones[penúltima] => false
// - marcar la condición penúltima => true
```

- [ ] y ¿cómo hacerlo para que el depósito firestore no se quede en modo de prueba?
	- lectura: https://firebase.google.com/docs/firestore/security/get-started
```
// v2 para usar consultas de base
rules_version = '2';

// abrir un bloque de reglas
service cloud.firestore { }

// `match` identifica documento, `allow` autoriza acceso
match /databases/{database}/documents {
	match /<path>/ {
	  allow <action>: if <condition>;
	}
}

// ruta progresiva o sea son equivalentes:
// `match /ciudades/{ciudad} { match /hitos/{hito} {`
// `match /cities/{ciudad}/hitos/{hito} {`

// {nombre=**} para comodín con recursión
// esta ** también busca correspondencias hondas

// acciones permitidas
allow <action>:
// valores posibles
allow read, write, create, get, update, list, delete:

// condiciones
allow <action>: if <condition>
// ej si es usuario autorizado
allow read, write: if request.auth != null;
// ej solo para los datos propios del usuario
allow read, update, delete: if request.auth != null && request.auth.uid == userId;

// condición que se basa en datos del depósito
allow update: if request.resource.data.miVariable == 'valor'

// condición que se basa en la presencia de otros documentos
// aquí solo crear si existe id del usuario en otro documento
allow create: if request.auth != null && exists(/databases/$(database)/documents/usuarios/$(request.auth.uid))

// este ejemplo les permite a todos los usuarios escribir y leer todos los documentos
match /databases/{database}/documents {
	match /{document=**} {
	  allow read, write: if request.auth != null;
	}
}

// función para envolver la condición
match /databases/{database}/documents {
	function esUsuario() {
		return request.auth != null || resource.data.visibilidad == 'público'
	}
	match /ciudades/{ciudad} {
		allow read: if esUsuario();
	}
	...
}
// no son filtros - una consulta amplísima no está permitida - en el ej anterior
// Sí:
db.collection('ciudades').where('visibilidad', '==', 'público').get()...
// No:
db.collection('ciudades').get()...

```

## Running Locally

- Ensure you have [Node.js](https://nodejs.org/en/) installed
- Clone this repo
- Run `npm install`
- Run `npm start`
- A local version of the app will be available at http://localhost:3000

