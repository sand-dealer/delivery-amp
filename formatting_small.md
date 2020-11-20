## Filtering sections
Some documents may be relevant for multiple AMP formats, but certain formats may need further explanation or information that is not relevant to the others. You can filter these sections by wrapping them in the following shortcode1.

<div class="ap-m-code-snippet">
<pre>
&lsqb;filter formats="websites"]
This is only visible for [websites](?format=websites).
&lsqb;/filter]

&lsqb;filter formats="websites"]
This is only visible for [websites](?format=websites).
&lsqb;/filter]

&lsqb;filter formats="websites, email"]
This is visible for [websites](?format=websites) & [email](?format=email).
&lsqb;/filter]

&lsqb;filter formats="stories"]
This is visible for [stories](?format=stories).
&lsqb;/filter]
</pre>
</div>
