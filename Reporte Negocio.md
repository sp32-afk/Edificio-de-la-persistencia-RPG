# Sesión 4 (20/11/2026):



* ## Código de guardado:

&#x20;

EP2.eyJ2IjoyLCJ3IjoiU2VyZ2lvIFBhc3RyYW5hIDIwMjUyNTc4MDg2IiwiZyI6IjMwMiIsInMiOiIyMDI2LTExLTE2VDAyOjQyOjE2LjI2OFoiLCJlIjoiIiwicCI6IjMzMzMzMzMzMzMzMzMzMzMwMDAwMDAwMDAifQ.FdpCFEeVS9ZskHC1



====================================================================================================



* ### Piso 3 - Negocio - Sala 3.1 - Inyección por constructor



El Service recibe el repositorio por el constructor en vez de crearlo él. Su atributo es del tipo del contrato, así que no sabe si detrás hay memoria, MariaDB o MongoDB. Quien decide es el arranque, con la fábrica.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java (inicio de la clase)



public class PersonajeService {



&#x20;   private final PersonajeRepository repositorio;



&#x20;   public PersonajeService(PersonajeRepository repositorio) {

&#x20;       this.repositorio = repositorio;

&#x20;   }



&#x20;   // ---------- consultas ----------



&#x20;   public List<Personaje> listar() {

&#x20;       return repositorio.listarTodos();

&#x20;   }



&#x20;   // ... (más consultas y cambios)

}



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java, entre las marcas >>> SALA 3.1 y <<< FIN SALA 3.1.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py (inicio de la clase)



class PersonajeService:



&#x20;   def \_\_init\_\_(self, repositorio):

&#x20;       self.\_repositorio = repositorio



&#x20;   # ---------- consultas ----------



&#x20;   def listar(self):

&#x20;       return self.\_repositorio.listar\_todos()



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py, entre las marcas >>> SALA 3.1 y <<< FIN SALA 3.1.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java declara el atributo como private final PersonajeRepository: no se puede reasignar. Python lo guarda en self.\_repositorio; el guion bajo indica que es de uso interno.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué el atributo del Service es de tipo PersonajeRepository y no RepositorioMariaDB?



====================================================================================================



* ### Piso 3 - Negocio - Sala 3.2 - Obtener: de null a 404



El repositorio responde null / None cuando el id no existe. El Service convierte eso en PersonajeNoEncontradoException, y el controlador la convierte en una página 404. Cada capa hace su parte.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java (fragmento)



&#x20;   public Personaje obtener(int id) {

&#x20;       Personaje p = repositorio.buscarPorId(id);

&#x20;       if (p == null) {

&#x20;           throw new PersonajeNoEncontradoException(id);

&#x20;       }

&#x20;       return p;

&#x20;   }



&#x20;   public List<Item> inventario(int id) {

&#x20;       obtener(id);    // 404 si el personaje no existe

&#x20;       return repositorio.listarInventario(id);

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java, entre las marcas >>> SALA 3.2 y <<< FIN SALA 3.2.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py (fragmento)



&#x20;   def obtener(self, id):

&#x20;       personaje = self.\_repositorio.buscar\_por\_id(id)

&#x20;       if personaje is None:

&#x20;           raise PersonajeNoEncontradoException(f"No existe el personaje {id}")

&#x20;       return personaje



&#x20;   def inventario(self, id):

&#x20;       self.obtener(id)    # 404 si el personaje no existe

&#x20;       return self.\_repositorio.listar\_inventario(id)



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py, entre las marcas >>> SALA 3.2 y <<< FIN SALA 3.2.



\---------------------------------------------------------------------------------------------------



**Java vs Python. Java compara con p == null y lanza la excepción con throw new. Python compara con is None y usa raise.**



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito



inventario(id) llama primero a obtener(id) aunque no use el personaje. ¿Para qué?



====================================================================================================



* ### Piso 3 - Negocio - Sala 3.3 - Crear: validar y traducir



El Service valida antes de tocar el repositorio: si algo falla, lanza ValidacionException y la base nunca se entera. Después arma el objeto con la fábrica, lo guarda y traduce DatoDuplicadoException a un mensaje para el usuario.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java (fragmento)



&#x20;   public Personaje crear(String nombre, String clase, int nivel, int vida, int ataque, BigDecimal oro) {

&#x20;       nombre = nombre == null ? "" : nombre.trim();

&#x20;       if (nombre.isEmpty()) {

&#x20;           throw new ValidacionException("El nombre es obligatorio");

&#x20;       }

&#x20;       if (nombre.length() > 50) {

&#x20;           throw new ValidacionException("El nombre admite máximo 50 caracteres");

&#x20;       }

&#x20;       validarClase(clase);

&#x20;       if (nivel < 1 || nivel > 100) {

&#x20;           throw new ValidacionException("El nivel debe estar entre 1 y 100");

&#x20;       }

&#x20;       if (vida < 0) {

&#x20;           throw new ValidacionException("La vida no puede ser negativa");

&#x20;       }

&#x20;       if (ataque < 0) {

&#x20;           throw new ValidacionException("El ataque no puede ser negativo");

&#x20;       }

&#x20;       if (oro == null || oro.signum() < 0) {

&#x20;           throw new ValidacionException("El oro no puede ser negativo");

&#x20;       }

&#x20;       Personaje p = FabricaPersonajes.crear(clase, null, nombre, nivel, vida, ataque,

&#x20;               oro.setScale(2, RoundingMode.HALF\_UP), null, null);

&#x20;       try {

&#x20;           return repositorio.guardar(p);

&#x20;       } catch (DatoDuplicadoException e) {

&#x20;           throw new ValidacionException("Ya existe un personaje llamado " + nombre);

&#x20;       }

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java, entre las marcas >>> SALA 3.3 y <<< FIN SALA 3.3.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py (fragmento)



&#x20;   def crear(self, nombre, clase, nivel=1, vida=100, ataque=10, oro="0"):

&#x20;       nombre = (nombre or "").strip()

&#x20;       if not nombre:

&#x20;           raise ValidacionException("El nombre es obligatorio")

&#x20;       if len(nombre) > 50:

&#x20;           raise ValidacionException("El nombre admite máximo 50 caracteres")

&#x20;       self.\_validar\_clase(clase)

&#x20;       nivel = self.\_entero(nivel, "nivel")

&#x20;       vida = self.\_entero(vida, "vida")

&#x20;       ataque = self.\_entero(ataque, "ataque")

&#x20;       oro = self.\_dinero(oro)

&#x20;       if not 1 <= nivel <= 100:

&#x20;           raise ValidacionException("El nivel debe estar entre 1 y 100")

&#x20;       if vida < 0:

&#x20;           raise ValidacionException("La vida no puede ser negativa")

&#x20;       if ataque < 0:

&#x20;           raise ValidacionException("El ataque no puede ser negativo")

&#x20;       if oro < 0:

&#x20;           raise ValidacionException("El oro no puede ser negativo")



&#x20;       personaje = crear\_personaje(clase, nombre, nivel, vida, ataque, oro)

&#x20;       try:

&#x20;           return self.\_repositorio.guardar(personaje)

&#x20;       except DatoDuplicadoException:

&#x20;           raise ValidacionException(f"Ya existe un personaje llamado {nombre}")



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py, entre las marcas >>> SALA 3.3 y <<< FIN SALA 3.3.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java recibe números ya convertidos por el controlador. Python los recibe como texto del formulario y los convierte con \_entero y \_dinero dentro del Service.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

MariaDB ya tiene un CHECK (vida >= 0). ¿Por qué el Service sigue validando que la vida no sea negativa?



====================================================================================================



* ### Piso 3 - Negocio - Sala 3.4 - Agregar ítem: reglas del negocio



Antes de agregar un ítem, el Service revisa tres reglas: que el personaje exista, que la cantidad esté entre 1 y 99 y que el ítem exista en el catálogo. Solo entonces le pide al repositorio que lo agregue.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java (fragmento)



&#x20;   public List<Item> agregarItem(int id, String nombreItem, int cantidad) {

&#x20;       obtener(id);

&#x20;       if (cantidad < 1 || cantidad > 99) {

&#x20;           throw new ValidacionException("La cantidad debe estar entre 1 y 99");

&#x20;       }

&#x20;       boolean existe = false;

&#x20;       for (Item item : repositorio.listarCatalogo()) {

&#x20;           if (item.getNombre().equals(nombreItem)) {

&#x20;               existe = true;

&#x20;           }

&#x20;       }

&#x20;       if (!existe) {

&#x20;           throw new ValidacionException("No existe el ítem " + nombreItem);

&#x20;       }

&#x20;       repositorio.agregarItem(id, nombreItem, cantidad);

&#x20;       return repositorio.listarInventario(id);

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/servicio/PersonajeService.java, entre las marcas >>> SALA 3.4 y <<< FIN SALA 3.4.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py (fragmento)



&#x20;   def agregar\_item(self, id, nombre\_item, cantidad=1):

&#x20;       self.obtener(id)

&#x20;       cantidad = self.\_entero(cantidad, "cantidad")

&#x20;       if not 1 <= cantidad <= 99:

&#x20;           raise ValidacionException("La cantidad debe estar entre 1 y 99")

&#x20;       nombres = \[]

&#x20;       for item in self.\_repositorio.listar\_catalogo():

&#x20;           nombres.append(item.nombre)

&#x20;       if nombre\_item not in nombres:

&#x20;           raise ValidacionException(f"No existe el ítem {nombre\_item}")

&#x20;       self.\_repositorio.agregar\_item(id, nombre\_item, cantidad)

&#x20;       return self.\_repositorio.listar\_inventario(id)



\----------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/servicio/personaje\_service.py, entre las marcas >>> SALA 3.4 y <<< FIN SALA 3.4.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java recorre el catálogo con un for-each y una bandera existe. Python arma una lista de nombres y pregunta con in. Los dos devuelven el inventario actualizado.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué el Service revisa que el ítem exista en el catálogo si el repositorio también devuelve false cuando no existe?

