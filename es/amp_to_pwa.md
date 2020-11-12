---
"$title": Convierta su sitio AMP en una PWA
"$order": '10'
description: Al almacenar en caché los recursos dentro del navegador, una PWA puede proporcionar datos, activos y páginas fuera de línea al usuario para mantenerlo comprometido e informado.
tutorial: cierto
formats:
- sitios web
author: crystalonscript
---

Progressive Web Apps harness the power of [service workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) to enable rich offline abilities and consistent user experiences across varying network strengths. By caching resources within the browser, a PWA is able to provide data, assets, and offline pages to the user to keep them engaged and informed.

Este tutorial le enseñará cómo convertir un sitio AMP en una PWA instalable con capacidades fuera de línea agregando un Manifiesto Web y un Service Worker impulsado por AMP Service Worker.

# Descarga y ejecuta el código de inicio

Descarga el [código de inicio aquí](/static/files/tutorials/amptopwa.zip) .

Utilice un servidor web local para obtener una vista previa del sitio web.

[tip type="default"] **SUGERENCIA:** para un servidor web rápido, ejecute `python -m SimpleHTTPServer` . [/tip]

Debería poder ver la página de inicio de Lyrical Lightning, el festival Mobile Music Magic. Tiene un enlace en la página de inicio para ver el horario y en qué escenario están las bandas.

{{image ('/ static / img / docs / tutorials / tut-lyricallyghtning.png', 594, 558, alt = 'Imagen de PWA')}}

Los usuarios de nuestro sitio pueden tener una conectividad de red irregular en el evento cuando probablemente deseen acceder a la programación. Esto lo convierte en un gran candidato para convertirlo en una PWA que se puede instalar en la pantalla de inicio de nuestro usuario y proporciona todas las funciones críticas incluso sin conexión.

# Crear un manifiesto de aplicación web

El [manifiesto de la aplicación web](https://developers.google.com/web/fundamentals/web-app-manifest/) es un archivo JSON simple que le informa al navegador sobre su aplicación web y cómo debe comportarse cuando se 'instala' en el dispositivo móvil o escritorio del usuario. Muchos navegadores requieren tener un manifiesto para mostrar el [mensaje Agregar a la pantalla de inicio](https://developers.google.com/web/fundamentals/app-install-banners/) .

Agrega un archivo titulado `manifest.json` a tu repositorio con el siguiente código:

[sourcecode:JSON]
{
"short_name": "LyLy",
"name": "Lyrical Lyghtning",
"icons": [
{
"src": "./images/amplogo192.png",
"type": "image/png",
"sizes": "192x192"
},
{
"src": "./images/amplogo512.png",
"type": "image/png",
"sizes": "512x512"
}
],
"start_url": "/index.html",
"background_color": "#222325",
"display": "standalone",
"scope": "/",
"theme_color": "#222325"
}
[/sourcecode]

# Agregar el trabajador de servicio de AMP

Un trabajador de servicio es un script que su navegador ejecuta en segundo plano, separado de una página web, que amplía las funciones de los navegadores al almacenar en caché las solicitudes para mejorar el rendimiento y proporcionar funcionalidad sin conexión. Construir un trabajador de servicios desde cero es posible, pero requiere mucho tiempo. Las bibliotecas como Workbox ayudan, pero AMP va un paso más allá al ofrecer [AMP Service Worker](https://github.com/ampproject/amp-sw) , en el que AMP automatiza muchos pasos directamente, incluido el almacenamiento en caché de scripts, activos y documentos de AMP, así como la implementación de mejores prácticas comunes como la [precarga de navegación](https://developers.google.com/web/updates/2017/02/navigation-preload) .

The AMP Service Worker automatically [caches AMP scripts](https://github.com/ampproject/amp-sw/tree/master/src/modules/amp-caching) and [documents](https://github.com/ampproject/amp-sw/tree/master/src/modules/document-caching) as user requests them, after installing it. We'll start by adding the basic AMP Service Worker.

## Crear el archivo de trabajador de servicio

Cree un archivo llamado `sw.js` y agregue el siguiente código:

[sourcecode:js]
importScripts('https://cdn.ampproject.org/sw/amp-sw.js');
AMP_SW.init();
[/sourcecode]

Con solo dos líneas de código, esto importa el AMP Service Worker a su Service Worker y lo inicializa.

## Instale automáticamente su trabajador de servicio en sus páginas AMP

Los sitios web de AMP utilizan el componente [`<amp-install-serviceworker>`](../../../documentation/components/reference/amp-install-serviceworker.md) para instalar el trabajador del servicio en el fondo del navegador, mientras el usuario disfruta de su contenido.

Coloque la etiqueta de secuencia de comandos requerida en el encabezado de `index.html` y el elemento `<amp-install-serviceworker>` dentro de `<body>` :

[sourcecode:html]
…

<script async custom-element="amp-install-serviceworker" src="https://cdn.ampproject.org/v0/amp-install-serviceworker-0.1.js"></script>

…
...
<amp-install-serviceworker src="/sw.js"
           data-iframe-src="install-sw.html"
           layout="nodisplay">
</amp-install-serviceworker>

</body>
[/sourcecode]

[tip type="important"] **Importante:** el trabajador del servicio debe recibir servicios desde el directorio raíz ( `/sw.js` ) para poder almacenar en caché todo el contenido de su sitio. [/tip]

`<amp-install-serviceworker>` instala el trabajador del servicio creando un iframe y ejecutando el archivo `data-iframe-src` . Cree el archivo `install-sw.html` y agregue el siguiente código:

[sourcecode:html]

<!doctype html>
<title>installing service worker</title>
<script type='text/javascript'>
 if('serviceWorker' in navigator) {
   navigator.serviceWorker.register('./sw.js');
 };
</script>
[/sourcecode]

El iframe registra el archivo AMP Service Worker en el navegador.

# Personaliza lo que se almacena en caché

AMP Service Worker viene con beneficios incorporados al tiempo que permite campos opcionales que puede configurar para optimizar en función de las necesidades de su aplicación.

Nuestra aplicación de festival de música almacenará en caché nuestros activos de imagen, precargará el enlace de alineación y especificará una página sin conexión.

## Activos de caché

You can configure the AMP Service Worker to [cache assets](https://github.com/ampproject/amp-sw/tree/master/src/modules/asset-caching), such as images, videos and fonts. We'll use it to cache our background image and the AMP logo. Open the `sw.js` file and update it to the code below:

[sourcecode:js]
importScripts('https://cdn.ampproject.org/sw/amp-sw.js');
AMP_SW.init({
assetCachingOptions: [{
regexp: /\.(png|jpg)/,
cachingStrategy: 'CACHE_FIRST'
}]
});
[/sourcecode]

We've specified the caching strategy to be [cache first](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/#cache-falling-back-to-network). The means the app will try to serve images from cache first before requesting anything from the network. This is especially useful for this app since we won't be updating our background image or the AMP logo.

## Vínculos de captación previa

The AMP Service Worker prefetches links that have the `data-rel=prefetch` attribute. This enables users to view pages offline even if they haven't visited them yet. We'll add the attribute to our link tag for `lineup.html`.

[sourcecode:html]
...
<a href="/lineup.html" data-rel="prefetch">See Full Lineup</a>
...
[/sourcecode]

# Mostrar una página sin conexión

Para hacer frente a casos inesperados o clics en enlaces a páginas que no obtuvimos previamente, agregaremos una página sin conexión para ofrecer una experiencia de usuario coherente que sea "de marca", en lugar de mostrar la página sin conexión del navegador genérico. Descargue [`offline.html` aquí](/static/files/tutorials/offline.zip) y actualice `sw.js` al siguiente código:

[sourcecode:js]
importScripts('https://cdn.ampproject.org/sw/amp-sw.js');
AMP_SW.init({
assetCachingOptions: [{
regexp: /\.(png|jpg)/,
cachingStrategy: 'CACHE_FIRST'
}],
offlinePageOptions: {
url: '/offline.html',
assets: []
}
});
[/sourcecode]

# Prueba tu PWA

Puede probar que su AMP Service Worker está almacenando en caché los activos necesarios y brindando una solución sin conexión ideal a través de [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/progressive-web-apps) .

Probamos Lyrical Lyghtning abriendo el panel DevTools `Ctrl + Shift + I` en Windows o `Cmd + Opt + I` en Mac. También puede hacer clic derecho en la página y seleccionar `inspect` en el menú. Luego seleccione `Application` para ver su registro de trabajador de servicio.

{{image ('/ static / img / docs / tutorials / amp-sw-test.png', 1349, 954, alt = 'Panel de DevTools abierto en Lyrical lyghtning PWA')}}

Click the `offline` box to switch into offline mode. Click the `see full lineup` link and navigate to `offline.html` to check if they were properly cached and prefetched.

[tip type="default"] **Sugerencia:** para un análisis exhaustivo de las funciones de una aplicación web progresiva, ejecute [la herramienta Lighhouse de Google](https://developers.google.com/web/ilt/pwa/lighthouse-pwa-analysis-tool) para generar un informe. [/tip]

# ¡Felicidades!

¡Ha creado con éxito una PWA con AMP! En este tutorial aprendiste a:

- Crear un [manifiesto de aplicación web](https://developers.google.com/web/fundamentals/web-app-manifest/)
- Instale un Service Worker en AMP con [`amp-install-serviceworker`](../../../documentation/components/reference/amp-install-serviceworker.md)
- Personalizar el [trabajador de servicio de AMP](https://amp.dev/documentation/guides-and-tutorials/optimize-and-measure/amp-as-pwa.html)
- [Vínculos de captación previa](https://developer.mozilla.org/en-US/docs/Web/HTTP/Link_prefetching_FAQ)
- Crea una página sin conexión

Obtenga más información sobre las consideraciones de [Service Worker](https://amp.dev/documentation/guides-and-tutorials/optimize-and-measure/amp-as-pwa.html) y [UX offline](https://developers.google.com/web/fundamentals/instant-and-offline/offline-ux) . Aprenda a [interactuar con la analítica](https://amp.dev/documentation/guides-and-tutorials/optimize-measure/configure-analytics/index.html) y siga el tutorial sobre [cómo configurar la analítica básica para sus páginas AMP](https://amp.dev/documentation/guides-and-tutorials/optimize-and-measure/tracking-engagement.html) .
