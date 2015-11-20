---
layout: post
section-type: post
title: One password to rule them all?
category: tech
tags: [ 'opensource', 'cypherpunk', 'bitcoin' ]
---
Passwords rule the world. In order to fully understand the power of passwords, imagine
a person who could guess all the passwords that exist. Wouldn't that person be ruling the world? 😉

Unfortunately passwords are really hard to handle and this is one of the most common
source of cyber incidents. Ideally we should have one unique password per service,
which doesn't contain words, is consisted by letters (both capital and non-capital), numbers and symbols <small>etc etc</small>. And, last but not least, we have to change these passwords every (i.e.) 3 months in order to be safe.

One of my constant headaches is that I don't follow all of these rules and lately this pain
has grown. I decided to update all my passwords (and keep updating them frequently) in order to be compliant with all the security suggestions.
For example I didn't have a unique password per service. Instead I had about 10 strong
passwords and I was assigning them to newly subscribed services, according to how
much I didn't want my account of this service to be hacked. For example, I was assigning my
less strong password to the services that I didn't care at all about being hacked.

I gave it some thought and I came up with the following system. I create a very strong password (let's call it passphrase) and every time that I want to create an account for service X, then by combining my passphrase and the service's name (let's call it service tag), X in this case, I can point to a block of <a href="https://en.wikipedia.org/wiki/Bitcoin" target="blank">Bitcoin's</a> <a href="https://en.wikipedia.org/wiki/Block_chain_(database)" target="blank">Block chain</a>. From this block, I can extract its hash,
 hash it and *voila!*, I got my password.

The way you can combine the secret passphrase with the service's tag, is by hashing
them and then assigning the digest to a block by computing the modulo of a recent
prime block of the (always ordered) Block chain. Yesterday the highest prime block of the Block chain was 381323.
Like this, you get a huge password, with letters and numbers,
so it's actually a very <a href="https://xkcd.com/936/" target="blank">strong</a> password and whenever I decide that I want to change my passwords, all I have to do is to change my secret passphrase.

I created a proof of concept web app that was using the following, in order to point to a block:

<pre><code data-trim class="javascript">
SHA256(passphrase + serviceTag) modulo 381323
</code></pre>

The javascript code to do that was less than 20 lines of code

<pre><code data-trim class="javascript">
function getBlockchainPassword(passphrase, serviceName) {
  // Highest prime number available in the Bitcoin blockchain to use as the modulo
  var primeBlock = 381323;

  // No (security) need to make the user remember case sensitive service names
  serviceName = serviceName.toLowerCase();

  var block = parseInt('0x' + passphrase + serviceName) % primeBlock;

  var xhttp = new XMLHttpRequest();
  var url = 'https://bitcoin.toshi.io/api/v0/blocks/' + block;

  xhttp.onreadystatechange = function() {
    if (xhttp.readyState == 4 && xhttp.status == 200) {
      var block = JSON.parse(xhttp.responseText);
      document.getElementById('password').value = sha256(block.hash);
    }
  }

  xhttp.open('GET', url, true);
  xhttp.send();
}
</code></pre>

I started using it and I realized that I had created a password manager without actually storing any data.
Also the Block chain cannot be altered, when a block is checkpointed, it's checkpointed for an eternity, thanks to the nodes of the Bitcoin network.

Results *for_an_1m4Gin4ry* secret passphrase

<pre><code data-trim class="xml">
Service Tag: gmail
Password:  61626e0726ff0ab201c97bfda68b8343bf7dbebad9365117f6da73c56b8b6241
Service Tag:  facebook
Password:  26fdff60be55a5934de2b56093dd9cc61bfd88c84f0bb0c80df5413f9a6c360b
Service Tag:  github
Password:  c4f8bc9fb5aea9d8d323d4e97b30e853a0ce531696527f75be72a7de66be4c9f
</code></pre>

P.S: There is a follow up [post](https://panossakkos.github.io/tech/2015/11/08/one-password-pt-2.html) on this
