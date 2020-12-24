## Secciones de filtrado

Algunos documentos pueden ser relevantes para múltiples formatos AMP, pero ciertos formatos pueden necesitar más explicaciones o información que no es relevante para los demás. Puede filtrar estas secciones envolviéndolas en el siguiente shortcode1.

<div class="ap-m-code-snippet">
<pre>&amp;lsqb;filter formats="websites"]
This is only visible for [websites](?format=websites).
&amp;lsqb;/filter]

&amp;lsqb;filter formats="websites"]
This is only visible for [websites](?format=websites).
&amp;lsqb;/filter]

&amp;lsqb;filter formats="websites, email"]
This is visible for [websites](?format=websites) &amp; [email](?format=email).
&amp;lsqb;/filter]

&amp;lsqb;filter formats="stories"]
This is visible for [stories](?format=stories).
&amp;lsqb;/filter]</pre>
</div>
