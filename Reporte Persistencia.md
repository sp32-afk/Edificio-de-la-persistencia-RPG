# Sesión 3 (17/11/2026):



* ## Código de guardado:

&#x20;

EP2.eyJ2IjoyLCJ3IjoiU2VyZ2lvIFBhc3RyYW5hIDIwMjUyNTc4MDg2IiwiZyI6IjMwMiIsInMiOiIyMDI2LTExLTE2VDAyOjQyOjE2LjI2OFoiLCJlIjoiIiwicCI6IjMzMzMzMzMzMzMzMzMzMzMwMDAwMDAwMDAifQ.FdpCFEeVS9ZskHC1



====================================================================================================

* ### Piso 2 - Persistencia - Sala 2.1 - El contrato



El contrato dice QUÉ operaciones ofrece cualquier repositorio, no CÓMO se hacen. Las tres implementaciones (memoria, MariaDB, MongoDB) lo cumplen completo, y el Service solo conoce el contrato.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/PersonajeRepository.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/PersonajeRepository.java (interface)



import gestionrpg.modelo.Item;

import gestionrpg.modelo.Personaje;

import java.util.List;



/\*\*

&#x20;\* Contrato de la capa de persistencia: QUÉ operaciones ofrece cualquier

&#x20;\* repositorio, no CÓMO se hacen. Si una clase que lo implementa olvida un

&#x20;\* método, no compila.

&#x20;\*/

public interface PersonajeRepository {



&#x20;   /\*\* Guarda un personaje nuevo, le asigna su id y lo devuelve. \*/

&#x20;   Personaje guardar(Personaje p);



&#x20;   /\*\* Devuelve el personaje con ese id, o null si no existe. \*/

&#x20;   Personaje buscarPorId(int id);



&#x20;   /\*\* Devuelve una lista NUEVA con todos los personajes. \*/

&#x20;   List<Personaje> listarTodos();



&#x20;   /\*\* Devuelve una lista nueva con los personajes de esa clase. \*/

&#x20;   List<Personaje> listarPorClase(String clase);



&#x20;   /\*\* Guarda los cambios de un personaje existente. true si existía. \*/

&#x20;   boolean actualizar(Personaje p);



&#x20;   /\*\* Borra el personaje. true si existía. \*/

&#x20;   boolean eliminar(int id);



&#x20;   /\*\* Devuelve los ítems del personaje (lista vacía si no tiene). \*/

&#x20;   List<Item> listarInventario(int idPersonaje);



&#x20;   /\*\* Devuelve todos los ítems que existen en el juego (la tabla item). \*/

&#x20;   List<Item> listarCatalogo();



&#x20;   /\*\*

&#x20;    \* Suma esa cantidad del ítem al inventario. Si ya lo tenía, se acumula.

&#x20;    \* false si el ítem no existe en el catálogo.

&#x20;    \*/

&#x20;   boolean agregarItem(int idPersonaje, String nombreItem, int cantidad);



&#x20;   /\*\* Saca el ítem completo del inventario. true si lo tenía. \*/

&#x20;   boolean quitarItem(int idPersonaje, String nombreItem);

}



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/PersonajeRepository.java, entre las marcas >>> SALA 2.1 y <<< FIN SALA 2.1.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/personaje\_repository.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/personaje\_repository.py (clase abstracta)



"""Contrato de la capa de persistencia.



Dice QUÉ operaciones debe ofrecer cualquier repositorio, no CÓMO se hacen.

Python no deja crear un objeto de una clase que no implemente todos los

métodos abstractos (lanza TypeError).

"""

from abc import ABC, abstractmethod





class PersonajeRepository(ABC):



&#x20;   @abstractmethod

&#x20;   def guardar(self, personaje):

&#x20;       """Guarda un personaje nuevo, le asigna su id y lo devuelve."""



&#x20;   @abstractmethod

&#x20;   def buscar\_por\_id(self, id):

&#x20;       """Devuelve el personaje con ese id, o None si no existe."""



&#x20;   @abstractmethod

&#x20;   def listar\_todos(self):

&#x20;       """Devuelve una lista NUEVA con todos los personajes."""



&#x20;   @abstractmethod

&#x20;   def listar\_por\_clase(self, clase):

&#x20;       """Devuelve una lista nueva con los personajes de esa clase."""



&#x20;   @abstractmethod

&#x20;   def actualizar(self, personaje):

&#x20;       """Guarda los cambios de un personaje existente. True si existía."""



&#x20;   @abstractmethod

&#x20;   def eliminar(self, id):

&#x20;       """Borra el personaje. True si existía."""



&#x20;   @abstractmethod

&#x20;   def listar\_inventario(self, id\_personaje):

&#x20;       """Devuelve la lista de ítems del personaje (vacía si no tiene)."""



&#x20;   @abstractmethod

&#x20;   def listar\_catalogo(self):

&#x20;       """Devuelve todos los ítems que existen en el juego (la tabla item)."""



&#x20;   @abstractmethod

&#x20;   def agregar\_item(self, id\_personaje, nombre\_item, cantidad):

&#x20;       """Suma esa cantidad del ítem al inventario. Si ya lo tenía, se acumula.

&#x20;       False si el ítem no existe en el catálogo."""



&#x20;   @abstractmethod

&#x20;   def quitar\_item(self, id\_personaje, nombre\_item):

&#x20;       """Saca el ítem completo del inventario. True si lo tenía."""



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/personaje\_repository.py, entre las marcas >>> SALA 2.1 y <<< FIN SALA 2.1.



\---------------------------------------------------------------------------------------------------



Java vs Python. En Java el contrato es una interface y las clases dicen implements: si falta un método, no compila. En Python es una clase que hereda de ABC con @abstractmethod: si falta un método, crear el objeto lanza TypeError.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué buscarPorId devuelve null / None en vez de lanzar un error cuando el id no existe? ¿Quién decide que eso es un error?



====================================================================================================



* ### Piso 2 - Persistencia - Sala 2.2 - RepositorioMemoria



La implementación más simple: un HashMap / dict con el id como clave y un contador que imita el AUTO\_INCREMENT. Sirve para probar sin base de datos; al reiniciar el servidor todo se pierde.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMemoria.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMemoria.java (fragmento)



&#x20;   private final Map<Integer, Personaje> datos = new HashMap<>();

&#x20;   private final Map<Integer, List<Item>> inventarios = new HashMap<>();

&#x20;   private int siguienteId = 1;    // como AUTO\_INCREMENT: nunca reutiliza un id



&#x20;   @Override

&#x20;   public Personaje guardar(Personaje p) {

&#x20;       // Misma regla que el UNIQUE de MariaDB sobre el nombre

&#x20;       for (Personaje existente : datos.values()) {

&#x20;           if (existente.getNombre().equals(p.getNombre())) {     // equals, no ==

&#x20;               throw new DatoDuplicadoException(p.getNombre(), null);

&#x20;           }

&#x20;       }

&#x20;       p.setId(siguienteId++);

&#x20;       datos.put(p.getId(), p);

&#x20;       inventarios.put(p.getId(), new ArrayList<>());

&#x20;       return p;

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMemoria.java, entre las marcas >>> SALA 2.2 y <<< FIN SALA 2.2.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/personaje\_memoria.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repositorio\_memoria.py (fragmento)



&#x20;   def \_\_init\_\_(self):

&#x20;       self.\_datos = {}          # id -> Personaje

&#x20;       self.\_inventarios = {}    # id -> lista de Item

&#x20;       self.\_siguiente\_id = 1    # como AUTO\_INCREMENT: nunca reutiliza un id



&#x20;   def guardar(self, personaje):

&#x20;       # Misma regla que el UNIQUE de MariaDB sobre el nombre

&#x20;       for existente in self.\_datos.values():

&#x20;           if existente.nombre == personaje.nombre:

&#x20;               raise DatoDuplicadoException(personaje.nombre)

&#x20;       personaje.id = self.\_siguiente\_id

&#x20;       self.\_siguiente\_id += 1

&#x20;       self.\_datos\[personaje.id] = personaje

&#x20;       self.\_inventarios\[personaje.id] = \[]

&#x20;       return personaje



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repositorio\_memoria.py, entre las marcas >>> SALA 2.2 y <<< FIN SALA 2.2.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java recorre datos.values() con un for-each y compara nombres con .equals(), nunca con ==. Python recorre self.\_datos.values() y compara con ==, que en Python sí compara el contenido.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

El repositorio en memoria revisa que no haya dos personajes con el mismo nombre. ¿A qué regla de MariaDB imita, y por qué tiene que hacerlo a mano?



====================================================================================================



* ### Piso 2 - Persistencia - Sala 2.3 - MariaDB: leer un personaje



Para leer, el repositorio abre una conexión, prepara la consulta con parámetros, la ejecuta y convierte cada fila en un objeto con la fábrica. Las tres lecturas (por id, todos y por clase) usan el mismo método auxiliar.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java (fragmento)



&#x20;   private List<Personaje> consultar(String sql, Object... parametros) {

&#x20;       List<Personaje> resultado = new ArrayList<>();

&#x20;       try (Connection con = conectar();

&#x20;            PreparedStatement ps = con.prepareStatement(sql)) {

&#x20;           for (int i = 0; i < parametros.length; i++) {

&#x20;               ps.setObject(i + 1, parametros\[i]);

&#x20;           }

&#x20;           try (ResultSet rs = ps.executeQuery()) {

&#x20;               while (rs.next()) {

&#x20;                   resultado.add(filaAPersonaje(rs));

&#x20;               }

&#x20;           }

&#x20;       } catch (SQLException e) {

&#x20;           throw new AccesoDatosException("Error consultando personajes", e);

&#x20;       }

&#x20;       return resultado;

&#x20;   }



&#x20;   @Override

&#x20;   public Personaje buscarPorId(int id) {

&#x20;       List<Personaje> encontrados = consultar(SELECT\_PERSONAJE + " WHERE p.id\_personaje = ?", id);

&#x20;       return encontrados.isEmpty() ? null : encontrados.get(0);

&#x20;   }





\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java, entre las marcas >>> SALA 2.3 y <<< FIN SALA 2.3.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py (fragmento)



&#x20;   def \_consultar\_personajes(self, sql, parametros=()):

&#x20;       resultado = \[]

&#x20;       with self.\_conectar() as conexion, conexion.cursor() as cursor:

&#x20;           cursor.execute(sql, parametros)

&#x20;           for fila in cursor.fetchall():

&#x20;               resultado.append(self.\_fila\_a\_personaje(fila))

&#x20;       return resultado



&#x20;   def buscar\_por\_id(self, id):

&#x20;       encontrados = self.\_consultar\_personajes(SELECT\_PERSONAJE + " WHERE p.id\_personaje = %s", (id,))

&#x20;       return encontrados\[0] if encontrados else None



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py, entre las marcas >>> SALA 2.3 y <<< FIN SALA 2.3.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java usa JDBC: PreparedStatement, ResultSet y try-with-resources para cerrar todo solo. Python usa pymysql con with; el cursor DictCursor devuelve cada fila como un diccionario.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Qué pasaría si no cerraras la conexión después de cada consulta? ¿Qué parte del código se encarga de cerrarla?



====================================================================================================



* ### Piso 2 - Persistencia - Sala 2.4 - MariaDB: guardar con INSERT



Guardar es un INSERT con parámetros. Después hay que recuperar el id que generó el AUTO\_INCREMENT y ponérselo al objeto. Si el nombre ya existe, MariaDB responde con el error 1062 y el repositorio lo traduce a DatoDuplicadoException.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java- Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java (fragmento)



&#x20;   @Override

&#x20;   public Personaje guardar(Personaje p) {

&#x20;       String sql = "INSERT INTO personaje (nombre, id\_clase, nivel, vida, ataque, oro, mana, precision\_tiro)"

&#x20;               + " VALUES (?, ?, ?, ?, ?, ?, ?, ?)";

&#x20;       try (Connection con = conectar();

&#x20;            PreparedStatement ps = con.prepareStatement(sql, Statement.RETURN\_GENERATED\_KEYS)) {

&#x20;           ponerCampos(ps, p);

&#x20;           ps.executeUpdate();          // JDBC hace commit automático (autocommit)

&#x20;           try (ResultSet claves = ps.getGeneratedKeys()) {

&#x20;               if (claves.next()) {

&#x20;                   p.setId(claves.getInt(1));    // id generado por AUTO\_INCREMENT

&#x20;               }

&#x20;           }

&#x20;           return p;

&#x20;       } catch (SQLException e) {

&#x20;           if (e.getErrorCode() == ERROR\_DUPLICADO) {

&#x20;               throw new DatoDuplicadoException(p.getNombre(), e);

&#x20;           }

&#x20;           throw new AccesoDatosException("Error guardando personaje", e);

&#x20;       }

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java, entre las marcas >>> SALA 2.4 y <<< FIN SALA 2.4.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py- Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py (fragmento)



&#x20;   def guardar(self, personaje):

&#x20;       sql = """INSERT INTO personaje

&#x20;                    (nombre, id\_clase, nivel, vida, ataque, oro, mana, precision\_tiro)

&#x20;                VALUES (%s, %s, %s, %s, %s, %s, %s, %s)"""

&#x20;       valores = (personaje.nombre, self.\_id\_clase\[personaje.clase], personaje.nivel,

&#x20;                  personaje.vida, personaje.ataque, personaje.oro,

&#x20;                  getattr(personaje, "mana", None), getattr(personaje, "precision", None))

&#x20;       with self.\_conectar() as conexion:

&#x20;           try:

&#x20;               with conexion.cursor() as cursor:

&#x20;                   cursor.execute(sql, valores)

&#x20;                   personaje.id = cursor.lastrowid    # id generado por AUTO\_INCREMENT

&#x20;               conexion.commit()                       # sin commit, el INSERT no queda guardado

&#x20;           except pymysql.err.IntegrityError as error:

&#x20;               conexion.rollback()

&#x20;               if error.args\[0] == ERROR\_DUPLICADO:

&#x20;                   raise DatoDuplicadoException(personaje.nombre) from error

&#x20;               raise

&#x20;       return personaje



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py, entre las marcas >>> SALA 2.4 y <<< FIN SALA 2.4.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java pide las llaves generadas con RETURN\_GENERATED\_KEYS y getGeneratedKeys(); JDBC confirma solo (autocommit). Python lee cursor.lastrowid y debe llamar conexion.commit(), o el INSERT se pierde.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué los valores van como ? o %s y nunca pegados al texto del SQL? Da un ejemplo de nombre que rompería la consulta.



====================================================================================================



* ### Piso 2 - Persistencia - Sala 2.5 - MariaDB: eliminar y el error 1451



Eliminar es un DELETE. Si el personaje tiene combates, MariaDB no lo deja borrar y responde con el error 1451. El repositorio no escribe mensajes para el usuario: traduce ese error a DatoEnUsoException y el Service decide qué decir.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java (fragmento)



&#x20;   @Override

&#x20;   public boolean eliminar(int id) {

&#x20;       try (Connection con = conectar();

&#x20;            PreparedStatement ps = con.prepareStatement("DELETE FROM personaje WHERE id\_personaje = ?")) {

&#x20;           ps.setInt(1, id);

&#x20;           return ps.executeUpdate() > 0;

&#x20;       } catch (SQLException e) {

&#x20;           if (e.getErrorCode() == ERROR\_FK\_EN\_USO) {

&#x20;               // inventario y personaje\_mision se borran en cascada, combate no

&#x20;               throw new DatoEnUsoException(id, e);

&#x20;           }

&#x20;           throw new AccesoDatosException("Error eliminando personaje", e);

&#x20;       }

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMariaDB.java, entre las marcas >>> SALA 2.5 y <<< FIN SALA 2.5



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py (fragmento)



&#x20;   def eliminar(self, id):

&#x20;       with self.\_conectar() as conexion:

&#x20;           try:

&#x20;               with conexion.cursor() as cursor:

&#x20;                   filas = cursor.execute("DELETE FROM personaje WHERE id\_personaje = %s", (id,))

&#x20;               conexion.commit()

&#x20;           except pymysql.err.IntegrityError as error:

&#x20;               conexion.rollback()

&#x20;               if error.args\[0] == ERROR\_FK\_EN\_USO:

&#x20;                   # inventario y personaje\_mision se borran en cascada, combate no

&#x20;                   raise DatoEnUsoException(id) from error

&#x20;               raise

&#x20;       return filas > 0





\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mariadb.py, entre las marcas >>> SALA 2.5 y <<< FIN SALA 2.5.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java revisa e.getErrorCode() de la SQLException. Python atrapa pymysql.err.IntegrityError y mira error.args\[0]; además hace rollback() antes de traducir.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué el repositorio lanza DatoEnUsoException en vez de mostrar directamente "No se puede eliminar: tiene combates"?



====================================================================================================



* ### Piso 2 - Persistencia - Sala 2.6 - MongoDB: guardar con contador



MongoDB no tiene AUTO\_INCREMENT. El repositorio usa la colección contadores y $inc para sacar el siguiente número. Antes revisa que no exista otro personaje con ese nombre, y el índice único es la segunda defensa.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMongo.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMongo.java (fragmento)



&#x20;   @Override

&#x20;   public Personaje guardar(Personaje p) {

&#x20;       if (personajes.find(eq("nombre", p.getNombre())).first() != null) {

&#x20;           throw new DatoDuplicadoException(p.getNombre(), null);

&#x20;       }

&#x20;       Document documento = camposDe(p)

&#x20;               .append("inventario", new ArrayList<Document>())

&#x20;               .append("misiones", new ArrayList<Document>())

&#x20;               .append("id\_personaje", siguienteId());

&#x20;       try {

&#x20;           personajes.insertOne(documento);

&#x20;       } catch (MongoWriteException e) {

&#x20;           if (esDuplicado(e)) {

&#x20;               throw new DatoDuplicadoException(p.getNombre(), e);

&#x20;           }

&#x20;           throw e;

&#x20;       }

&#x20;       p.setId(documento.getInteger("id\_personaje"));

&#x20;       return p;

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMongo.java, entre las marcas >>> SALA 2.6 y <<< FIN SALA 2.6.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mongo.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mongo.py (fragmento)



&#x20;   def guardar(self, personaje):

&#x20;       documento = self.\_campos\_de(personaje)

&#x20;       documento\["inventario"] = \[]

&#x20;       documento\["misiones"] = \[]

&#x20;       if self.\_personajes.find\_one({"nombre": personaje.nombre}) is not None:

&#x20;           raise DatoDuplicadoException(personaje.nombre)

&#x20;       documento\["id\_personaje"] = self.\_siguiente\_id()

&#x20;       try:

&#x20;           self.\_personajes.insert\_one(documento)

&#x20;       except DuplicateKeyError as error:

&#x20;           raise DatoDuplicadoException(personaje.nombre) from error

&#x20;       personaje.id = documento\["id\_personaje"]

&#x20;       return personaje



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mongo.py, entre las marcas >>> SALA 2.6 y <<< FIN SALA 2.6.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java arma un Document con append y atrapa MongoWriteException. Python arma un diccionario y atrapa DuplicateKeyError.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué el siguiente id se pide con $inc en una sola operación y no leyendo el último id y sumándole 1?



====================================================================================================





* ### Piso 2 - Persistencia - Sala 2.7 - El selector: fábrica y configuración



La fábrica lee el archivo de configuración y crea la implementación del contrato que corresponda. Es el único lugar del proyecto que sabe qué motor hay detrás. Cambiar de MariaDB a MongoDB es cambiar una línea de config.properties o config.ini.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositoryFactory.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositoryFactory.java (fragmento)



&#x20;   public static PersonajeRepository crear(Properties cfg) {

&#x20;       String tipo = cfg.getProperty("repositorio", "").trim().toLowerCase();

&#x20;       switch (tipo) {

&#x20;           case "memoria":

&#x20;               return new RepositorioMemoria();

&#x20;           case "mariadb":

&#x20;               return new RepositorioMariaDB(

&#x20;                       cfg.getProperty("mariadb.host"),

&#x20;                       cfg.getProperty("mariadb.puerto"),

&#x20;                       cfg.getProperty("mariadb.base"),

&#x20;                       cfg.getProperty("mariadb.usuario"),

&#x20;                       cfg.getProperty("mariadb.clave"));

&#x20;           case "mongo":

&#x20;               return new RepositorioMongo(cfg.getProperty("mongo.uri"), cfg.getProperty("mongo.base"));

&#x20;           default:

&#x20;               // Mejor que la app no arranque a que falle en la primera petición

&#x20;               throw new IllegalStateException(

&#x20;                       "Repositorio desconocido: '" + tipo + "'. Usa memoria, mariadb o mongo.");

&#x20;       }

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositoryFactory.java, entre las marcas >>> SALA 2.7 y <<< FIN SALA 2.7.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/repository\_factory.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repository\_factory.py (fragmento)



def crear\_repositorio(cfg):

&#x20;   tipo = cfg.get("app", "repositorio", fallback="").strip().lower()

&#x20;   if tipo == "memoria":

&#x20;       return RepositorioMemoria()

&#x20;   elif tipo == "mariadb":

&#x20;       return RepositorioMariaDB(

&#x20;           host=cfg.get("mariadb", "host"),

&#x20;           puerto=cfg.get("mariadb", "puerto"),

&#x20;           base=cfg.get("mariadb", "base"),

&#x20;           usuario=cfg.get("mariadb", "usuario"),

&#x20;           clave=cfg.get("mariadb", "clave"))

&#x20;   elif tipo == "mongo":

&#x20;       return RepositorioMongo(uri=cfg.get("mongo", "uri"), base=cfg.get("mongo", "base"))

&#x20;   # Mejor que la app no arranque a que falle en la primera petición

&#x20;   raise ValueError(f"Repositorio desconocido: '{tipo}'. Usa memoria, mariadb o mongo.")



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repository\_factory.py, entre las marcas >>> SALA 2.7 y <<< FIN SALA 2.7.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java lee Properties con getProperty y decide con switch. Python lee configparser por secciones (\[app], \[mariadb]) y decide con if / elif. Los dos devuelven el tipo del contrato.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

El archivo real de configuración va en .gitignore y al repositorio solo se sube el config.example. ¿Por qué?



====================================================================================================



* ### Piso 2 - Persistencia - Sala 2.8 - Inventario: agregar un ítem



Agregar un ítem tiene dos casos: si el personaje ya lo tiene, se suma la cantidad; si no, se agrega. MongoDB lo resuelve en dos pasos: $inc sobre el elemento del arreglo y, si no lo encontró, $push con los datos copiados del catálogo items. MariaDB lo hace en una sola instrucción con INSERT … ON DUPLICATE KEY UPDATE.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMongo.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMongo.java (fragmento)



&#x20;   @Override

&#x20;   public boolean agregarItem(int idPersonaje, String nombreItem, int cantidad) {

&#x20;       // 1. Si ya lo tiene: $inc sobre ese elemento del arreglo ($ = el que cumplió el filtro)

&#x20;       long encontrados = personajes.updateOne(

&#x20;               and(eq("id\_personaje", idPersonaje), eq("inventario.item", nombreItem)),

&#x20;               Updates.inc("inventario.$.cantidad", cantidad)).getMatchedCount();

&#x20;       if (encontrados > 0) {

&#x20;           return true;

&#x20;       }

&#x20;       // 2. Si no lo tiene: se copia del catálogo y se agrega al arreglo con $push

&#x20;       Document delCatalogo = items.find(eq("nombre", nombreItem)).first();

&#x20;       if (delCatalogo == null) {

&#x20;           return false;

&#x20;       }

&#x20;       Document nuevo = new Document("item", delCatalogo.getString("nombre"))

&#x20;               .append("tipo", delCatalogo.getString("tipo"))

&#x20;               .append("valor", numero(delCatalogo.get("valor"), 0))

&#x20;               .append("cantidad", cantidad);

&#x20;       return personajes.updateOne(eq("id\_personaje", idPersonaje),

&#x20;               Updates.push("inventario", nuevo)).getMatchedCount() > 0;

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/repositorio/RepositorioMongo.java, entre las marcas >>> SALA 2.8 y <<< FIN SALA 2.8.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mongo.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mongo.py (fragmento)



&#x20;   def agregar\_item(self, id\_personaje, nombre\_item, cantidad):

&#x20;       # 1. Si ya lo tiene: $inc sobre ese elemento del arreglo ($ = el que cumplió el filtro)

&#x20;       resultado = self.\_personajes.update\_one(

&#x20;           {"id\_personaje": id\_personaje, "inventario.item": nombre\_item},

&#x20;           {"$inc": {"inventario.$.cantidad": cantidad}})

&#x20;       if resultado.matched\_count > 0:

&#x20;           return True

&#x20;       # 2. Si no lo tiene: se copia del catálogo y se agrega al arreglo con $push

&#x20;       del\_catalogo = self.\_items.find\_one({"nombre": nombre\_item})

&#x20;       if del\_catalogo is None:

&#x20;           return False

&#x20;       nuevo = {"item": del\_catalogo\["nombre"], "tipo": del\_catalogo\["tipo"],

&#x20;                "valor": del\_catalogo\["valor"], "cantidad": cantidad}

&#x20;       resultado = self.\_personajes.update\_one({"id\_personaje": id\_personaje},

&#x20;                                               {"$push": {"inventario": nuevo}})

&#x20;       return resultado.matched\_count > 0



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/repositorio/repositorio\_mongo.py, entre las marcas >>> SALA 2.8 y <<< FIN SALA 2.8.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java usa Updates.inc y Updates.push y lee getMatchedCount(). Python escribe los operadores como diccionarios ({"$inc": ...}) y lee matched\_count. El $ en inventario.$.cantidad es el elemento que cumplió el filtro.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

Compara cómo cada motor resuelve "sumar si ya existe": ¿qué hace MariaDB con ON DUPLICATE KEY UPDATE y qué hace MongoDB con $inc y $push?



====================================================================================================



