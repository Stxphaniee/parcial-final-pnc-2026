# Stephanie Aracely Echeverria Cuellar 

## Indicaciones

Recientemente, se utilizó AI para crear un sistema de gestion de una biblioteca, el cual ha generado varios errores, su trabajo es arreglarlo. Dado el siguiente caso de uso, explique y/o resuelva cada problema según se le pida.

---

## Consideraciones

La libreria crea automaticamente un correo con los nombres de la persona

---

## Problemas

### 1. Filtro por autor y género (10%)

QA ha reportado que el endpoint para obtener los libros puede filtrar por **autor** y por **género**, o por cualquiera de los dos de manera individual.

Actualmente:

- Filtrar únicamente por autor funciona correctamente.
- Filtrar únicamente por género funciona correctamente.
- Filtrar por **autor y género al mismo tiempo** provoca que el servidor falle.

**Instrucción:** Explique la causa del problema y resuélvalo.



R: El error pasa porque cuando se filtra por autor y genero al mismo tiempo el servicio llama al metodo del repositorio
 enviando los parametros en un orden incorrecto.ademas el metodo del repositorio recibe el genero como String
cuando en la entidad Book el atributo genre es un enum (Genre) y esto provoca que Spring Data no pueda construir correctamente la consulta y el servidor falle

---

### 2. Error al volver a prestar un libro (10%)

Un usuario reportó que al pedir prestado el libro **The Selfish Gene**, devolverlo e intentar pedirlo prestado nuevamente, el servidor falla.

**Instrucción:** Explique la causa del problema y resuélvalo.

R: Cuando un libro es devuelto umicamente se incrementa la cantidad disponible (availableCount) pero nunca se actualiza el estado available
Esto provoca que un libro que anteriormente quedó marcado como no disponible (available = false) continue en ese estado aunque ya exista una copia disponible nuevamente
como el prestamo valida el atributo available el sistema impide volver a prestar el libro y genera un error

---

### 3. Cantidad de libros por género (10%)

Existe un endpoint que devuelve la cantidad de libros disponibles por género. Sin embargo, actualmente dicho endpoint falla.

**Instrucción:** Explique la causa del problema y resuélvalo.

R: El endpoint obtiene todos los libros mediante findAll() sin verificar si realmente están disponibles
Como el requerimiento es devolver la cantidad de libros disponibles por generoel resultado es incorrecto y el endpoint puede fallar dependiendo de la implementacion

---

### 4. Error al consultar un libro por ID (10%)

Un miembro del equipo de frontend reporta que la siguiente llamada falla:

```http
GET /books?id=ed16ed1e-7017-4697-a08a-d28c09a74acf
```

**Instrucción:** Explique la causa del problema.

R: El frontend realiza la petición utilizando un parametro de consulta
GET /books?id=ed16ed1e-7017-4697-a08a-d28c09a74acf
y el controlador espera recibir el identificador como una variable de ruta (@PathVariable) : GET /books/{id}
al no coincidir el tipo de ruta esperado con la petición enviada spring no encuentra el endpoint correspondiente y la solicitud falla

---

### 5. Error al crear un libro (10%)

QA ha reportado que el siguiente payload enviado al endpoint `POST /books` provoca un error:

```json
{
  "title": "Clean Code",
  "author": "Robert C. Martin",
  "genre": "classic",
  "isbn": "978-0132350884",
  "available": true,
  "availableCount": 5
}
```

**Instrucción:** Explique la causa del problema.
R:
el método valueOf() diferencia entre mayusculas y minúsculas y si el enum contiene CLASSIC, al recibir "classic" se produce una IllegalArgumentException

---

### 6. Devolución de libros no prestados (20%)

QA ha reportado que un usuario es capaz de devolver libros que nunca ha solicitado en préstamo.

**Instrucción:**

- Confirme si este comportamiento es realmente posible. SI
- Si es posible, explique la causa y resuelva el problema. 
r: el metodo encargado de devolver un libro nunca verifica que el usuario tenga un prestamo activo de ese libro unicamente obtiene el usuario y el libro y
incrementa la cantidad disponible y registra la devolucion
como no existe una validación previacu alquier usuario puede devolver cualquier libro aunque nunca lo haya solicitado en prestámo
esto genera inconsistencias en el inventario ya que la cantidad disponible puede incrementarse sin que realmente exista una devoluciOn

- Si no es posible, explique por qué, haciendo referencia al código correspondiente.

---