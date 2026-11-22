* # Sesión 6 (22/11/2026):



* ## Código de guardado:

&#x20;

EP2.eyJ2IjoyLCJ3IjoiU2VyZ2lvIFBhc3RyYW5hIDIwMjUyNTc4MDg2IiwiZyI6IjMwMiIsInMiOiIyMDI2LTExLTE2VDAyOjQyOjE2LjI2OFoiLCJlIjoiIiwicCI6IjMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzAifQ.W96HKo9nIYZu20zQ



====================================================================================================



* ### Azotea - Recorrido completo - Sala A.1 - Recorrido completo: Kaelen



En la azotea se ve todo junto. El recorrido del Reto 6 crea a Kaelen, le cambia la vida y lo elimina pasando por todas las capas: el Service valida, el repositorio guarda en el motor que diga la configuración y el resultado vuelve con su id.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/RecorridoPersistencia.java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/RecorridoPersistencia.java (fragmento)



&#x20;   public static void main(String\[] args) {

&#x20;       Properties cfg = Main.cargarConfiguracion();

&#x20;       if (args.length > 0) {

&#x20;           cfg.setProperty("repositorio", args\[0]);

&#x20;       }

&#x20;       String motor = cfg.getProperty("repositorio");

&#x20;       PersonajeService service = new PersonajeService(RepositoryFactory.crear(cfg));

&#x20;       System.out.println("=== RPG Manager · recorrido de persistencia · Java · motor: " + motor + " ===");



&#x20;       imprimirLista("1. Todos los personajes", service.listar());

&#x20;       imprimirLista("2. Solo los Magos", service.listarPorClase("Mago"));



&#x20;       List<Personaje> todos = service.listar();

&#x20;       if (!todos.isEmpty()) {

&#x20;           Personaje primero = todos.get(0);

&#x20;           System.out.println("\\n3. Inventario de " + primero.getNombre());

&#x20;           List<Item> inventario = service.inventario(primero.getId());

&#x20;           for (Item item : inventario) {

&#x20;               System.out.printf("  %-18s %-9s valor %4d  x%d%n",

&#x20;                       item.getNombre(), item.getTipo(), item.getValor(), item.getCantidad());

&#x20;           }

&#x20;           if (inventario.isEmpty()) {

&#x20;               System.out.println("  (vacío)");

&#x20;           }

&#x20;       }



&#x20;       Personaje nuevo;

&#x20;       try {

&#x20;           nuevo = service.crear(NOMBRE\_PRUEBA, "Arquero", 3, 90, 18, new BigDecimal("12.50"));

&#x20;       } catch (ValidacionException e) {

&#x20;           System.out.println("\\n4. No se pudo crear: " + e.getMessage());

&#x20;           return;

&#x20;       }

&#x20;       System.out.println("\\n4. Creado: " + nuevo.getNombre() + " con id " + nuevo.getId());



&#x20;       service.actualizarVida(nuevo.getId(), 55);

&#x20;       System.out.println("5. Vida de " + nuevo.getNombre() + " actualizada a "

&#x20;               + service.obtener(nuevo.getId()).getVida());



&#x20;       service.eliminar(nuevo.getId());

&#x20;       System.out.println("6. " + NOMBRE\_PRUEBA + " eliminado");



&#x20;       imprimirLista("7. Todos los personajes otra vez", service.listar());



&#x20;       System.out.println("\\n8. Validaciones del Service");

&#x20;       String\[]\[] intentos = { {"", "Mago", "100"}, {"Zed", "Paladin", "100"}, {"Zed", "Mago", "-5"} };

&#x20;       for (String\[] intento : intentos) {

&#x20;           try {

&#x20;               service.crear(intento\[0], intento\[1], 1, Integer.parseInt(intento\[2]), 10, BigDecimal.ZERO);

&#x20;           } catch (ValidacionException e) {

&#x20;               System.out.println("  rechazado: " + e.getMessage());

&#x20;           }

&#x20;       }

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/RecorridoPersistencia.java, entre las marcas >>> SALA A.1 y <<< FIN SALA A.1.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/recorrido.py



\# Archivo: rpg\_manager\_multinivel\_flask/recorrido.py (fragmento)



def main():

&#x20;   cfg = leer\_configuracion()

&#x20;   if len(sys.argv) > 1:

&#x20;       cfg.set("app", "repositorio", sys.argv\[1])

&#x20;   motor = cfg.get("app", "repositorio")

&#x20;   service = PersonajeService(crear\_repositorio(cfg))

&#x20;   print(f"=== RPG Manager · recorrido de persistencia · Python · motor: {motor} ===")



&#x20;   imprimir\_lista("1. Todos los personajes", service.listar())

&#x20;   imprimir\_lista("2. Solo los Magos", service.listar\_por\_clase("Mago"))



&#x20;   todos = service.listar()

&#x20;   if todos:

&#x20;       primero = todos\[0]

&#x20;       print(f"\\n3. Inventario de {primero.nombre}")

&#x20;       inventario = service.inventario(primero.id)

&#x20;       for item in inventario:

&#x20;           print(f"  {item.nombre:<18} {item.tipo:<9} valor {item.valor:>4}  x{item.cantidad}")

&#x20;       if not inventario:

&#x20;           print("  (vacío)")



&#x20;   try:

&#x20;       nuevo = service.crear(NOMBRE\_PRUEBA, "Arquero", nivel=3, vida=90, ataque=18, oro="12.50")

&#x20;   except ValidacionException as e:

&#x20;       print(f"\\n4. No se pudo crear: {e}")

&#x20;       return

&#x20;   print(f"\\n4. Creado: {nuevo.nombre} con id {nuevo.id}")



&#x20;   actualizado = service.actualizar\_vida(nuevo.id, 55)

&#x20;   print(f"5. Vida de {actualizado.nombre} actualizada a {service.obtener(nuevo.id).vida}")



&#x20;   service.eliminar(nuevo.id)

&#x20;   print(f"6. {NOMBRE\_PRUEBA} eliminado")



&#x20;   imprimir\_lista("7. Todos los personajes otra vez", service.listar())



&#x20;   print("\\n8. Validaciones del Service")

&#x20;   for intento in ({"nombre": "", "clase": "Mago"},

&#x20;                   {"nombre": "Zed", "clase": "Paladin"},

&#x20;                   {"nombre": "Zed", "clase": "Mago", "vida": -5}):

&#x20;       try:

&#x20;           service.crear(\*\*intento)

&#x20;       except ValidacionException as e:

&#x20;           print(f"  rechazado: {e}")



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/recorrido.py, entre las marcas >>> SALA A.1 y <<< FIN SALA A.1.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java lo corre con RecorridoPersistencia.java y Python con recorrido.py. Los dos leen la misma configuración que la app web, así que prueban el mismo motor.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito



Corre el recorrido con memoria, con mariadb y con mongo. ¿Qué cambia en la salida y qué no cambia en el código?



====================================================================================================



