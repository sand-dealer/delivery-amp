---
"$title": Estructura y renderizado de correos electrónicos AMP
order: '2'
formats:
- correo electrónico
teaser:
  text: |2-

    El correo electrónico está estructurado como un árbol MIME. Este árbol MIME contiene el cuerpo del mensaje
    y cualquier archivo adjunto al correo electrónico.
toc: cierto
---

<!--
This file is imported from https://github.com/ampproject/amphtml/blob/master/spec/email/amp-email-structure.md.
Please do not change this file.
If you have found a bug or an issue please
have a look and request a pull request there.
-->

<!---
Copyright 2018 The AMP HTML Authors. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS-IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

El correo electrónico está estructurado como un [árbol MIME](https://en.wikipedia.org/wiki/MIME) . Este árbol MIME contiene el cuerpo del mensaje y los archivos adjuntos al correo electrónico.

To embed AMP within an email, add a new MIME part with a content type of `text/x-amp-html` as a descendant of `multipart/alternative`. It should live alongside the existing `text/html` or `text/plain` parts. This ensures that the email message works on all clients.

<amp-img alt="AMP for Email MIME Parts Diagram" layout="responsive" width="752" height="246" src="https://github.com/ampproject/amphtml/raw/master/spec/img/amp-email-mime-parts.png"><noscript data-md-type="raw_html" data-segment-id="2510926"><img data-md-type="raw_html" alt="Diagrama de piezas de AMP para correo electrónico MIME" src="../img/amp-email-mime-parts.png"></noscript></amp-img>

Para obtener más información sobre el subtipo `multipart/alternative` , consulte [RFC 1521, sección 7.2.3](https://tools.ietf.org/html/rfc1521#section-7.2.3) .

## Información Adicional<a name="additional-information"></a>

La parte `text/x-amp-html` debe estar anidada en un nodo `multipart/alternative` . Un correo electrónico no puede tener más de una parte `text/x-amp-html` dentro de un nodo `multipart/alternative` .

La `multipart/alternative` debe contener al menos un nodo que no sea AMP ( `text/plain` o `text/html` ) además del nodo `text/x-amp-html` . Esto se mostrará a los usuarios cuyos clientes de correo electrónico no admiten AMP o que se excluyeron a través de la configuración de su proveedor de correo electrónico.

Nota: Algunos clientes de correo electrónico [[1]](https://openradar.appspot.com/radar?id=6054696888303616) solo renderizarán la última parte MIME, por lo que recomendamos colocar la parte `text/x-amp-html` MIME *antes de* la parte `text/html` MIME.

### Semántica de respuesta / reenvío<a name="replyingforwarding-semantics"></a>

El cliente de correo electrónico elimina la parte `text/x-amp-html` del árbol MIME cuando un usuario responde o reenvía un mensaje de correo electrónico AMP.

### Expiración<a name="expiry"></a>

The email client may stop displaying the AMP part of an email after a set period of time, e.g. 30 days. In this case, emails will display the `text/html` or `text/plain` part.

## Ejemplo<a name="example"></a>

<!-- prettier-ignore-start -->

[sourcecode:html]
From:  Person A <persona@example.com>
To: Person B <personb@example.com>
Subject: An AMP email!
Content-Type: multipart/alternative; boundary="001a114634ac3555ae05525685ae"

--001a114634ac3555ae05525685ae
Content-Type: text/plain; charset="UTF-8"; format=flowed; delsp=yes

Hello World in plain text!

--001a114634ac3555ae05525685ae
Content-Type: text/x-amp-html; charset="UTF-8"

<!doctype html>
<html ⚡4email>
<head>
  <meta charset="utf-8">
  <style amp4email-boilerplate>body{visibility:hidden}</style>
  <script async src="https://cdn.ampproject.org/v0.js"></script>
</head>
<body>
Hello World in AMP!
</body>
</html>
--001a114634ac3555ae05525685ae
Content-Type: text/html; charset="UTF-8"

<span>Hello World in HTML!</span>
--001a114634ac3555ae05525685ae--
[/sourcecode]

<!-- prettier-ignore-end -->
