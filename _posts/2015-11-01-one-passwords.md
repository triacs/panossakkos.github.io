---
layout: post
section-type: post
title: One password to rule them all?
category: tech
tags: [ 'opensource', 'cypherpunk', 'bitcoin' ]
---
<small>Longer than usual post is following</small>

Passwords rule the world. In order to fully understand the power of passwords, imagine
a person who could guess all the passwords that exist. Wouldn't that person ruling the world? 😉

Unfortunately passwords are really hard to handle and this is one of the most common
source of cyber incidents. Ideally we should have one unique password per service,
which doesn't contain words, is consisted by letters (both capital and non-capital), numbers and symbols <small>etc etc</small>. And, last but not least, we have to change these passwords every (i.e.) 3 months in order to be safe.

One of my constant headaches is that I don't follow all of these rules and lately this pain
has grown. I decided to updated all my passwords (and keep updating them every a couple of months) in order to be compliant with all the security suggestions.
For example I didn't have a unique password per service. Instead I had about 10 strong
passwords and I was assigning them to newly subscribed services, according to how
much I didn't want my account of this service to be hacked. For example, I was assigning my
less strong password to the services that I didn't care at all about being hacked.

I gave it some thought and I came up with the following system. I create a very strong password and every time that I want to create an account for service X, then I combine
these two pieces in order to point to block of <a href="https://en.wikipedia.org/wiki/Bitcoin" target="_blank">Bitcoin's</a> <a href="https://en.wikipedia.org/wiki/Block_chain_(database)" target="_blank">Block chain</a>. From this block, I extract its hash and *voila!* I have my password for service X.

Bitcoin's block hashes are <a href="https://xkcd.com/936/" target="_blank">64 characters long</a> and their first characters are 0s, depending on which generation they were checkpointed. And the way you can combine the secret passphrase with the service's name, is by computing their hash (i.e. SHA256) and then assigning this hash to a block by performing the modulo of a recent prime block of the (always ordered) Block chain. Yesterday the highest prime block of the Block chain was 381323. Like this, you get a huge password, with letters (not a combination of capital and non-capital letter) and numbers,
so it's a very strong password and whenever I decide that I want to change my passwords, all I have to do is to change my secret passphrase.

I created a <a href="https://panossakkos.github.io/one-passwords/" target="_blank">web app</a> that was using the following's block hash as the password

````
SHA256(secretPassphrase + serviceName) % 381323
````

The javascript code to do that was less than 20 lines of code

<script src="https://gist.github.com/PanosSakkos/363e2fbc98e0fd4eafd3.js"></script>

I started using it and I realized that I had created a password manager without actually storing any data(!). Also the blockchain cannot be altered, when a block is checkpointed, it's checkpointed for an eternity, thanks to the nodes of the Bitcoin network.

Results *for_an_1m4Gin4ry* secret passphrase

<pre style="text-align: left">
Service Name:  gmail
Password:  00000000000000000b853aa9881e0026fe0417b54ec3cdab639ce3e7ab1713cf
Service Name:  facebook
Password:  000000000000000011112dbd4483573fdfa2d9e80719794c8e2d7e273cc729f2
Service Name:  github
Password:  000000000038c6e01f40a04069e74b910d9a34b22f679799a6657f9dd49b46f2
</pre>

Of course this web app is not secure enough for actual use. It's a proof of concept and the minimum work that is missing, is that instead of this code running on client side, it should be on server side and that server has to be a node of the Bitcoin system (instead of querying REST APIs that expose information about the Blockchain). Also the blocks from the first generations should be excluded, by adding this offset to the final result, in order to have a minimum guarantee of password length.
