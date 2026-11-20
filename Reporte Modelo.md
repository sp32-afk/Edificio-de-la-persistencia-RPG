# Sesión 2 (16/11/2026):



* ## Código de guardado:

&#x20;

EP2.eyJ2IjoyLCJ3IjoiU2VyZ2lvIFBhc3RyYW5hIDIwMjUyNTc4MDg2IiwiZyI6IjMwMiIsInMiOiIyMDI2LTExLTE2VDAyOjQyOjE2LjI2OFoiLCJlIjoiIiwicCI6IjMzMzMwMDAwMDAwMDAwMDAwMDAwMDAwMDAifQ.5QH6QI-MuZmI1KbN



====================================================================================================



* ### Piso 1 - Modelo - Sala 1.1 - Personaje: la clase base



El Modelo describe los objetos del juego y no sabe nada de bases de datos. Personaje guarda lo común a todas las clases; el id empieza vacío porque lo asigna el repositorio al guardar.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Personaje.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Personaje.java (fragmento)



&#x20;   protected Personaje(Integer id, String nombre, int nivel, int vida, int ataque, BigDecimal oro) {

&#x20;       this.id = id;

&#x20;       this.nombre = nombre;

&#x20;       this.nivel = nivel;

&#x20;       this.vida = vida;

&#x20;       this.ataque = ataque;

&#x20;       this.oro = oro;

&#x20;   }



&#x20;   /\*\* "Guerrero", "Mago" o "Arquero": lo define cada subclase. \*/

&#x20;   public abstract String getClase();



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Personaje.java, entre las marcas >>> SALA 1.1 y <<< FIN SALA 1.1.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/modelo/personaje.p - Python

\# Archivo: rpg\_manager\_multinivel\_flask/modelo/personaje.py (fragmento)



class Personaje:

&#x20;   """Datos comunes a todos los personajes."""



&#x20;   clase = "Personaje"



&#x20;   def \_\_init\_\_(self, nombre, nivel=1, vida=100, ataque=10, oro=Decimal("0"), id=None):

&#x20;       self.id = id

&#x20;       self.nombre = nombre

&#x20;       self.nivel = nivel

&#x20;       self.vida = vida

&#x20;       self.ataque = ataque

&#x20;       self.oro = Decimal(str(oro))





\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Personaje.java, entre las marcas >>> SALA 1.1 y <<< FIN SALA 1.1.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java declara Personaje como abstract y obliga a cada subclase a definir getClase(). Python usa un atributo de clase clase = "Personaje" que cada subclase reemplaza. El oro es BigDecimal en Java y Decimal en Python, nunca double ni float.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué el id del personaje empieza en null / None y quién se lo asigna?



====================================================================================================



* ### Piso 1 - Modelo - Sala 1.2 - Mago y Arquero: herencia



Mago y Arquero heredan de Personaje y agregan su propio dato: el mana y la precisión. Por eso la tabla personaje necesitó dos columnas nuevas y en MongoDB solo algunos documentos tienen esos campos.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Mago.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Mago.java (clase completa)



public class Mago extends Personaje {



&#x20;   public static final int MANA\_POR\_DEFECTO = 100;



&#x20;   private int mana;



&#x20;   public Mago(Integer id, String nombre, int nivel, int vida, int ataque, BigDecimal oro, int mana) {

&#x20;       super(id, nombre, nivel, vida, ataque, oro);

&#x20;       this.mana = mana;

&#x20;   }



&#x20;   @Override

&#x20;   public String getClase() { return "Mago"; }



&#x20;   public int getMana() { return mana; }

}



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Mago.java, entre las marcas >>> SALA 1.2 y <<< FIN SALA 1.2.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/modelo/personaje.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/modelo/personaje.py (clase Mago)



class Mago(Personaje):

&#x20;   clase = "Mago"



&#x20;   def \_\_init\_\_(self, nombre, nivel=1, vida=100, ataque=10, oro=Decimal("0"), id=None,

&#x20;                mana=MANA\_POR\_DEFECTO):

&#x20;       super().\_\_init\_\_(nombre, nivel, vida, ataque, oro, id)

&#x20;       self.mana = mana



&#x20;   def a\_dict(self):

&#x20;       datos = super().a\_dict()

&#x20;       datos\["mana"] = self.mana

&#x20;       return datos



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/modelo/personaje.py, entre las marcas >>> SALA 1.2 y <<< FIN SALA 1.2.



\---------------------------------------------------------------------------------------------------



Java vs Python. En Java la primera línea del constructor de la subclase debe ser super(...). En Python se llama super().\_\_init\_\_(...) y luego se asigna el dato propio. Java marca el método reemplazado con @Override.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Qué pasaría si en el constructor de Mago asignas this.mana antes de llamar a super(...)?



====================================================================================================



* ### Piso 1 - Modelo - Sala 1.3 - La fábrica de personajes



Una fila de MariaDB, un documento de MongoDB y un formulario solo dicen el texto "Mago". La fábrica decide qué objeto crear con ese texto. La usan los tres repositorios y el Service, así que la decisión vive en un solo lugar.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/FabricaPersonajes.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/FabricaPersonajes.java (fragmento)



&#x20;   /\*\*

&#x20;    \* @param mana      solo para Mago; null = valor por defecto

&#x20;    \* @param precision solo para Arquero; null = valor por defecto

&#x20;    \*/

&#x20;   public static Personaje crear(String clase, Integer id, String nombre, int nivel, int vida,

&#x20;                                 int ataque, BigDecimal oro, Integer mana, Integer precision) {

&#x20;       switch (clase) {

&#x20;           case "Guerrero":

&#x20;               return new Guerrero(id, nombre, nivel, vida, ataque, oro);

&#x20;           case "Mago":

&#x20;               return new Mago(id, nombre, nivel, vida, ataque, oro,

&#x20;                       mana != null ? mana : Mago.MANA\_POR\_DEFECTO);

&#x20;           case "Arquero":

&#x20;               return new Arquero(id, nombre, nivel, vida, ataque, oro,

&#x20;                       precision != null ? precision : Arquero.PRECISION\_POR\_DEFECTO);

&#x20;           default:

&#x20;               throw new IllegalArgumentException("Clase desconocida: " + clase);

&#x20;       }

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/FabricaPersonajes.java, entre las marcas >>> SALA 1.3 y <<< FIN SALA 1.3.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/modelo/personaje.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/modelo/personaje.py (fragmento)



def crear\_personaje(clase, nombre, nivel=1, vida=100, ataque=10, oro=Decimal("0"), id=None,

&#x20;                   mana=None, precision=None):

&#x20;   """Reconstruye la subclase correcta a partir del texto de la clase.



&#x20;   La usan los tres repositorios: una fila de MariaDB, un documento de MongoDB

&#x20;   o un formulario dicen "Mago", y aquí se decide qué objeto crear.

&#x20;   """

&#x20;   if clase == "Guerrero":

&#x20;       return Guerrero(nombre, nivel, vida, ataque, oro, id)

&#x20;   if clase == "Mago":

&#x20;       return Mago(nombre, nivel, vida, ataque, oro, id,

&#x20;                   mana if mana is not None else MANA\_POR\_DEFECTO)

&#x20;   if clase == "Arquero":

&#x20;       return Arquero(nombre, nivel, vida, ataque, oro, id,

&#x20;                      precision if precision is not None else PRECISION\_POR\_DEFECTO)

&#x20;   raise ValueError(f"Clase desconocida: {clase}")



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/modelo/personaje.py, entre las marcas >>> SALA 1.3 y <<< FIN SALA 1.3.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java usa switch con case y lanza IllegalArgumentException si la clase no existe. Python encadena if y lanza ValueError. Si el mana llega vacío, los dos usan el valor por defecto.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Por qué conviene que los tres repositorios usen la misma fábrica en vez de que cada uno haga su propio if con las clases?



====================================================================================================



* ### Piso 1 - Modelo - Sala 1.4 - Item: el ítem del inventario



Item describe una línea del inventario: nombre, tipo, valor y cantidad. En Java es inmutable (sus atributos son final y no tiene setters). En Python sí se puede cambiar la cantidad.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Item.java - Java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Item.java (clase completa)



/\*\* Un ítem dentro del inventario de un personaje. \*/

public class Item {



&#x20;   private final String nombre;

&#x20;   private final String tipo;

&#x20;   private final int valor;

&#x20;   private final int cantidad;



&#x20;   public Item(String nombre, String tipo, int valor, int cantidad) {

&#x20;       this.nombre = nombre;

&#x20;       this.tipo = tipo;

&#x20;       this.valor = valor;

&#x20;       this.cantidad = cantidad;

&#x20;   }



&#x20;   public String getNombre() { return nombre; }

&#x20;   public String getTipo() { return tipo; }

&#x20;   public int getValor() { return valor; }

&#x20;   public int getCantidad() { return cantidad; }



&#x20;   @Override

&#x20;   public String toString() { return "Item(" + nombre + " x" + cantidad + ")"; }

}



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/modelo/Item.java, entre las marcas >>> SALA 1.4 y <<< FIN SALA 1.4.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/modelo/personaje.py - Python



\# Archivo: rpg\_manager\_multinivel\_flask/modelo/personaje.py (clase Item)



class Item:

&#x20;   """Un ítem dentro del inventario de un personaje."""



&#x20;   def \_\_init\_\_(self, nombre, tipo, valor, cantidad=1):

&#x20;       self.nombre = nombre

&#x20;       self.tipo = tipo

&#x20;       self.valor = valor

&#x20;       self.cantidad = cantidad



&#x20;   def a\_dict(self):

&#x20;       return {"nombre": self.nombre, "tipo": self.tipo, "valor": self.valor, "cantidad": self.cantidad}



&#x20;   def \_\_repr\_\_(self):

&#x20;       return f"Item({self.nombre!r} x{self.cantidad})"



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/modelo/personaje.py, entre las marcas >>> SALA 1.4 y <<< FIN SALA 1.4.



\---------------------------------------------------------------------------------------------------



Java vs Python. Esta diferencia cambia el repositorio en memoria: en Java, para sumar cantidad hay que reemplazar el Item por uno nuevo; en Python basta con item.cantidad += cantidad.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito

¿Qué ventaja tiene que Item sea inmutable en Java? ¿Qué obliga a hacer en el repositorio en memoria?

