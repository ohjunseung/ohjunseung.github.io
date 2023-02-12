---
title: "Should password be constrained?"
date: 2022-12-06T12:12:05+07:00
slug: ""
description: "Different apps and programs require password to be matching some criteria. Is it necessary?"
keywords: []
draft: false
tags: [tech-rant]
math: false
toc: false
---
Password is one of the most common ways to authenticate ourselves online. Different applications, either web or mobile,
all have various constraints for their password input. Most of them need only the alphabet (hence the word in "password").
Some require different casing, numbers, and symbols. Length is also one of their criteria, a minimum of six characters
and a maximum of 32 characters. Is this really necessary? What's the benefit?

Of course, a longer password means harder for attackers to brute-force. But a maximum of 32 characters? Does it really make any
difference? Is it smaller to store in the database, hence doing it this way?
I even found an app that only allows a maximum eight-character password (or maybe exactly 8, idk).
Let's see how we store passwords nowadays to prove (or disprove) the idea.


## Password Storing
No one in their right mind will store any password verbatim. "yourPassword" would never be stored as "yourPassword".
We usually use a hashing algorithm to convert plaintext (password) into ciphertext (random text). Most common ones are,
[Argon2](https://en.wikipedia.org/wiki/Argon2), [bcrypt](https://en.wikipedia.org/wiki/Bcrypt), [scrypt](https://en.wikipedia.org/wiki/Scrypt).
I won't talk about how password hashing works; you can read more [here](https://en.wikipedia.org/wiki/Cryptographic_hash_function#Password_verification).

Okay, the constraint might be derived from the limitation of the password hashing algorithm. Like bcrypt for example,
have a maximum of 72 bytes input, practically 50 characters which are more than the "typical" maximum online.
Something like Argon2 can handle a huge input size. Google limits its password input
between 8 to 100 characters. Very logical; thinking something like argon2 can handle this input.

Usually, hashing algorithm will produce the same size output. So, the argument for saving storage by limiting password
input is pretty much invalid.
Hashing algorithm also takes raw bytes as input, so including symbols and changing letter casing doesn't really matter.

Let's try Argon2 in Argon2id mode with some input examples. Here I specify the output length to be 16 bytes.

```
parameters: $argon2id$v=19$m=16,t=2,p=1$
salt: kpYc2O20J0rLlpNZ

input                           -> output in hex
---
exactly8                        -> 9e7ccabb91d20a45c875d1f7cd974437
atozonly                        -> 67094c5c3a96cb205621d62a545666ae
AtozOnly                        -> 020f12120beec77286da2b96b67d6b4d
atoZwith+symbols=?              -> 69d9692f93a2e05ffb0ce512079ee632
atoZwith+symbols=?and123        -> 63d7094636891d4ca1e4a72e4e518b34
completelyrandomtextbutverylong -> fbabc6b60e7cce389724d537d3fc2772
---
```

## Conclusion
As we can see, password input doesn't have any effect on the hasher output. This means that storage size doesn't change
between inputs. All of the examples would produce precisely 16 bytes. We can safely say password doesn't need to be constrained.

The security of passwords depends on which algorithm is used and how you implement the algorithm. Any flaw in the algorithm
would compromise security. Improper use of the technology would also create flaws.
So this matter more than how complex your password input is. I'm not a security expert, and my advice might not be valid,
but usually, something long is enough to slow down brute-force attacks.
