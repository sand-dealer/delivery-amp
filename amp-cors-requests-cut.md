Okay, so the page is _on my domain_ and the JSON is _on my domain_. I see no
problem!

<amp-img alt="CORS and Cache" layout="responsive" src="https://www.ampproject.org/static/img/docs/CORS_with_Cache.png" width="809" height="391">
  <noscript>
    <img alt="CORS and Cache" src="https://www.ampproject.org/static/img/docs/CORS_with_Cache.png" />
  </noscript>
</amp-img>

**Okay, what should I do?**

_Example: Including personalized content in an amp-list via cookies_

[sourcecode:html]
<amp-list
  credentials="include"
  src="<%host%>/json/product.json?clientId=CLIENT_ID(myCookieId)"
>
  <template type="amp-mustache">
    Your personal offer: ${% raw %}{{price}}{% endraw %}
  </template>
</amp-list>
[/sourcecode]

Let's walk though these scenarios with an example. In our example, we manage the `example.com` site that hosts an AMP page named `article-amp.html.`The AMP page contains an `amp-list` to retrieve dynamic data from a `data.json` file that is also hosted on `example.com`. We want to process requests to our `data.json` file that come from our AMP page. These requests could be from the AMP page on the same origin (non-cached) or from the AMP page on a different origin (cached).

<amp-img alt="CORS example" layout="fixed" src="https://www.ampproject.org/static/img/docs/cors_example_walkthrough.png" width="629" height="433">
  <noscript>
    <img alt="CORS example" src="https://www.ampproject.org/static/img/docs/cors_example_walkthrough.png" />
  </noscript>
</amp-img>

### Allowed origins <a name="allowed-origins"></a>

### Scenario 1: Get request from AMP page on same origin <a name="scenario-1-get-request-from-amp-page-on-same-origin"></a>

In the following scenario, the `article-amp.html` page requests the `data.json` file; the origins are the same.

<amp-img alt="CORS example - scenario 1" layout="fixed" src="https://www.ampproject.org/static/img/docs/cors_example_walkthrough_ex1.png" width="657" height="155">
  <noscript>
    <img alt="CORS example" src="https://www.ampproject.org/static/img/docs/cors_example_walkthrough_ex1.png" />
  </noscript>
</amp-img>

### Scenario 2: Get request from cached AMP page <a name="scenario-2-get-request-from-cached-amp-page"></a>

In the following scenario, the `article-amp.html` page cached on the Google AMP Cache requests the `data.json` file; the origins differ.

<amp-img alt="CORS example - scenario 2" layout="fixed" src="https://www.ampproject.org/static/img/docs/cors_example_walkthrough_ex2.png" width="657" height="155">
  <noscript>
    <img alt="CORS example" src="https://www.ampproject.org/static/img/docs/cors_example_walkthrough_ex2.png" />
  </noscript>
</amp-img>
