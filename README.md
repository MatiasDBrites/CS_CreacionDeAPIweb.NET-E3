# CS_CreacionDeAPIweb.NET-E3
Ejercicio: Adición de un controlador

## **Creación de un controlador**

1. Seleccione la carpeta *Controladores* en Visual Studio Code y agregue un nuevo archivo denominado *PizzaController.cs*.

Se crea un archivo de clase vacío de nombre *ProductsController.cs*
 en el directorio *Controllers*
. El nombre del directorio *Controllers*
 es una convención. El nombre del directorio procede de la arquitectura Modelo-Vista-*Controlador*
 que usa la API web.

Agregue el código siguiente a *Controllers/PizzaController.cs*
. Guarde los cambios.

```csharp
using ContosoPizza.Models;
using ContosoPizza.Services;
using Microsoft.AspNetCore.Mvc;

namespace ContosoPizza.Controllers;

[ApiController]
[Route("[controller]")]
public class PizzaController : ControllerBase
{
    public PizzaController()
    {
    }

    // GET all action

    // GET by Id action

    // POST action

    // PUT action

    // DELETE action
}
```

****Obtención de todas las pizzas****

El primer verbo REST que hay que implementar es `GET`, con el que un cliente puede obtener todas las pizzas de la API. Se puede usar el atributo integrado `[HttpGet]` para definir un método que devuelva las pizzas del servicio.

Reemplace el comentario `// GET all action` de *Controllers/ProductsController.cs* por el siguiente código:

```csharp
[HttpGet]
public ActionResult<List<Pizza>> GetAll() =>
    PizzaService.GetAll();
```

La acción anterior:

- Responde solo al verbo HTTP `[HttpGet]`, tal y como indica el atributo `GET`.
- Consulta el servicio en busca de todas las pizzas y devuelve automáticamente los datos cuyo `Content-Type` es `application/json`.

## **Recuperación de una única pizza**

Es posible que el cliente también quiera solicitar información sobre una pizza específica en lugar de toda la lista. Se puede implementar otra acción`GET` que requiera un parámetro `id`. Se puede usar el atributo integrado `[HttpGet("{id}")]` para definir un método que devuelva las pizzas del servicio. La lógica de enrutamiento registra `[HttpGet]` (sin `id`) y `[HttpGet("{id}")]` (con `id`) como dos rutas diferentes. A continuación, puede escribir una acción independiente para recuperar un solo elemento.

Reemplace el comentario `// GET by Id action` de *Controllers/ProductsController.cs* por el siguiente código:

```csharp
[HttpGet("{id}")]
public ActionResult<Pizza> Get(int id)
{
    var pizza = PizzaService.Get(id);

    if(pizza == null)
        return NotFound();

    return pizza;
}
```

La acción anterior:

- Responde solo al verbo HTTP `[HttpGet]`, tal y como indica el atributo `GET`.
- Requiere que se incluya el valor del parámetro `id` en el segmento de URL después de `pizza/`. Recuerde que el atributo `/pizza` de nivel de controlador ha definido el patrón `[Route]`.
- Consulta la base de datos en busca de una pizza que coincida con el parámetro `id` proporcionado.

Cada instancia `ActionResult` usada en la acción anterior se asigna al código de estado HTTP correspondiente en la tabla siguiente.

| Resultado de la acciónde ASP.NET Core | Código de estado HTTP | Descripción |
| --- | --- | --- |
| Ok está implícito | 200 | Hay un producto que coincide con el parámetro id proporcionado en la caché en memoria.El producto se incluye en el cuerpo de la respuesta en el tipo multimedia que se define en el encabezado de solicitud HTTP accept (JSON de forma predeterminada). |
| NotFound | 404 | No hay ningún producto que coincida con el parámetro id proporcionado en la caché en memoria. |

## **Compilación y prueba del controlador**

1. Ejecute el siguiente comando para compilar e iniciar la API web:

```csharp
dotnet run
```

1. Abra el terminal `httprepl` existente o uno nuevo integrado desde Visual Studio Code seleccionando **Terminal**>**Nuevo terminal** en el menú principal.
2. Conéctese a la API web mediante el comando siguiente:

```csharp
httprepl https://localhost:{PORT}
```

Como alternativa, ejecute el siguiente comando en cualquier momento mientras `HttpRepl`
 se ejecuta:

```csharp
(Disconnected)> connect https://localhost:{PORT}
```

Para ver el punto de conexión de `Pizza`
 ya disponible, ejecute el siguiente comando:

```csharp
ls
```

El comando anterior detecta todas las API disponibles en el punto de conexión conectado. Debería mostrar el código siguiente:

CLI de .NET

```
 https://localhost:{PORT}/> ls
 .                 []
 Pizza             [GET]
 WeatherForecast   [GET]
```

Ejecute el comando siguiente para ir al punto de conexión `Pizza`
:

```csharp
cd Pizza
```

El comando anterior muestra una salida de las API disponibles para el punto de conexión `Pizza`
:

CLI de .NET

```
https://localhost:{PORT}/> cd Pizza
/Pizza    [GET]
```

Realice una solicitud `GET`
 en `HttpRepl`
 usando el comando siguiente:

```bash
get
```

El comando anterior devuelve una lista de todas las pizzas de `json`
:

CLI de .NETCopiar

```
  HTTP/1.1 200 OK
  Content-Type: application/json; charset=utf-8
  Date: Fri, 02 Apr 2021 21:55:53 GMT
  Server: Kestrel
  Transfer-Encoding: chunked

  [
      {
          "id": 1,
          "name": "Classic Italian",
          "isGlutenFree": false
      },
      {
          "id": 2,
          "name": "Veggie",
          "isGlutenFree": true
      }
  ]
```

Para consultar en busca de una sola pizza, se puede realizar otra solicitud `GET`
, pero pase un parámetro `id`
 usando el comando siguiente:

```bash
get 1
```

El comando anterior devuelve `Classic Italian`
 con la salida siguiente.

CLI de .NET

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Fri, 02 Apr 2021 21:57:57 GMT
Server: Kestrel
Transfer-Encoding: chunked

{
    "id": 1,
    "name": "Classic Italian",
    "isGlutenFree": false
}
```

La API también controla situaciones en las que el elemento no existe. Llame a la API de nuevo, pero pase un parámetro `id`
 de pizza no válido con el siguiente comando:

CLI de .NET

```
get 5
```

El comando anterior devuelve un error `404 Not Found`
 con la siguiente salida:

CLI de .NETCopiar

```
HTTP/1.1 404 Not Found
Content-Type: application/problem+json; charset=utf-8
Date: Fri, 02 Apr 2021 22:03:06 GMT
Server: Kestrel
Transfer-Encoding: chunked

{
    "type": "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    "title": "Not Found",
    "status": 404,
    "traceId": "00-ec263e401ec554b6a2f3e216a1d1fac5-4b40b8023d56762c-00"
}
```

1. Vuelva al terminal `dotnet` en la lista desplegable de Visual Studio Code y apague la API web presionando CTRL+C en el teclado.

Ya ha terminado de implementar los verbos `GET`. En la unidad siguiente, puede agregar más acciones a `PizzaController` para admitir operaciones CRUD en datos de pizza.
