# Sesión 5 (22/11/2026):



* ## Código de guardado:

&#x20;

EP2.eyJ2IjoyLCJ3IjoiU2VyZ2lvIFBhc3RyYW5hIDIwMjUyNTc4MDg2IiwiZyI6IjMwMiIsInMiOiIyMDI2LTExLTE2VDAyOjQyOjE2LjI2OFoiLCJlIjoiIiwicCI6IjMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzAifQ.W96HKo9nIYZu20zQ



====================================================================================================



* ### Piso 4 - Control y Vista - Sala 4.1 - El arranque arma la cadena



El arranque es el único lugar donde se conectan las capas, siempre en el mismo orden: configuración, fábrica, Service, controlador. Después de eso cada capa solo habla con la de abajo.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/Main.java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/Main.java (fragmento)



&#x20;   public static void main(String\[] args) {

&#x20;       Properties cfg = cargarConfiguracion();

&#x20;       PersonajeRepository repositorio = RepositoryFactory.crear(cfg);     // aquí se elige el motor

&#x20;       PersonajeService service = new PersonajeService(repositorio);       // inyección por constructor

&#x20;       PersonajeControlador controlador = new PersonajeControlador(service, cfg.getProperty("repositorio"));



&#x20;       Javalin app = Javalin.create();

&#x20;       controlador.registrarRutas(app);

&#x20;       app.start(7070);

&#x20;       System.out.println("RPG Manager en http://localhost:7070/personajes · repositorio: "

&#x20;               + cfg.getProperty("repositorio"));

&#x20;   }



\---------------------------------------------------------------------------------------------------



Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/Main.java, entre las marcas >>> SALA 4.1 y <<< FIN SALA 4.1.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/app.py



\# Archivo: rpg\_manager\_multinivel\_flask/app.py (fragmento)



def crear\_app(cfg=None, repositorio=None):

&#x20;   cfg = cfg or leer\_configuracion()

&#x20;   repositorio = repositorio or crear\_repositorio(cfg)   # aquí se elige el motor

&#x20;   service = PersonajeService(repositorio)                # inyección por constructor



&#x20;   app = Flask(\_\_name\_\_)

&#x20;   app.json.ensure\_ascii = False

&#x20;   app.register\_blueprint(crear\_controlador(service, cfg.get("app", "repositorio")))

&#x20;   return app



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto

Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/app.py, entre las marcas >>> SALA 4.1 y <<< FIN SALA 4.1.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java arranca en Main.main y levanta Javalin en el puerto 7070. Python arma la app en crear\_app y Flask corre en el puerto 5000.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito



Si el puerto 7070 ya está ocupado, la app no arranca. ¿Qué capa falla y por qué no tiene nada que ver con la base de datos?



====================================================================================================



* ### Piso 4 - Control y Vista - Sala 4.2 - La ruta POST crear



El controlador recibe el formulario, llama al Service y elige qué mostrar. No tiene SQL ni reglas del juego: si el Service lanza ValidacionException, vuelve a mostrar el formulario con el error; si todo sale bien, redirige a la lista.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/controlador/PersonajeControlador.java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/controlador/PersonajeControlador.java (fragmento)



&#x20;   private void crear(Context ctx) {

&#x20;       Map<String, String> datos = new HashMap<>();

&#x20;       for (String campo : List.of("nombre", "clase", "nivel", "vida", "ataque", "oro")) {

&#x20;           String valor = ctx.formParam(campo);

&#x20;           if (valor != null) {

&#x20;               datos.put(campo, valor);

&#x20;           }

&#x20;       }

&#x20;       try {

&#x20;           service.crear(datos.get("nombre"), datos.get("clase"),

&#x20;                   entero(datos.get("nivel"), "nivel", 1), entero(datos.get("vida"), "vida", 100),

&#x20;                   entero(datos.get("ataque"), "ataque", 10), dinero(datos.get("oro")));

&#x20;       } catch (ValidacionException e) {

&#x20;           ctx.status(400).html(PersonajeVista.formulario(datos, e.getMessage(), motor));

&#x20;           return;

&#x20;       }

&#x20;       ctx.redirect("/personajes");

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/controlador/PersonajeControlador.java, entre las marcas >>> SALA 4.2 y <<< FIN SALA 4.2.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/controlador/personaje\_controlador.py



\# Archivo: rpg\_manager\_multinivel\_flask/controlador/personaje\_controlador.py (fragmento)



&#x20;   @bp.post("/personajes")

&#x20;   def crear():

&#x20;       datos = request.form

&#x20;       try:

&#x20;           service.crear(datos.get("nombre"), datos.get("clase"), datos.get("nivel", 1),

&#x20;                         datos.get("vida", 100), datos.get("ataque", 10), datos.get("oro", 0))

&#x20;       except ValidacionException as e:

&#x20;           return render\_template("nuevo.html", datos=datos, error=str(e)), 400

&#x20;       return redirect(url\_for("personajes.listar"))



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/controlador/personaje\_controlador.py, entre las marcas >>> SALA 4.2 y <<< FIN SALA 4.2.



\---------------------------------------------------------------------------------------------------



Java vs Python. Javalin registra la ruta con app.post("/personajes", this::crear) y lee ctx.formParam. Flask usa el decorador @bp.post("/personajes") y lee request.form.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito



¿Por qué después de crear se hace un redirect a la lista en vez de mostrar la lista directamente?



====================================================================================================



* ### Piso 4 - Control y Vista - Sala 4.3 - Las rutas del inventario



Agregar un ítem es una ruta POST que recibe el nombre del ítem y la cantidad. Si el Service rechaza algo, el controlador vuelve al detalle con el error en la URL; la vista lo muestra en la alerta roja.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/controlador/PersonajeControlador.java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/controlador/PersonajeControlador.java (fragmento)



&#x20;   private void agregarItem(Context ctx) {

&#x20;       int id = idDe(ctx);

&#x20;       try {

&#x20;           service.agregarItem(id, ctx.formParam("item"), entero(ctx.formParam("cantidad"), "cantidad", 1));

&#x20;       } catch (ValidacionException e) {

&#x20;           ctx.redirect("/personajes/" + id + "?error=" + codificar(e.getMessage()));

&#x20;           return;

&#x20;       }

&#x20;       ctx.redirect("/personajes/" + id);

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/controlador/PersonajeControlador.java, entre las marcas >>> SALA 4.3 y <<< FIN SALA 4.3.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/controlador/personaje\_controlador.py



\# Archivo: rpg\_manager\_multinivel\_flask/controlador/personaje\_controlador.py (fragmento)



&#x20;   @bp.post("/personajes/<int:id>/inventario")

&#x20;   def agregar\_item(id):

&#x20;       try:

&#x20;           service.agregar\_item(id, request.form.get("item"), request.form.get("cantidad", 1))

&#x20;       except PersonajeNoEncontradoException as e:

&#x20;           return render\_template("error.html", mensaje=str(e)), 404

&#x20;       except ValidacionException as e:

&#x20;           return redirect(url\_for("personajes.detalle", id=id, error=str(e)))

&#x20;       return redirect(url\_for("personajes.detalle", id=id))



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/controlador/personaje\_controlador.py, entre las marcas >>> SALA 4.3 y <<< FIN SALA 4.3.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java convierte la cantidad con su método entero antes de llamar al Service. Python le pasa el texto y el Service lo convierte.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito



¿Por qué el error viaja en la URL (?error=...) en vez de guardarse en una variable del controlador?



====================================================================================================



* ### Piso 4 - Control y Vista - Sala 4.4 - La vista con Bootstrap



La vista arma el HTML con Bootstrap y no decide nada: muestra lo que le pasa el controlador. Todo texto que viene del usuario se escapa, para que un nombre con <script> se vea como texto y no se ejecute.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/vista/PersonajeVista.java



// Archivo: rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/vista/PersonajeVista.java (fragmento)



&#x20;   private static String escapar(Object valor) {

&#x20;       if (valor == null) {

&#x20;           return "";

&#x20;       }

&#x20;       // El \& va primero: los demás reemplazos crean textos que empiezan por \&

&#x20;       return valor.toString()

&#x20;               .replace("\&", "\&amp;")

&#x20;               .replace("<", "\&lt;")

&#x20;               .replace(">", "\&gt;")

&#x20;               .replace("\\"", "\&quot;")

&#x20;               .replace("'", "\&#39;");

&#x20;   }



\---------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_javalin/src/main/java/gestionrpg/vista/PersonajeVista.java, entre las marcas >>> SALA 4.4 y <<< FIN SALA 4.4.



\---------------------------------------------------------------------------------------------------



##### rpg\_manager\_multinivel\_flask/templates/detalle.html



{# Archivo: rpg\_manager\_multinivel\_flask/templates/detalle.html (tabla del inventario) #}



{% for item in inventario %}

&#x20; <tr>

&#x20;   <td>{{ item.nombre }}</td><td>{{ item.tipo }}</td>

&#x20;   <td class="text-end">{{ item.valor }}</td><td class="text-end">{{ item.cantidad }}</td>

&#x20;   <td class="text-end">

&#x20;     <form method="post" action="{{ url\_for('personajes.quitar\_item', id=p.id) }}">

&#x20;       <input type="hidden" name="item" value="{{ item.nombre }}">

&#x20;       <button class="btn btn-sm btn-outline-danger" type="submit">Quitar</button>

&#x20;     </form>

&#x20;   </td>

&#x20; </tr>

{% else %}

&#x20; <tr><td colspan="5" class="text-center text-muted py-3">Inventario vacío.</td></tr>

{% endfor %}



\----------------------------------------------------------------------------------------------------



###### Llévalo a tu proyecto



Pulsa Copiar bloque y pégalo en rpg\_manager\_multinivel\_flask/templates/detalle.html, entre las marcas >>> SALA 4.4 y <<< FIN SALA 4.4.



\---------------------------------------------------------------------------------------------------



Java vs Python. Java arma el HTML con StringBuilder y escapa a mano con escapar(). Flask usa plantillas Jinja2, que escapan solas cada {{ }}; los ciclos se escriben con {% for %}.



\---------------------------------------------------------------------------------------------------



###### Para tu trabajo escrito



En escapar(), ¿por qué el \& tiene que reemplazarse primero?

