---
layout: post
section-type: post
title: One passwords
category: tech
tags: [ 'opensource', 'cypherpunk', 'bitcoin' ]
---
This is the second <a href="https://panossakkos.github.io/tech/2015/11/01/one-passwords.html"
target="blank">iteration</a> of creating a stateless password manager.
The stateless property is valuable because it eliminates the need to install software, or create accounts.
Moreover, there is nothing that an attacker can steal, everything is plain
computations using a secret key, the user's passphrase.
A realization of a stateless password manager could be as simple as a client-only web app.
That way you can build trust with the users, because they can read the javascript
code that it's being executed on their machine.

The first iteration was using a combination of a passphrase and a service tag
as user's input. These two were hashed and mapped to a block of Bitcoin's Block chain.
The mapped block's hash was hashed and the digest was the password.
I quickly realized that there was no reason to use the Block
chain. You can simply hash the two inputs.

<pre><code data-trim class="javascript">
password = hash(passphrase + serviceTag)
</code></pre>

Let's assume that I start using this technique for every service that I need to authenticate against.
In an ideal world all these services are protecting my passwords by not storing them
as plaintext and by gating the login attempts. This ideal world is not even close to
what is happening today and my password computation technique is as weak as the less secure
service that I use it against. Because the less secure service that I'm using
will be attacked and the attacker will get his hands on my plain text password.
Of course my stolen password is not reused anywhere, thanks to the use of the service tag.
However the service tag must be considered common knowledge, which means that the attacker
(let's assume that he knows how I compute my passwords) will brute force the passphrase
and if my passphrase is weak, he will find it in reasonable time.
Let's see if we can mitigate that by adding in the computation what all the service
providers should be doing in their systems in order to protect my password.

The common and effective approach against the <a
href="https://en.wikipedia.org/wiki/Rainbow_table#Defense_against_rainbow_tables"
target="blank">rainbow table attacks</a> (which are used to brute force hashed passwords)
is to use a <a href="https://en.wikipedia.org/wiki/Salt_(cryptography)" target="blank">salt</a>.
You can imagine a salt as a very strong password that it's appended to the user's password and then hashed.
This breaks the precomputed tables because the attacker has to recompute them for each possible salt value.
We can easily pick this salt from the Block chain, in order to maintain our stateless property.

<pre><code data-trim class="javascript">
salt = blockChain[hash(passphrase + serviceTag) modulo bigPrimeBlockHeight].hash
password = hash(passphrase + serviceTag + salt)
</code></pre>

Also, let's slow down the attacker by
<a href="https://en.wikipedia.org/wiki/Key_stretching" target="blank">stretching</a>
the computed password.

<pre><code data-trim class="javascript">
salt = blockChain[hash(passphrase + serviceTag) modulo bigPrimeBlockHeight].hash
password = hash(passphrase + serviceTag + salt)

for i = 0, i < keyStretchingIterations, i++
  password = hash(password)
</code></pre>

Here is the source for SHA256 as the cryptographic hash, 382777 possible salt values and 65000 iterations of stretching:

<pre><code data-trim class="javascript">
// Highest prime number available in the Bitcoin blockchain to use as the modulo
// for the block mapping that its hash will serve as the salt.
var PrimeBlock = 382777;

var StretchingIterations = 65000;

function computePassword(passphrase, serviceTag) {

  // No (security) need to make the user remember case sensitive service names
  serviceTag = serviceTag.toLowerCase();

  var saltBlock = parseInt('0x' + sha256(passphrase + serviceTag)) % PrimeBlock;

  var xhttp = new XMLHttpRequest();
  var url = 'https://bitcoin.toshi.io/api/v0/blocks/' + saltBlock;

  xhttp.onreadystatechange = function() {

    if (xhttp.readyState == 4 && xhttp.status == 200) {

      var salt = JSON.parse(xhttp.responseText).hash;
      var password = sha256(passphrase + serviceTag + salt);

      for (i = 0; i < StretchingIterations; i++) {
          password = sha256(password);
      }

      document.getElementById('password').value = password;
    }
  }

  xhttp.open('GET', url, true);
  xhttp.send();
}
</code></pre>

Results *for_an_1m4Gin4ry* secret passphrase:

<pre><code data-trim class="xml">
Service Name:  gmail
Password:  78b8e2c01f26c91f6281876772b289ccb8c7ae644f5b2bb195cef27d06459b44
Service Name:  facebook
Password:  806b939f8707e431612c40649fa1de8538408fc7d490488e81c06321828141e6
Service Name:  github
Password:  8d8aa6e37da2a3fafb20e937af638bf8386cfdad949be3fa3fa1729e823dc010
</code></pre>

If you are interested in dogfooding
<a href="https://panossakkos.github.io/one-passwords/" target="blank">it</a> or
if you have any suggestions, feel free to join the gitter channel.

[![Join the chat at https://gitter.im/PanosSakkos/one-passwords](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/PanosSakkos/one-passwords?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
