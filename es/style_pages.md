---
"$title": CSS compatible
description: Como todas las páginas web, las páginas AMP tienen estilo con CSS, pero no puedes hacer referencia a hojas de estilo externas con la excepción de fuentes personalizadas. Además, ciertos estilos no están permitidos ...
formats:
- sitios web
- correo electrónico
- anuncios
- cuentos
author: Meggin
contributors:
- pbakaus
- CrystalOnScript
- bpaduch
- choumx
---

[filter formats="email"] Nota: AMP para correo electrónico especifica restricciones de CSS adicionales que se describen en [AMP para CSS compatible con correo electrónico](../../../../documentation/guides-and-tutorials/learn/email-spec/amp-email-css.md) . [/filter]

Como todas las páginas web, las páginas AMP tienen estilo con CSS, pero no puedes hacer referencia a hojas de estilo externas (con la excepción de [fuentes personalizadas](#the-custom-fonts-exception) ). Además, ciertos estilos no están permitidos debido a implicaciones en el rendimiento.

Los estilos pueden estar en el encabezado del documento o como atributos de `style` línea (consulte [Agregar estilos a una página](index.md#add-styles-to-a-page) ). Pero puede usar preprocesadores CSS y plantillas para crear páginas estáticas para administrar mejor su contenido.

[tip type="note"] **NOTA: los** componentes de AMP vienen con estilos predeterminados para facilitar la creación de páginas receptivas. Estos estilos se definen en [`amp.css`](https://github.com/ampproject/amphtml/blob/master/css/amp.css) . [/tip]

## Estilos no permitidos

Los siguientes estilos no están permitidos en las páginas AMP:

<table>
  <thead>
    <tr>
      <th class="col-thirty" data-th="Banned style">Estilo prohibido</th>
      <th data-th="Description">Descripción</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="Banned style">
<code>!important</code> qualifier </td>
      <td data-th="Description">No se permite el uso y referencia a <code>!important</code> . Este es un requisito necesario para que AMP pueda hacer cumplir sus reglas de tamaño de elementos.</td>
    </tr>
    <tr>
      <td data-th="Banned style"><code>&lt;link rel=”stylesheet”&gt;</code></td>
      <td data-th="Description">No permitido con la excepción de <a href="#the-custom-fonts-exception">fuentes personalizadas</a> .</td>
    </tr>
    <tr>
      <td data-th="Banned style">
<code>i-amphtml-</code> class and <code>i-amphtml-</code> tag names.</td>
      <td data-th="Description">The validator disallows class and tags names with the following regex `(^|\W)i-amphtml-`. These are reserved for internal use by the AMP framework. It follows, that the user's stylesheet may not reference CSS selectors for <code>i-amphtml-</code> classes and tags.</td>
    </tr>
  </tbody>
</table>

## Recomendaciones de desempeño

Estos estilos permitidos deben restringir los valores a lo siguiente para un rendimiento óptimo:

<table>
  <thead>
    <tr>
      <th class="col-thirty" data-th="Banned style">Estilo restringido</th>
      <th data-th="Description">Descripción</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="Restricted style">propiedad de <code>transition</code>
</td>
      <td data-th="Description">Solo propiedades aceleradas por GPU (actualmente <code>opacity</code> , <code>transform</code> y <code>-vendorPrefix-transform</code> ).</td>
    </tr>
    <tr>
      <td data-th="Restricted style"><code>@keyframes {...}</code></td>
      <td data-th="Description">Solo propiedades aceleradas por GPU (actualmente <code>opacity</code> , <code>transform</code> y <code>-vendorPrefix-transform</code> ).</td>
    </tr>
  </tbody>
</table>

## La excepción de fuentes personalizadas<a name="the-custom-fonts-exception"></a>

Las páginas de AMP no pueden incluir hojas de estilo externas, con la excepción de fuentes personalizadas.

[tip type="read-on"] SIGUE **LEER:** Obtén más información sobre las [fuentes personalizadas en AMP](custom_fonts.md) . [/tip]

## Usando preprocesadores CSS<a name="using-css-preprocessors"></a>

La salida generada por los preprocesadores funciona tan bien en AMP como en cualquier otra página web. Por ejemplo, el sitio [amp.dev](https://amp.dev/) usa [Sass](http://sass-lang.com/) . (Usamos [Grow](http://grow.io/) para crear las páginas AMP estáticas que componen el sitio [amp.dev](https://amp.dev/) ).

When using preprocessors, pay special attention to what you include; load only what your pages use. For example, the [head.html](https://github.com/ampproject/docs/blob/master/views/partials/head.html) includes all required AMP mark-up and the inlined CSS from the `*.scss` source files. It also includes the custom element script for [`amp-youtube`](../../../../documentation/components/reference/amp-youtube.md), among others, so that many pages across the site can include embedded youtube videos.

[sourcecode:html]{% raw %}
&lt;head&gt;
  &lt;meta charset="utf-8"&gt;
  &lt;meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1"&gt;
  &lt;meta property="og:description" content="{% if doc.description %}{{doc.description}} – {% endif %}AMP Project"&gt;
  &lt;meta name="description" content="{% if doc.description %}{{doc.description}} – {% endif %}AMP Project"&gt;

  &lt;title&gt;AMP Project&lt;/title&gt;
  &lt;link rel="icon" href="/static/img/amp_favicon.png"&gt;
  &lt;link rel="canonical" href="{{doc.url}}"&gt;
  &lt;link href="https://fonts.googleapis.com/css?family=Roboto:200,300,400,500,700" rel="stylesheet"&gt;
  &lt;style amp-custom&gt;
  {% include "/assets/css/main.min.css" %}
  &lt;/style&gt;

  &lt;style amp-boilerplate&gt;body{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}&lt;/style&gt;&lt;noscript&gt;&lt;style amp-boilerplate&gt;body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}&lt;/style&gt;&lt;/noscript&gt;
  &lt;script async src="https://cdn.ampproject.org/v0.js"&gt;&lt;/script&gt;
  &lt;script async custom-element="amp-carousel" src="https://cdn.ampproject.org/v0/amp-carousel-0.1.js"&gt;&lt;/script&gt;
  &lt;script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"&gt;&lt;/script&gt;
  &lt;script async custom-element="amp-lightbox" src="https://cdn.ampproject.org/v0/amp-lightbox-0.1.js"&gt;&lt;/script&gt;
  &lt;script async custom-element="amp-youtube" src="https://cdn.ampproject.org/v0/amp-youtube-0.1.js"&gt;&lt;/script&gt;
  &lt;script async custom-element="amp-sidebar" src="https://cdn.ampproject.org/v0/amp-sidebar-0.1.js"&gt;&lt;/script&gt;
  &lt;script async custom-element="amp-iframe" src="https://cdn.ampproject.org/v0/amp-iframe-0.1.js"&gt;&lt;/script&gt;
&lt;/head&gt;
{% endraw %}[/sourcecode]

Para ver cómo lo anterior se traduce en HTML AMP formateado, vea el código fuente de cualquier página en [amp.dev](https://amp.dev/) . (En Chrome, haga clic con el botón derecho y `View Page Source` ).
