---
layout: post
section-type: post
title: One password to rule them all? (pt 2)
category: tech
tags: [ 'opensource', 'cypherpunk', 'bitcoin' ]
---

<small>*Longer than usual post is following*</small>

This is the second iteration of creating a stateless password manager.
Stateless is valuable because it eliminates the need to download special software, or create accounts.
The realization of a stateless password manager could be ass simple as a web app.
Another important aspect is open-source, given that this can build the trust of the
(hopefully!) reluctant users.

The first iteration was using a combination of a secret passphrase and a service name
as user's input. These two were hashed and then mapped to a block of Bitcoin's Block chain.
Finally this block's hash was the returned password.

Next morning, (as soon as I woke up!) I realized that this was <strike>stupid</strike> naive.

There was no reason to use the Block chain at all, you can just hash the two inputs!
So, this is what we have right now


````
hash(secretPassphrase + serviceName)
````
