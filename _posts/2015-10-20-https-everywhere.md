---
layout: post
section-type: post
title: Contributing to HTTPS Everywhere
category: tech
tags: [ 'opensource', 'tor' ]
---
There is a very simple way to have an impact on privacy and security by contributing to
<a href="https://www.eff.org/https-everywhere" target="blank">HTTPS Everywhere</a>.
It's a collaboration project between the
<a href="https://www.torproject.org/">Tor project</a> and the
<a href="https://www.eff.org/">Electronic Frontier Foundation</a> (EFF).

<small>Stripped from the project's webpage:</small>

>HTTPS Everywhere is a Firefox, Chrome, and Opera extension that encrypts your communications with many major websites, making your browsing more secure.

Note that *HTTPS Everywhere* comes pre-installed and pre-enabled with the
<a href="https://www.torproject.org/download/download-easy.html.en" target="blank">Tor browser bundle</a> as well. The "*many major websites*" from the description, practically means that there are rules defined for the websites that support https.

How can you contribute?

While browsing with *HTTPS Everywhere* enabled, if you see a website that is not on https, try to access it with https. If the website is still functional, then you can very easily add a rule for it. Fork the Github
<a href="https://github.com/EFForg/https-everywhere" target="blank"> repo</a> and

<pre><code data-trim class="bash">
cd src/chrome/content/rules
./make-trivial-rule &lt;the domain you want to add&gt;
</code></pre>

And the default rule set file will be created. For example the trivial rule for

<pre><code data-trim class="bash">
./make-trivial-rule di.uoa.gr
</code></pre>

will look like this

<pre><code data-trim class="xml">
&lt;ruleset name="National and Kapodistrian University of Athens - Department of Informatics and Telecommunications"&gt;
  &lt;target host="di.uoa.gr" /&gt;
  &lt;target host="www.di.uoa.gr" /&gt;
  &lt;rule from="^http:" to="https:" /&gt;
&lt;/ruleset&gt;
</code></pre>

Open a pull request and that's it!
A very quick way to have quite some impact on protecting the privacy and the security of the many 😉
