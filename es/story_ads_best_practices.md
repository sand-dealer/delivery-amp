---
"$title": Mejores prácticas para crear un anuncio de historia web
"$order": dieciséis
description: Las Historias web son una experiencia táctil en pantalla completa que sumerge a los lectores en el contenido. Los anuncios que aparecen en Web Stories deben tener un diseño coherente y coherente con Web Stories UX.
formats:
- anuncios
- cuentos
---

Las Historias web son una experiencia táctil en pantalla completa que sumerge a los lectores en el contenido. Los anuncios que aparecen en Web Stories deben tener un diseño coherente y coherente con Web Stories UX. Esto evita una experiencia de usuario discordante o interrumpida. Esta guía demuestra cómo crear un anuncio para Web Stories.

## Principios de los anuncios de historias web

Los formatos de anuncios actuales, como banners y cuadros, no se integran bien con el formato AMP Story. Los anuncios clásicos son lentos, interrumpen y se sienten fuera de lugar dentro de la experiencia de Story.

Los anuncios de historias web se ajustan a los siguientes principios:

- Anuncio HTML de AMP válido: siga las mismas especificaciones técnicas que un [anuncio HTML de AMP](https://github.com/ampproject/amphtml/blob/master/extensions/amp-a4a/amp-a4a-format.md) clásico.
- Visual primero: estado de invitación atractivo, audaz y basado en el contexto.
- Nativo: la página del anuncio tiene las mismas dimensiones que una página de historia orgánica.
- Mismo modelo de interacción: el usuario puede pasar a la siguiente pantalla como lo haría con una página de historia orgánica.
- Rápido: el anuncio nunca aparece a un usuario en un estado medio cargado.

Para ser coherente con estos principios, el tiempo de ejecución de la historia web determina la ubicación correcta de una página de anuncios en medio de la historia web. Obtenga más información sobre la mecánica de colocación de anuncios en [Publicidad en Historias web](advertise_amp_stories.md) .

## Ejemplo de anuncio de historia web

Los anuncios de Web Story son anuncios HTML de AMP, pero requieren datos de metaetiquetas, cumplen con las especificaciones de diseño definidas y los elementos de la interfaz de usuario requeridos. Un anuncio de historia web siempre incluirá un botón de llamada a la acción (CTA) y una etiqueta de anuncio que se muestra como un texto de exención de responsabilidad en la parte superior de la página.

{{image (&#39;/ static / img / docs / stampads / stamp_ad.png&#39;, 425, 800, layout = &#39;intrinsic&#39;, alt = &#39;Ejemplo de un anuncio de AMP Story&#39;, caption = &#39;Ejemplo de un anuncio de AMP Story&#39; , alinear = &#39;&#39;)}}

Para mantener la coherencia de la experiencia del usuario, el tiempo de ejecución de Web Story es responsable de representar la etiqueta del anuncio y el botón de CTA.

[tip type="important"] **IMPORTANTE:** solo se puede hacer clic en el botón de CTA en un anuncio de historia web, así que tenga esto en cuenta cuando desarrolle su creatividad. [/tip]

## Datos de metaetiquetas

Los datos de la metaetiqueta especifican que el anuncio cumple con el formato Web Story, establece la enumeración del texto del botón de CTA, indica dónde enviará el botón al usuario y qué tipo de página es.

[sourcecode:html]

&lt;html amp4ads&gt;
  &lt;head&gt;
    &lt;meta charset="utf-8"&gt;
    &lt;meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1"&gt;

    &lt;!-- Specifies where the user is directed --&gt;
    &lt;meta name="amp-cta-url" content="%%CLICK_URL_UNESC%%%%DEST_URL%%"&gt;

    &lt;!-- Specifies the call to action button text enum --&gt;
    &lt;meta name="amp-cta-type" content="EXPLORE"&gt;

    &lt;!-- Specifies what type of landing page the user is direct to --&gt;
    &lt;meta name="amp-cta-landing-page-type" content="NONAMP"&gt;

    &lt;style amp4ads-boilerplate&gt;body{visibility:hidden}&lt;/style&gt;
    &lt;style amp-custom&gt;
     amp-img {height: 100vh}
    &lt;/style&gt;
    &lt;script async src="https://cdn.ampproject.org/amp4ads-v0.js"&gt;&lt;/script&gt;

  &lt;/head&gt;
  &lt;body&gt;
    &lt;amp-img src=%%FILE:JPG1%% layout="responsive" height="1280" width="720"&gt;&lt;/amp-img&gt;
  &lt;/body&gt;
&lt;/html&gt;
[/sourcecode]

Se recomienda elegir la `amp-cta-type tag` de las [opciones de texto del botón de CTA disponibles](#call-to-action-button-text-enum) . AMP localizará automáticamente las opciones predefinidas cuando sea apropiado.

Se permite el texto personalizado, pero deberá implementar su propia localización.

## Enumeración del texto del botón de llamada a la acción<a name="call-to-action-button-text-enum"></a>

El botón de llamada a la acción se puede configurar a partir de un conjunto predefinido de opciones:

- `APPLY_NOW` : "Aplicar ahora"
- `BOOK_NOW` : "Libro"
- `BUY_TICKETS` : "Comprar entradas"
- `DOWNLOAD` : "Descargar"
- `EXPLORE` : "Explorar ahora"
- `GET_NOW` : "Obtener ahora"
- `INSTALL` : "Instalar ahora"
- `LISTEN` : "Escuchar ahora"
- `MORE` : "Más"
- `OPEN_APP` : "Abrir aplicación"
- `ORDER_NOW` : "Solicitar ahora"
- `PLAY` : "Jugar"
- `READ` : "Leer ahora"
- `SHOP` : "Compre ahora"
- `SHOWTIMES` : "Horarios"
- `SIGN_UP` : "Registrarse"
- `SUBSCRIBE` : "Suscríbete ahora"
- `USE_APP` : "Usar aplicación"
- `VIEW` : "Ver"
- `WATCH` : "Ver"
- `WATCH_EPISODE` : "Ver episodio"

[tip type="note"] **NOTA: los** enlaces profundos a las aplicaciones no son compatibles, pero los enlaces a la página de la App Store o la página de Google Play Store son compatibles mediante http / https. La enumeración del texto del botón de CTA se especifica en la carga útil de la respuesta del anuncio. [/tip]

Si se necesita soporte para una nueva enumeración de texto de botón de CTA, abra un [problema de GitHub](https://github.com/ampproject/amphtml/issues/new) .

## Página de destino del anuncio

Puede especificar una de las tres opciones para la página de inicio de un anuncio de historia web.

- `STORY` : La página de destino es una [historia patrocinada](story_ads_best_practices.md#sponsored-story) .
- `AMP` : la página de destino es una página AMP válida.
- `NONAMP` : Cualquier otro tipo de página web.

## Diseño

Las historias de AMP son horizontales y de pantalla completa. Los anuncios de historias deben coincidir con este formato para brindar una experiencia de usuario consistente.

## Dimensiones superpuestas

La etiqueta del anuncio se superpone a una barra de degradado oscura en todo el ancho del anuncio y se extenderá desde la parte superior hasta 46 px hacia abajo.

{{image (&#39;/ static / img / docs / stampads / ad_overlay.png&#39;, 515, 520, layout = &#39;intrinsic&#39;, alt = &#39;Demostración de la superposición de anuncios&#39;, caption = &#39;La superposición de anuncios se encuentra en la parte superior&#39;, alinear = &#39;&#39;)}}

El CTA se encuentra a 32px desde la parte inferior y está centrado horizontalmente. Tiene 120 px por 36 px.

{{image (&#39;/ static / img / docs / stampads / cta_button.png&#39;, 515, 520, layout = &#39;intrinsic&#39;, alt = &#39;Demostración del botón CTA&#39;, caption = &#39;El botón CTA se encuentra cerca de la parte inferior&#39; , alinear = &#39;&#39;)}}

## Imágenes y video

Las imágenes y los videos incluidos en un anuncio de AMP Story deben ser de pantalla completa estándar 4: 3. Los anuncios que incluyen video deben usar un [póster.](../../../documentation/components/reference/amp-video.md#poster) Las dimensiones recomendadas para una imagen de póster son 720p (720w x 1280h).

[sourcecode:html]
&lt;amp-video controls
  width="720"
  height="1280"
  layout="responsive"
  poster="images/kitten-playing.png"&gt;

  &lt;source src="videos/kitten-playing.webm"
    type="video/webm" /&gt;
  &lt;source src="videos/kitten-playing.mp4"
    type="video/mp4" /&gt;
  &lt;div fallback&gt;
    &lt;p&gt;This browser does not support the video element.&lt;/p&gt;
  &lt;/div&gt;
&lt;/amp-video&gt;
[/sourcecode]

### Imagenes

Las imágenes de fondo se pueden escalar a pantalla completa. El siguiente CSS es una forma exitosa de recortar y centrar videos e imágenes.

[sourcecode:html]

&lt;style amp-custom&gt;
    amp-img, amp-video {
        height: 100vh;
    }
    amp-video video {
        object-fit: cover;
    }
    amp-img img{
        object-fit: cover;
    }
&lt;/style&gt;

[/sourcecode]

### Vídeo

#### Especificar `<source>` vs `src`

Al especificar la fuente para un [`amp-video`](../../../documentation/components/reference/amp-video.md)

Ejemplo: especificar varios archivos de origen

[sourcecode:html]
&lt;amp-video id="video-page1" autoplay loop
  layout="fill" poster="https://example.com/media/poster.jpg"&gt;

  &lt;source src="https://amp-example.com/media/movie.m3u8"
    type="application/vnd.apple.mpegurl" /&gt;
  &lt;source src="https://amp-example.com/media/movie.mp4"
    type="video/mp4" /&gt;
&lt;/amp-video&gt;
[/sourcecode]

#### Tamaño y duración del video

Para un rendimiento óptimo, debe intentar proporcionar videos que no superen los 4 MB. Los tamaños de archivo más pequeños permiten una descarga más rápida, así que mantenga las cosas lo más pequeñas posible.

#### Formatos de video

Si solo puede proporcionar un formato de video único, proporcione **MP4** . Sin embargo, siempre que sea posible, use video **HLS** y especifique MP4 como respaldo para los navegadores que aún no admiten video HLS. HLS realiza una transmisión de velocidad de bits adaptativa, donde la calidad del video puede modificarse para adaptarse mejor a la conexión de red del usuario.

[tip type="note"] **NOTA:** el formato de video HLS no es compatible con el navegador Chrome para escritorio (ni siquiera mediante emulación), por lo que es necesario especificar un respaldo MP4 para cualquier tráfico de escritorio a su página. Para depurar videos de HLS, deberá utilizar un dispositivo móvil real a través de depuración USB. [/tip]

#### Resolución de video

Los videos de Web Story son siempre verticales (es decir, vista vertical), con una relación de aspecto esperada de 16: 9. Utilice la resolución recomendada para el tipo de transmisión de video:

<table>
  <thead>
    <tr>
     <th>Tipo de transmisión de video</th>
     <th>Resolución</th>
    </tr>
  </thead>
  <tbody>
    <tr>
     <td>No adaptativo</td>
     <td>720 x 1280 px</td>
    </tr>
    <tr>
     <td>Adaptado</td>
     <td>720 x 1280 px<br> 540 x 960 px<br> 360 x 480 px</td>
    </tr>
  </tbody>
</table>

[tip type="note"] **NOTA:** para los dispositivos móviles que difieren de la relación de aspecto 16: 9, el video se puede recortar horizontal o verticalmente para ajustarse a la ventana gráfica. [/tip]

#### Códec de vídeo

1. Para MP4, use `H.264` .
2. Para WEBM, use `VP9` .
3. Para HLS o DASH, use `H.264` .

#### Calidad de video

##### Optimizaciones de transcodificación

Hay varias herramientas que puede utilizar para codificar videos y ajustar la calidad del video durante la codificación. Éstos son solo algunos:

<table>
  <thead>
    <tr>
     <th>Herramienta</th>
     <th>Notas</th>
    </tr>
  </thead>
  <tbody>
    <tr>
     <td><a href="https://www.ffmpeg.org/about.html">FFmpeg</a></td>
     <td>Optimizaciones recomendadas:<ul>
<li> Para MP4, use <code>-crf 23</code> .</li>
<li> Para WEBM, utilice <code>-b:v 1M</code> .</li>
</ul>
</td>
    </tr>
    <tr>
     <td><a href="https://libav.org/avconv.html">avconv</a></td>
     <td>Optimizaciones recomendadas:<ul>
<li> Para MP4, use <code>-crf 23</code> .</li>
<li> Para WEBM, utilice <code>-b:v 1M</code> .</li>
</ul>
</td>
    </tr>
    <tr>
     <td><a href="https://github.com/google/shaka-packager">Empaquetador Shaka</a></td>
     <td>Un codificador que también puede generar el formato HLS, incluida la lista de reproducción.</td>
    </tr>
  </tbody>
</table>

##### Tamaño del segmento HLS

Asegúrese de que el tamaño de los segmentos de HLS no suele tener más de 10 segundos de duración.

## Animación

Las animaciones tienen algunas salvedades en las historias, como el concepto de lo que es "visible". Por ejemplo, en nuestra vista de escritorio de "3 paneles", su creatividad puede estar visible en la página, pero aún no en el centro de atención. Esto puede ser problemático si el efecto deseado es iniciar animaciones cuando una página se convierte en el punto focal principal.

Para ayudar con esto, AMP agregará un atributo especial `amp-story-visible` al cuerpo de su creatividad cuando sea el punto focal en todos los contextos de publicación. Se recomienda disparar animaciones basadas en esta señal.

Ejemplo: esta animación se activará cuando la página se enfoque y se reiniciará si un usuario hace clic en otra página de la historia y regresa.

[sourcecode:html]

&lt;style amp-custom&gt;
    body[amp-story-visible] .my-animation-class {
      animation: 2s my-animation-name;
    }
&lt;/style&gt;

[/sourcecode]

## Historia patrocinada<a name="sponsored-story"></a>

Una Historia patrocinada existe como una URL en la web, lo que permite dirigir el tráfico de usuarios a una Historia patrocinada desde el botón de llamada a la acción en un anuncio de Historia AMP. Una historia patrocinada es una historia de AMP, pero se centra en una experiencia publicitaria expansiva y envolvente.

{{image (&#39;/ static / img / docs / stampads / patrocinado_story_full.png&#39;, 1600, 900, layout = &#39;intrinsic&#39;, alt = &#39;El botón CTA dirige a una historia patrocinada&#39;, caption = &#39;El botón CTA dirige a una historia patrocinada Historia &#39;, align =&#39; &#39;)}}

Lea más sobre cómo crear una [historia web aquí](../start/create_successful_stories.md) .
