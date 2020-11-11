---
"$title": Precarga tu PWA desde tus páginas AMP
"$order": '1'
description: Una buena estrategia es convertir el punto de entrada en su sitio en una página AMP, luego calentar la PWA detrás de escena y cambiar a ...
formats:
- sitios web
author: pbakaus
---

Una buena estrategia es hacer que el **punto de entrada a su sitio sea una página AMP** , luego **calentar la PWA detrás de escena** y cambiar a ella para continuar:

- Todas las páginas de "hoja" de contenido (aquellas que tienen contenido específico, no páginas de descripción general) se publican como AMP para esa experiencia de carga casi instantánea.
- Estos AMP utilizan el elemento especial [`amp-install-serviceworker`](../../../documentation/components/reference/amp-install-serviceworker.md) AMP para calentar un caché y el shell de PWA mientras el usuario disfruta de su contenido.
- Cuando el usuario hace clic en otro enlace en su sitio web (por ejemplo, la llamada a la acción en la parte inferior para una experiencia más similar a una aplicación), el trabajador del servicio intercepta la solicitud, se hace cargo de la página y carga el shell de PWA en su lugar.

Siga leyendo para saber por qué y cómo utilizar este patrón de desarrollo.

## Mejore el recorrido del usuario conectándose a una PWA

### AMP para la adquisición inicial de usuarios

AMP is an ideal solution for so-called **leaf pages**, content pages that your users discover organically through a search engine, a shared link by a friend or through a link on another site. Because of AMP's [specialized pre-rendering](../../../about/how-amp-works.html), AMP pages load extremely fast, which in return means much less drop off (the latest [DoubleClick study](https://www.doubleclickbygoogle.com/articles/mobile-speed-matters/) shows that **more than 53% of all users will drop off after 3 seconds**).

### PWA para una rica interactividad y compromiso

Progressive Web Apps, on the other hand, allow for much greater interactivity and engagement, but do not have the *instant first-load characteristics* of an AMP page. At their core is a technology called Service Worker, a client-side proxy that allows you to cache all sorts of assets for your pages, but said Service Worker only activates *after* the first load.

{{image ('/ static / img / docs / pwamp_comparison.png', 977, 549, align = '', caption = 'Los pros y los contras de AMP frente a PWA.')}}

## Calienta tu PWA con `amp-install-serviceworker`

AMP tiene la capacidad de instalar el Service Worker de su aplicación web progresiva desde dentro de una página AMP; sí, ¡incluso si esa página AMP se sirve desde una caché AMP! Si se hace correctamente, un enlace que conduce a su PWA (desde una de sus páginas AMP) se sentirá casi instantáneo, similar al primer salto a la página AMP.

[tip type="tip"] **SUGERENCIA:** si aún no está familiarizado con Service Worker, le recomiendo el [curso Udacity de](https://www.udacity.com/course/offline-web-applications--ud899) Jake Archibald. [/tip]

Primero, instale el trabajador de servicio en todas sus páginas AMP usando [`amp-install-serviceworker`](../../../documentation/components/reference/amp-install-serviceworker.md) , incluyendo primero el componente a través de su secuencia de comandos en el `<head>` de su página:

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

En última instancia, en el paso de instalación del trabajador del servicio, almacene en caché los recursos que necesitará la PWA:

[sourcecode:javascript]
var CACHE_NAME = 'my-site-cache-v1';
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/script/main.js'
];

self.addEventListener('install', function(event) {
  // Perform install steps
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});
[/sourcecode]

[tip type="tip"] **SUGERENCIA:** hay formas más fáciles de tratar con un trabajador de servicios. Eche un vistazo a las [bibliotecas auxiliares de Service Worker](https://github.com/GoogleChrome/sw-helpers) . [/tip]

## Hacer que todos los enlaces de una página AMP naveguen hasta la PWA

Lo más probable es que la mayoría de los enlaces en sus páginas AMP conduzcan a más páginas de contenido. Existen dos estrategias para asegurarse de que los siguientes clics en los enlaces generen una "actualización" de la aplicación web progresiva, [según la forma en que utilice AMP](../../../documentation/guides-and-tutorials/optimize-measure/discovery.md) :

### 1. Si empareja sus páginas canónicas con páginas AMP

En este caso, tiene un sitio web canónico (no AMP) y genera páginas AMP que están vinculadas a estas páginas canónicas. Actualmente, esta es la forma más común en que se usa AMP y significa que los enlaces en sus páginas AMP probablemente enlazarán con la versión canónica de su sitio. **Buenas noticias: si su sitio canónico es su PWA, está listo** .

### 2. Si su sitio canónico es AMP

En este caso, sus páginas canónicas *son* sus páginas AMP: está construyendo su sitio web completo con AMP y simplemente usa AMP como una biblioteca (hecho curioso: el mismo sitio en el que está leyendo esto está construido de esta manera). **En este escenario, la mayoría de los enlaces en sus páginas AMP conducirán a otras páginas AMP.**

Ahora puede implementar su PWA en una ruta separada como `your-domain.com/pwa` y usar el Service Worker que ya se está ejecutando para **interceptar la navegación del navegador cuando alguien hace clic en un enlace en la página AMP** :

[sourcecode:javascript]
self.addEventListener('fetch', event => {
    if (event.request.mode === 'navigate') {
      event.respondWith(fetch('/pwa'));

      // Immediately start downloading the actual resource.
      fetch(event.request.url);
    }

});
[/sourcecode]

Lo que es especialmente interesante de esta técnica es que ahora está utilizando la mejora progresiva para pasar de AMP a PWA. Sin embargo, esto también significa que, tal como están, los navegadores que aún no son compatibles con los trabajadores del servicio pasarán de AMP a AMP y nunca navegarán hasta la PWA.

AMP solves this with something called [shell URL rewriting](../../../documentation/components/reference/amp-install-serviceworker.md#shell-url-rewrite). By adding a fallback URL pattern to the [`amp-install-serviceworker`](../../../documentation/components/reference/amp-install-serviceworker.md) tag, you are instructing AMP to rewrite all matching links on a given page to go to another legacy shell URL instead, if no service worker support has been detected:

[sourcecode:html]
<amp-install-serviceworker
      src="https://www.your-domain.com/serviceworker.js"
      layout="nodisplay"
      data-no-service-worker-fallback-url-match=".*"
      data-no-service-worker-fallback-shell-url="https://www.your-domain.com/pwa">
</amp-install-serviceworker>
[/sourcecode]

Con estos atributos en su lugar, todos los clics posteriores en un AMP irán a su PWA, independientemente de cualquier trabajador de servicio.

[tip type="read-on"] SIGUE **LEER:** Ya ha llegado tan lejos. ¿Por qué no reutiliza sus páginas AMP existentes para crear su PWA? [He aquí cómo](amp-in-pwa.md) . [/tip]
