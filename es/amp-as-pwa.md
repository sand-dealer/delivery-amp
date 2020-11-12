---
"$title": Fácil acceso sin conexión y rendimiento mejorado
"$order": '11'
description: Un Service Worker es un proxy del lado del cliente que se encuentra entre su página y su servidor, y se utiliza para crear fantásticas experiencias fuera de línea, carga rápida ...
formats:
- sitios web
author: CrystalOnScript
contributors:
- pbakaus
---

[Los trabajadores del servicio](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) permiten experiencias enriquecidas fuera de línea y experiencias de usuario consistentes en diferentes fortalezas de red. Al almacenar en caché los recursos dentro del navegador, una aplicación web puede proporcionar datos, activos y páginas fuera de línea al usuario para mantenerlo comprometido e informado.

Recuerde: el Service Worker no podrá interactuar con la versión almacenada en caché de AMP de su página. Úselo para continuar los viajes a su origen.

## Instalar un trabajador de servicio

Un Service Worker es un proxy del lado del cliente que se encuentra entre su página y su servidor, y se utiliza para crear fantásticas experiencias sin conexión, escenarios de shell de aplicaciones de carga rápida y enviar notificaciones automáticas.

[tip type="note"] **NOTA:** si el concepto de Service Workers es nuevo para usted, lea la [introducción en WebFundamentals](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers) . [/tip]

Su Service Worker debe estar registrado en una página determinada, o el navegador no la encontrará ni la ejecutará. Por defecto, esto se hace con la ayuda de un [poco de JavaScript](https://developers.google.com/web/fundamentals/instant-and-offline/service-worker/registration) . En las páginas AMP, usa el componente [`amp-install-serviceworker`](../../../documentation/components/reference/amp-install-serviceworker.md) para lograr lo mismo.

Para eso, primero incluya el componente [`amp-install-serviceworker`](../../../documentation/components/reference/amp-install-serviceworker.md) través de su script en el `<head>` de su página:

[sourcecode:html]

<script async custom-element="amp-install-serviceworker"
  src="https://cdn.ampproject.org/v0/amp-install-serviceworker-0.1.js"></script>

[/sourcecode]

Luego agregue lo siguiente en algún lugar dentro de su `<body>` (modifique para que apunte a su trabajador de servicio real):

[sourcecode:html]
<amp-install-serviceworker
      src="https://www.your-domain.com/serviceworker.js"
      layout="nodisplay">
</amp-install-serviceworker>
[/sourcecode]

Si el usuario navega a sus páginas de AMP en su origen (a diferencia del primer clic, que generalmente se proporciona desde una caché de AMP), el trabajador de servicio se hará cargo y podrá hacer una [gran cantidad de cosas interesantes](https://developers.google.com/web/fundamentals/instant-and-offline/offline-ux) .

## El trabajador de servicios de AMP

Si estás aquí, estás creando páginas con AMP. El equipo de AMP se preocupa enormemente por poner al usuario en primer lugar y brindarle una experiencia web de clase mundial. Para mantener la coherencia de estas experiencias, el equipo de AMP ha creado un trabajador de servicio específicamente para AMP.

[tip type="default"] **SUGERENCIA:** siga nuestro tutorial para aprender a usar [AMP Service Worker en su PWA](/content/amp-dev/documentation/guides-and-tutorials/optimize-measure/amp_to_pwa.md) . [/tip]

### Instalación de AMP Service Worker

Instale AMP Service Worker con pasos mínimos:

- Importe el código AMP Service Worker en su archivo de service worker.

[sourcecode:js]
  importScripts('https://cdn.ampproject.org/sw/amp-sw.js');
  [/sourcecode]

- Instale el trabajador de servicio con el siguiente código.

[sourcecode:js]
  AMP_SW.init();
  [/sourcecode]

- Hecho.

### Almacenamiento en caché automatizado

AMP Service Worker almacena automáticamente en caché los archivos de script AMP y los documentos AMP. Al almacenar en caché los archivos de script AMP, están disponibles instantáneamente para el navegador de los usuarios, lo que permite la funcionalidad sin conexión y páginas más rápidas en redes inestables.

Si su aplicación requiere tipos específicos de almacenamiento en caché de documentos, AMP Service Worker permite la personalización. Como agregar una lista de denegación para documentos que siempre deben solicitarse a la red. En el siguiente ejemplo, reemplace `Array<RegExp>` con una matriz de expresiones regulares que representan los documentos que desea evitar almacenar en caché.

[sourcecode:js]
AMP_SW.init(
documentCachingOptions: {
denyList?: Array<RegExp>;
}
);
[/sourcecode]

Lea más sobre cómo [personalizar el almacenamiento en caché de documentos aquí](https://github.com/ampproject/amp-sw/tree/master/src/modules/document-caching) .

### Optimización del trabajador de servicio AMP

Para utilizar AMP Service Worker en todas sus capacidades, los campos opcionales deben configurarse para almacenar en caché los activos necesarios y precargar enlaces.

Los activos que impulsan la visita del usuario a una página, como un video, imágenes importantes o un PDF descargable, deben almacenarse en caché para que se pueda acceder a ellos nuevamente si el usuario no está conectado.

[sourcecode:js]
AMP_SW.init(
assetCachingOptions: [{
regexp: /\.(png|jpg)/,
cachingStrategy: 'CACHE_FIRST'
}],
);
[/sourcecode]

Puede personalizar la estrategia de almacenamiento en caché y definir una lista de denegación.

Los enlaces a las páginas que sus usuarios pueden necesitar visitar se pueden obtener previamente, lo que permite acceder a ellos sin conexión. Esto se hace agregando un atributo de `data-prefetch` a la etiqueta de enlace.

[sourcecode:html]
<a href='....' data-rel='prefetch' />
[/sourcecode]

### Experiencia sin conexión

Comunique al usuario que se ha desconectado y debe intentar volver a cargar el sitio cuando vuelva a estar conectado, incluyendo una página sin conexión. El AMP Service Worker puede almacenar en caché tanto la página como sus activos.

[sourcecode:js]
AMP_SW.init({
offlinePageOptions: {
url: '/offline.html';
assets: ['/images/offline-header.jpg'];
}
})
[/sourcecode]

Una página sin conexión exitosa parece ser parte de su sitio al tener una interfaz de usuario consistente con el resto de la aplicación.

### Forzar actualización

El equipo está trabajando para implementar una función de forzar actualización / eliminación si su AMP Service Worker necesita ser deshabilitado o cambiado si una implementación para los usuarios ha salido mal.

Para administrar eficazmente un trabajador del servidor, debe comprender cómo [el almacenamiento en caché HTTP estándar afecta la forma en que el JavaScript de su trabajador del servicio se mantiene actualizado](https://developers.google.com/web/updates/2018/06/fresher-sw) . Los trabajadores de servicios que cuentan con las directivas de almacenamiento en caché HTTP adecuadas pueden resolver pequeñas correcciones de errores al realizar los cambios apropiados y volver a implementar su trabajador de servicios en su entorno de alojamiento. Si necesita eliminar un trabajador de servicio, es una buena idea tener a mano un archivo de trabajador de servicio simple y [sin operaciones](https://en.wikipedia.org/wiki/NOP) , como el siguiente:

```js
self.addEventListener('install', () => {
  // Skip over the "waiting" lifecycle state, to ensure that our
  // new service worker is activated immediately, even if there's
  // another tab open controlled by our older service worker code.
  self.skipWaiting();
});
```

[tip type="read-on"] [Obtenga más información](https://stackoverflow.com/questions/33986976/how-can-i-remove-a-buggy-service-worker-or-implement-a-kill-switch/38980776#38980776) sobre cómo administrar los trabajadores del servicio desplegados. [/tip]

## Escribir un trabajador de servicio personalizado

Puede utilizar la técnica anterior para habilitar el acceso sin conexión a su sitio web AMP, así como extender sus páginas **tan pronto como se publiquen desde el origen** . Esto se debe a que puede modificar la respuesta a través del evento de `fetch` del trabajador de servicio y devolver la respuesta que desee:

[sourcecode:js]
self.addEventListener('fetch', function(event) {
event.respondWith(
caches.open('mysite').then(function(cache) {
return cache.match(event.request).then(function(response) {
var fetchPromise = fetch(event.request).then(function(networkResponse) {
cache.put(event.request, networkResponse.clone());
return networkResponse;
})

        // Modify the response here before it goes out..
        ...

        return response || fetchPromise;
      })
    })

);
});
[/sourcecode]

Con esta técnica, puede modificar su página AMP con todo tipo de funciones adicionales que de otro modo fallarían la [validación AMP](../../../documentation/guides-and-tutorials/learn/validation-workflow/validate_amp.md) , por ejemplo:

- Funciones dinámicas que requieren JS personalizado.
- Componentes personalizados / solo relevantes para su sitio.
