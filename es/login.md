---
"$title": Iniciar sesión
"$order": '1'
description: La primera vez que ingresa a la página, puede ver 2 comentarios y un botón de inicio de sesión. Si busca el botón de inicio de sesión en el código, encontrará ...
---

La primera vez que ingresa a la [página](../../../../documentation/examples/previews/Comment_Section.html) , puede ver 2 comentarios y un botón de inicio de sesión.

<amp-img src="/static/img/login-button.jpg" alt="Login button" height="290" width="300"></amp-img>

Si busca el botón de inicio de sesión en el código, encontrará:

[sourcecode:html]
<span amp-access="NOT loggedIn" role="button" tabindex="0" amp-access-hide>
  <h5>Please login to comment</h5>
  <button on="tap:amp-access.login-sign-in" class="button-primary comment-button">Login</button>
</span>
[/sourcecode]

El comportamiento de los atributos relacionados con [`amp-access`](../../../../documentation/components/reference/amp-access.md) depende de una configuración de toda la página para [`amp-access`](../../../../documentation/components/reference/amp-access.md) , en nuestro caso, esta:

[sourcecode:html]
<script id="amp-access" type="application/json">
  {
    "authorization": "https://ampbyexample.com/samples_templates/comment_section/authorization?rid=READER_ID&url=CANONICAL_URL&ref=DOCUMENT_REFERRER&_=RANDOM",
    "noPingback": "true",
    "login": {
      "sign-in": "https://ampbyexample.com/samples_templates/comment_section/login?rid=READER_ID&url=CANONICAL_URL",
      "sign-out": "https://ampbyexample.com/samples_templates/comment_section/logout"
    },
    "authorizationFallbackResponse": {
      "error": true,
      "loggedIn": false
    }
  }
</script>
[/sourcecode]

The authorization endpoint is deployed as part of AMPByExample. It's the responsibility of the publisher of the page to provide this endpoint. In this sample case, for simplicity, we implemented basic logic so that when this request is received, the server reads the value of a cookie named `ABE_LOGGED_IN`. If the cookie is not there, we return a JSON response containing `loggedIn = false`. As a result, the first time a user lands on the page, this request will return `loggedIn = false` and the login button will be shown.

Mirando nuevamente el código HTML del botón, usando `on="tap:amp-access.login-sign-in"` , especificamos que una vez que el usuario presiona el botón, se debe usar la URL especificada en el JSON anterior:

[sourcecode:json]
{
	"login": {
    "sign-in": "https://ampbyexample.com/samples_templates/comment_section/login?rid=READER_ID&url=CANONICAL_URL"
  }
}

[/sourcecode]

[tip type="note"] **NOTA:** observe que es posible definir diferentes URL dentro del nodo de inicio de sesión, en este caso estamos definiendo el `sign-in` y luego definiremos el `sign-out` . [/tip]

La página de inicio de sesión es una página que no es AMP en la que completamos los valores de inicio de sesión y contraseña para simplificar. Observe el uso del tipo de entrada oculta `returnURL` , que el servidor AMPByExample rellena mediante plantillas del lado del servidor. El servidor lee este valor de un parámetro llamado `return` , que la biblioteca AMP agrega automáticamente a la URL de inicio de sesión.

En el siguiente ejemplo, el valor del parámetro de `return` se agrega a la solicitud una vez que hace clic en el botón de inicio de sesión. Puede explorar este valor utilizando la consola de Chrome DevTools y navegando a la pestaña Red.

<amp-img src="/static/img/return-parameter.jpg" alt="Return parameter" height="150" width="600"></amp-img>

Una vez que el servidor AMPByExample recibe la solicitud POST de la página de inicio de sesión y el inicio de sesión y la contraseña son correctos, redirige la solicitud a la `returnURL` que mencionamos anteriormente y agrega el parámetro `#success=true` . El tiempo de ejecución de AMP ahora puede autorizar la página y finalmente permitirle agregar un comentario.

Es importante comprender qué hace el tiempo de ejecución de AMP y qué debería hacer el servidor, ya que la implementación del servidor es responsabilidad del editor de la página.

Como resumen rápido:

- El tiempo de ejecución de AMP agrega automáticamente el parámetro de retorno a la solicitud de inicio de sesión especificada dentro del objeto JSON de inicio de sesión.
- El tiempo de ejecución de AMP cierra la página de inicio de sesión y redirige a la página especificada por el parámetro de URL de retorno.
- El servidor debe orquestar la respuesta una vez que el usuario haga clic en el botón de inicio de sesión.

[tip type="tip"] **SUGERENCIA:** también se puede encontrar una explicación más detallada sobre este flujo en [`amp-access`](../../../../documentation/components/reference/amp-access.md) . [/tip]
