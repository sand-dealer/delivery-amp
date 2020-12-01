---
"$title": Crea un mapa de asientos
"$order": '104'
description: Los mapas de asientos son partes importantes de las aplicaciones web de los emisores de boletos, pero la implementación en AMP puede ser difícil. Siga leyendo para aprender cómo implementar un mapa de asientos en AMP por
tutorial: cierto
formats:
- sitios web
author: kul3r4
contributors:
- pbakaus
---

Los mapas de asientos son partes importantes de las aplicaciones web de los emisores de boletos, pero la implementación en AMP puede ser difícil. Siga leyendo para aprender cómo implementar un mapa de asientos en AMP mediante el uso de una combinación de componentes AMP disponibles.

{} GL_PH0 Una muestra viva la aplicación de las prácticas descritas a continuación está disponible [aquí](../../../documentation/examples/documentation/SeatMap.html) . [/tip]

## Componentes AMP necesarios

Comencemos revisando los componentes necesarios:

### amplificador-panorámica-zoom

[`amp-pan-zoom`](../../../documentation/components/reference/amp-pan-zoom.md) permite ampliar y desplazar el contenido mediante un doble toque y pellizcando. Este componente sirve como base para la implementación del mapa de asientos.

### amp-list

[`amp-list`](../../../documentation/components/reference/amp-list.md) recupera contenido dinámicamente desde un punto final CORS JSON y lo renderiza usando una plantilla proporcionada. Se utiliza para obtener la disponibilidad actual del mapa de asientos, de modo que los usuarios siempre obtengan los datos más recientes.

### amp-bind

[`amp-bind`](../../../documentation/components/reference/amp-bind.md) agrega interactividad a la página. Se necesita aquí para realizar un seguimiento de cuántos asientos se han seleccionado.

### selector de amplificador

[`amp-selector`](../../../documentation/components/reference/amp-selector.md) representa un control que presenta un menú de opciones y permite al usuario elegir entre él. El mapa de asientos completo puede considerarse un menú de opciones donde cada asiento es una opción. Facilita mucho el estilo del estado seleccionado para los asientos al permitirle usar expresiones CSS. Por ejemplo, la siguiente expresión llena un asiento con un color naranja una vez seleccionada.

```css
rect[selected].seat {
  fill: var(--orange-theme);
}
```

## Requisitos

1. Para dibujar un Mapa de asientos como un SVG donde cada asiento está representado por un `rect` elemento, necesita información sobre cada asiento: posición `x` y `y` , `width` y `height` y, posiblemente, `rx` y `ry` para redondear las esquinas de los rectángulos.
2. Un identificador único para cada asiento que se puede utilizar para hacer la reserva.
3. Una medida de todo el ancho y alto del mapa de asientos que se utilizará en el atributo `viewbox` .

## Dibujar el mapa de asientos

El mapa de asientos se representa a través de [`amp-list`](../../../documentation/components/reference/amp-list.md) y [`amp-mustache`](../../../documentation/components/reference/amp-mustache.md) . Después de recibir los datos de la llamada [`amp-list`](../../../documentation/components/reference/amp-list.md) , puede usar dichos datos para iterar a través de los asientos:

[sourcecode:html]
{% raw %}<svg preserveAspectRatio="xMidYMin slice" viewBox="0 0 {{width}} {{height}}">
{{#seats}}
<rect option="{{id}}" role="button" tabindex="0" class="seat {{unavailable}}" x="{{x}}" y="{{y}}" width="{{width}}" height="{{height}}" rx="{{rx}}" ry="{{ry}}"/>
{{/seats}}
</svg>{% endraw %}
[/sourcecode]

## Diseñar asientos no disponibles

En el ejemplo anterior, `{% raw %}{{unavailable}}{% endraw %}` es el valor de un campo devuelto por el extremo JSON y utilizado para diseñar un asiento no disponible. Este enfoque no le permite eliminar atributos como `option="{{id}}"` en caso de que un asiento no esté disponible, ya que la plantilla no puede ajustar el elemento `<html>` las páginas completas.

Un enfoque alternativo y más detallado es repetir las etiquetas de la siguiente manera:

[sourcecode:html]
{% raw %}{{#available }}{% endraw %}
<rect option="{{id}}" role="button" tabindex="0" class="seat" x="{{x}}" y="{{y}}" width="{{width}}" height="{{height}}" rx="{{rx}}" ry="{{ry}}"/>{% raw %}{{/available }}{% endraw %}

{% raw %}{{^available}}{% endraw %}<rect role="button" tabindex="0" class="seat unavailable" x="{{x}}" y="{{y}}" width="{{width}}" height="{{height}}" rx="{{rx}}" ry="{{ry}}"/>{% raw %}{{/available }}{% endraw %}
[/sourcecode]

## Dimensionando su mapa de asientos

A menos que el tamaño de su mapa de asientos sea fijo, es difícil ajustar el tamaño de la [`amp-list`](../../../documentation/components/reference/amp-list.md) contiene el mapa de asientos. [`amp-list`](../../../documentation/components/reference/amp-list.md) necesita dimensiones fijas o use `layout="fill"` (para usar el espacio disponible del contenedor principal). Hay dos formas de abordar este problema:

1. Calcule el espacio disponible en la página una vez que sepa el espacio utilizado por otros componentes como encabezados y pies de página. Este cálculo se puede hacer en CSS usando la expresión `calc` y asignándola como la `min-height` de un div padre de la [`amp-list`](../../../documentation/components/reference/amp-list.md) .
2. Utilice un diseño flexible cuando sepa la altura del diseño de la página.

## Estilo amp-pan-zoom

Si usa el enfoque descrito en la sección anterior, [`amp-pan-zoom`](../../../documentation/components/reference/amp-pan-zoom.md) debe usar `layout="fill"` .

[tip type="tip"] **SUGERENCIA:** para mantener un espacio en blanco alrededor del mapa de asientos y aun así hacerlo parte del área de pellizco y zoom:

- Agregue un div de envoltura para el svg
- Agregar relleno

Si no tiene un div de ajuste y agrega margen al SVG, no hará que los márgenes formen parte del área de pellizco y zoom. [/tip]

## Estado de manejo

Cuando los usuarios hacen clic en diferentes asientos, es posible realizar un seguimiento de los `id` asientos seleccionados en una variable utilizando `amp-state` , ya sea por:

- Agregar una expresión [`amp-bind`](../../../documentation/components/reference/amp-bind.md) para cada asiento para agregar el asiento seleccionado a una lista
- O usando [`amp-selector`](../../../documentation/components/reference/amp-selector.md) con la acción `on="select:AMP.setState({selectedSeats: event.selectedOptions})"` para que todos los asientos seleccionados se agreguen a una lista

Si bien el primer enfoque no requiere el [`amp-selector`](../../../documentation/components/reference/amp-selector.md) componente adicional, puede hacer que el mapa de asientos sea muy lento porque cada expresión de [`amp-bind`](../../../documentation/components/reference/amp-bind.md) se evaluará en cada selección / deselección de asiento.

El segundo enfoque también le permite reducir la duplicación de la expresión [`amp-bind`](../../../documentation/components/reference/amp-bind.md) para cada asiento que será representado por la plantilla.

## Estructura HTML final

Como referencia, aquí está el HTML final para el mapa de asientos:

[sourcecode:html]
{% raw %}<div class="seatmap-container">
  <amp-list layout="fill" src="/json/seats.json" binding="no" items="." single-item noloading>
    <template type="amp-mustache">
      <amp-pan-zoom layout="fill" class="seatmap">
        <amp-selector multiple on="select:AMP.setState({
          selectedSeats: event.selectedOptions
        })" layout="fill">
          <div class="svg-container">
            <svg preserveAspectRatio="xMidYMin slice" viewBox="0 0 {{width}} {{height}}">
            {{#seats}}
              <rect option="{{id}}" role="button"
               tabindex="0" class="seat {{unavailable}}"
              x="{{x}}" y="{{y}}"
              width="{{width}}" height="{{height}}"
              rx="{{rx}}" ry="{{ry}}"/>
            {{/seats}}
            </svg>
          </div>
        </amp-selector>
      </amp-pan-zoom>
    </template>
  </amp-list>
</div>{% endraw %}
[/sourcecode]
