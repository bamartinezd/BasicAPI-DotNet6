Construyendo Web API's RestFul con ASP.Net Core 6

API -> Interfaz de programacion de aplicaciones

N clientes pueden usar el mismo recurso para funcionar
Conjunto de interfaces definidas que permiten consumir el software.

Permite que dos apps distintas puedan consumirse entre si

Permite abstracciones... usar un sf sin necesidad de conocer como funciona internamente.

REST Transferencia de estado representacional

Estandar de construir servicios WEB con un conjunto de reglas establecidas(RestFul), no es mandatorio seguirlo al pié de la letra

Usar Metodos HTTP para ejecutar funciones/Verbos en un API
HTTPCrud

6 Reglas para que un API sea RestFul

Arquitectura cliente servidor -> garantiza separacion de responsabilidades.

Interfaz uniforme -> forma estandarizada para transferir info.
	*Identificacion del recurso
	*Manipulacion de recursos usando representaciones
	*Mensajes Autodescriptivos(MediaTypes)
	*Hipermedia como motor del estado de la aplicacion
	
Protocolos sin estado -> Cada request debe tener la info necesaria para ser repuesta

Caché -> la respuesta web debe indicar cuando se deben guardar en caché

Sistema de capas -> Arq a capas totalmente transparente para el cliente

Codigo en demanda -> Opcion de enviar codigo JS





Metodos HTTP-Verbos HTTP (CRUD) -> Web API Nivel 2 segun modelo de richardson

Mecanismo para expresar la accion que se quiere ejercer sobre un recurso
GET -> Pedir datos al servidor,
head -> retorna la cabecera info del mensaje y NO el mensaje como tal,
POST-> Indicar que queremos enviar info al server en el cuerpo,
PUT-> Casi igual que POST pero actualiza, 
DELETE -> Expresar que queremos borrar el recurso.

PATCH -> Actualizaciones parciales a un recurso

El dev define que va a ocurrir dentro de cada accion


Anatomia de una peticion HTTP

Mensaje de cliente a servidor usando el protocolo HTTP
Se contesta una respuesta HTTP

*Linea de peticion
	GET /api/autores HTTP/1.1

*Conjunto de campos de cabecera
	Metadatos que se envian con info ACERCA de la peticion
	Ej: Host: en.wikipedia.org
	Ej: Cache-control: no-cache

*cuerpo (Opcional)
	Info adicional a enviar al server... lo que sea... contenido percé que se quiere 	transmitir (POST, PUT)

Respuesta HTTP Estructura similar:
Linea de estatus(20X Ok, 40x BadRequest,...)
	1XX -> Respuesta informativa
	2XX -> Respuesta exitosa (200 Ok, 201 Created, 202 Accepted, 204 No Content)
	3XX -> Redireccion (301 Moved Permanently, 302 Found)
	4XX -> Error Cliente (400 BadRequest, 401 Unauthorized, 403 Forbidden, 404 Not 				Found, 405 Method not allowed)
	5XX-> (500 InternalServerError, 503 Service unavailable)
cabecera: conjunto de cabeceras
cuerpo: La info que el server quiere transmitir.


_____________________________________________________

Controladores y acciones
Llamada a EndPoint -> Desencadena acción
Un controlador es una clase que agrupa a un conjunto de acciones
comunmente acciones relacionadas a UN recurso.

Nomenclatura: Nombre+Controller Ej: AuthorController

Definimos las acciones basicas que podemos definir sobre un recurso.

Atributos
	[HttpGet]
	[HttpPost]
	[HttpPut]
	[HttpDelete]

Necesitamos definir una URL diferente para cada acción
 
Reglas de Ruteo

Permiten mapear una URL con una acción
-Ruteo por atributo: En los controladores
	[Route("api/authors")]
	combinacion/Ignorar
	Dos EndPoints pueden apuntar a una misma accion
		[HttpGet]
		[HttpGet("othername")]

	Un endpoint puede tener mas de un parametro puede ser opcional
	y con valor por defecto
		[HttpGet({id}/{otherparam=gavilan})]

-Ruteo Convencional: En la clase Startup
	
Tipos de datos que puede retornar una accion
	*Tipo especifico: string, int, T...
		no permite devolver un codigo de respuesta
	*ActionResult<T>: Resultado de accion - Buena practica
	*IActionResult: Solo permite retornar cualquier tipo de ActionResult

Programacion asincrona

Accion puede ser sincrono/asincrono
-Ejecucion de otras tareas mientras ejecuta la actual
-Costo de rendimiento
-Ideales para operaciones con DB y otros WS

El metodo o la accion de debe decorar con "async".
y el tipo de dato de retorno debe ser Task<T>
Si una funcion es asincrona el sufijo de la funcion debe terminar en "Async"
Al usar el operador "await" para esperar y mientras se ejecuta se destinan esos recursos para otras tareas.

Introduccion al Model Binding
Permite mapear datos Http a parametros de una accion
	Fuentes de parametros
		Valores de ruta: El parametro/s que vengan en la ruta ej: api/autor/4
		QueryStrings: pares de nombre valor ej: ?id=5&apellido=gavilan
		Valores de formulario:[FromBody] 
			BindRequired
			BindNever
			FromHeader
			FromQuery
			FromRoute
			FromForm: content type: application/urlformencoded
			FromServices...

Validando modelos
Podemos usar atributos para hacer validaciones.
A nivel de parametro o a nivel de modelo
[Required]
[StringLength]
[Range]
[CreditCard]
[Compare]
[Phone]
[RegularExpression]
[Url]

El [ApiController] del controlador permite validar el modelo/entidad reemplazando el modelState, y a nivel de EndPoint/Accion/Metodo TryValidateModel(Autor).

Reglas de validaciones personalizadas
*Por atributo
	puede ser reutilizada entre modelos y propiedades

*Por modelo
	Accede a todas las propiedades del modelo pero no se puede reutilizar



---------------
Inyeccion de dependencias
Es un patron en el cual una clase recibe las instancias de objetos que necesita de una fuente externa en vez de crearlas el mismo.

Ej:
La clase A usa la clase B y por ende DEPENDE de ella

..... Adelantar del cuaderno de GML

Servicios en clase Startup

Servicio es una clase que puede ser intectada a otras clases por el framework.
Se configuran en el metodo configureServices() de la clase StarTup

public void ConfigureServices(IServiceCollection services)
        {

            //Ejemplo de configurar servicios
            services.AddTransient<IClaseB, ClaseB2>();

La importancia de usar el contenedor de inversion de controles para centralizar la resolucion de dependencias en un solo lugar dentro de la aplicacion.

Tiempo de vida:
Transient: Cada que se solicite un servicio se sirve una nueva instancia de la clase
	services.AddTransient<IClaseB, ClaseB2>();

Scoped: 1 por peticion HTTP
	services.AddScoped<IClaseB, ClaseB2>();

Singleton: Siempre se da la misma instancia en la misma ejecucion
	services.AddSingleton<IClaseB, ClaseB2>();

Ej: cuando inyectamos ApplicationDBContext en el constructor del controlador
	se configuró con addDbContext() en el metodo configureServices() de la clase StarTup


---- LOGGING

Escribir y visualizar los mensajes que deja la aplicacion
ILoggerService: centraliza las operaciones relacionadas con logs
	Puede tener X proveedores que van a registrar y/o hacer algo con esos mensajes.
Niveles:
	*Trace -> mas bajo
	*Debug
	*Information
	*Warning
	*Error
	*Critical -> Mas alto

Dependiendo del que se configure en el appsettings logueará desde el especificado hacia arriba en niveles
En el appsettings se puede crear una nueva categoria especificando el namespace o la clase

----MIDDLEWARES

Una peticion llega al webapi y pasa por una tuberia de peticiones http
tuberia de peticiones: conjunto de procesos que la salida de cada proceso es la entrada del otro.

A cada proceso se le llama MIDDLEWARE:
	Ej: 
	MVC es un proceso (Middleware)
	Authorization proceso(Middleware)

El orden de los procesos (Middleware) de la tuberia es importante
puesto que para dar permisis al usuario de MVC para hacer alguna accion ya 
debió haber pasado el proceso(Middleware) de Autorizacion

Esta se configura en el metodo configure de la clase StartUp()
IApplicationBuilder: Base para ejecutar los metodos (tuberia)
IHostingEnvironment: Sabemos sobre que ambiente estamos, y usar distintos procesos segun ambiente.

Ejemplo:

app.UseAuthentication();
app.UseMvc();

Al contrario no tendria sentido por que MVC requiere poder usar Authentication
y por ende esta debio ser activada en un Middleware Anterior.


---FILTROS

Nos ayudan a correr codigo en determinados momentos del ciclo de vida del procesamiento de una peticion HTTP
Son utiles cuando se ve la necesidad de ejecutar una logica en VARIAS acciones de VARIOS controladores sin tener que repetir codigo.

Ej:
*De autorizacion
*Filtros de recursos
*Filtros de accion
*De Exception
*De resultado

Se pueden usar a nivel de 
-Accion
-Controlador
-Todo en WebApi

Fltros personalizados
*Podemos crear nuestros propios filtros
*IActionFilter, IAsyncActionFilter

Para que un filtro sea global debe ser usado en el configureServices() en las opciones de addMVC

EJECUTAR CODIGO RECURRENTE----

IHostedServices: permite tener una tarea que se va a ejecutar al levantar y al detener la ejecucion de la aplicacion.



