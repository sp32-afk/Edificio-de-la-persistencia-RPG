# **Sesión 1 (15/11/2026):**



* ## **Código de guardado:**

&#x20;

EP2.eyJ2IjoyLCJ3IjoiU2VyZ2lvIFBhc3RyYW5hIDIwMjUyNTc4MDg2IiwiZyI6IjMwMiIsInMiOiIiLCJlIjoiIiwicCI6IjAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAifQ.utImr9369WX8-AoX



====================================================================================================



* ### Bóveda\_Datos: MariaDB y MongoDB - Sala B.1 - Leer un personaje:



Un mismo personaje vive distinto en cada motor. En MariaDB está repartido en dos tablas y hay que unirlas con un JOIN para saber su clase. En MongoDB es un solo documento que ya trae la clase adentro.



\---------------------------------------------------------------------------------------------------



##### db/consultas\_repositorio.sql - MariaDB\_SQL



\-- Archivo: db/consultas\_repositorio.sql (consulta 1)



\-- ── 1. Leer un personaje (buscarPorId) ───────────────────────────────

\-- La clase no está como texto en personaje: llega desde la tabla clase.

SELECT p.id\_personaje, p.nombre, c.nombre AS clase,

&#x20;      p.nivel, p.vida, p.ataque, p.oro, p.mana, p.precision\_tiro

&#x20; FROM personaje p

&#x20; JOIN clase c ON p.id\_clase = c.id\_clase

&#x20;WHERE p.id\_personaje = 2;



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/consultas\_repositorio.sql, entre las marcas >>> SALA B.1 y <<< FIN SALA B.1.



\---------------------------------------------------------------------------------------------------



##### db/consultas\_repositorio.js - MongoDB\_JS



// Archivo: db/consultas\_repositorio.js (consulta 1)



// ── 1. Leer un personaje (buscarPorId) ───────────────────────────────

// La clase viaja dentro del documento: no hace falta ningún JOIN.

db.personajes.findOne(

&#x20; { id\_personaje: 2 },

&#x20; { \_id: 0, inventario: 0, misiones: 0 }

);



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/consultas\_repositorio.js, entre las marcas >>> SALA B.1 y <<< FIN SALA B.1.



\---------------------------------------------------------------------------------------------------



MariaDB vs MongoDB. En SQL las cláusulas tienen un orden fijo: SELECT, FROM, JOIN, WHERE. En MongoDB, findOne recibe primero el filtro (qué documento) y después la proyección (qué campos).



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué en MariaDB necesitas el JOIN clase para saber que Elara es Mago, y en MongoDB no?



====================================================================================================



* ### Bóveda -  Datos: MariaDB y MongoDB - Sala B.2 - El inventario: JOIN o documento:



En MariaDB el inventario es una tabla intermedia N:M: guarda pares (personaje, ítem) y la cantidad, pero el nombre del ítem está en la tabla item. En MongoDB el inventario es un arreglo dentro del documento del personaje.



\---------------------------------------------------------------------------------------------------



##### db/consultas\_repositorio.sql - MariaDB\_SQL



\-- Archivo: db/consultas\_repositorio.sql (consulta 2)



\-- ── 2. El inventario de un personaje (listarInventario) ──────────────

\-- inventario es la tabla intermedia N:M: el nombre del ítem vive en item.

SELECT i.nombre, i.tipo, i.valor, inv.cantidad

&#x20; FROM inventario inv

&#x20; JOIN item i ON inv.id\_item = i.id\_item

&#x20;WHERE inv.id\_personaje = 1

&#x20;ORDER BY i.nombre;



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/consultas\_repositorio.sql, entre las marcas >>> SALA B.2 y <<< FIN SALA B.2.



\---------------------------------------------------------------------------------------------------



##### db/consultas\_repositorio.js - MongoDB\_JS



// Archivo: db/consultas\_repositorio.js (consulta 2)



// ── 2. El inventario de un personaje (listarInventario) ──────────────

// El inventario es un arreglo embebido: se pide solo ese campo.

db.personajes.findOne(

&#x20; { id\_personaje: 1 },

&#x20; { \_id: 0, nombre: 1, inventario: 1 }

);



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/consultas\_repositorio.js, entre las marcas >>> SALA B.2 y <<< FIN SALA B.2.



\---------------------------------------------------------------------------------------------------



MariaDB vs MongoDB. SQL arma el resultado uniendo tres ideas: inventario, item y el filtro por personaje. MongoDB no une nada: pide el documento y se queda solo con el campo inventario usando la proyección { inventario: 1 }.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

Si cambia el precio de la Poción de vida, ¿en cuántos lugares lo cambias en MariaDB y en cuántos en MongoDB? ¿Qué ventaja y qué riesgo tiene cada modelo?



====================================================================================================



* ### Bóveda - Datos: MariaDB y MongoDB - Sala B.3 - Llaves foráneas: el caso Thorgar



La tabla combate apunta a personaje con tres llaves foráneas y sin ON DELETE CASCADE. Por eso MariaDB no deja borrar a Thorgar mientras tenga combates: responde con el error 1451. En MongoDB los combates guardan nombres, así que el borrado sí pasa y deja combates huérfanos.



\---------------------------------------------------------------------------------------------------



##### db/consultas\_repositorio.sql - MariaDB\_SQL



\-- Archivo: db/consultas\_repositorio.sql (consulta 3)



\-- ── 3. ¿Por qué no se puede borrar a Thorgar? (eliminar) ─────────────

\-- combate apunta a personaje con tres llaves foráneas y sin CASCADE.

SELECT COUNT(\*) AS combates\_de\_thorgar

&#x20; FROM combate

&#x20;WHERE id\_atacante = 1

&#x20;   OR id\_defensor = 1

&#x20;   OR id\_ganador = 1;

\-- Esta línea falla con el error 1451 (llave foránea en uso):

\-- DELETE FROM personaje WHERE id\_personaje = 1;



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/consultas\_repositorio.sql, entre las marcas >>> SALA B.3 y <<< FIN SALA B.3.



\---------------------------------------------------------------------------------------------------



##### db/consultas\_repositorio.js - MongoDB\_JS



// Archivo: db/consultas\_repositorio.js (consulta 3)



// ── 3. ¿Se puede borrar a Thorgar? (eliminar) ────────────────────────

// combates guarda NOMBRES, no llaves foráneas: nada impide el borrado.

db.combates.countDocuments({

&#x20; $or: \[

&#x20;   { atacante: "Thorgar" },

&#x20;   { defensor: "Thorgar" },

&#x20;   { ganador: "Thorgar" }

&#x20; ]

});

// Esta línea SÍ funciona en MongoDB (cuidado: deja combates huérfanos):

// db.personajes.deleteOne({ id\_personaje: 1 });



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/consultas\_repositorio.js, entre las marcas >>> SALA B.3 y <<< FIN SALA B.3.



\---------------------------------------------------------------------------------------------------



MariaDB vs MongoDB. En SQL las condiciones se encadenan con OR dentro del WHERE. En MongoDB se escribe $or con un arreglo de condiciones.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

La misma operación del contrato, eliminar(1), falla en MariaDB y funciona en MongoDB. ¿Cuál comportamiento te parece mejor para el juego y por qué?



====================================================================================================



* ### Bóveda-Datos: MariaDB y MongoDB - Sala B.4 - Preparar la persistencia:



Antes de conectar la app hay que ajustar los datos de la nivelación. En MariaDB, alter\_persistencia.sql agrega las columnas mana y precision\_tiro y les da un valor inicial. En MongoDB, mongo\_persistencia.js pasa la precisión del Arquero de 0.72 a 72 y numera los personajes.



\---------------------------------------------------------------------------------------------------



##### db/alter\_persistencia.sql - MariaDB\_SQL



\-- Archivo: db/alter\_persistencia.sql (fragmento)

ALTER TABLE personaje

&#x20; ADD COLUMN IF NOT EXISTS mana           INT NULL CHECK (mana >= 0),

&#x20; ADD COLUMN IF NOT EXISTS precision\_tiro INT NULL CHECK (precision\_tiro BETWEEN 0 AND 100);



\-- Valores iniciales para los personajes que ya existían

UPDATE personaje p JOIN clase c ON p.id\_clase = c.id\_clase

&#x20;  SET p.mana = 100

&#x20;WHERE c.nombre = 'Mago' AND p.mana IS NULL;



UPDATE personaje p JOIN clase c ON p.id\_clase = c.id\_clase

&#x20;  SET p.precision\_tiro = 70

&#x20;WHERE c.nombre = 'Arquero' AND p.precision\_tiro IS NULL;



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/alter\_persistencia.sql, entre las marcas >>> SALA B.4 y <<< FIN SALA B.4.



\---------------------------------------------------------------------------------------------------



##### db/mongo\_persistencia.js - MongoDB\_JS



// Archivo: db/mongo\_persistencia.js (fragmento)



// 1. Precisión del Arquero: de fracción (0.72) a entero de 0 a 100 (72)

const arqueros = db.personajes.find({ precision: { $lt: 1 } }).toArray();

arqueros.forEach(doc => {

&#x20; db.personajes.updateOne(

&#x20;   { \_id: doc.\_id },

&#x20;   { $set: { precision: Math.round(doc.precision \* 100) } }

&#x20; );

});

print("Arqueros ajustados: " + arqueros.length);



\----------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en db/mongo\_persistencia.js, entre las marcas >>> SALA B.4 y <<< FIN SALA B.4.



\---------------------------------------------------------------------------------------------------



MariaDB vs MongoDB. En SQL se cambia la estructura de la tabla con ALTER TABLE y los datos con UPDATE. En MongoDB no hay estructura que cambiar: se recorren los documentos y se corrige cada uno con updateOne y $set.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué en MariaDB hubo que hacer ALTER TABLE para guardar el mana, mientras que en MongoDB los Magos ya lo tenían?

