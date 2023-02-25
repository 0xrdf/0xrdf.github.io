---
layout: post
title:  "Making money like magic"
date:   2023-02-25 10:12:34 -0300
categories: [hacking,web]
---


Hi, all ✊ 


In this blog I will try to explain a bug, which I found during my studies, using [https://twitter.com/WebSecAcademy](https://twitter.com/WebSecAcademy) ( PortSwigger ) challenge [https://twitter.com/WebSecAcademy/status/1627712571091431424](https://twitter.com/WebSecAcademy/status/1627712571091431424). If you are not yet familiar with their work, I highly recommend their labs and especially the book ( holy grail of web hacking ) - The Web Application Hacker's Handbook: Finding and Exploiting Security Flaws Authors: Dafydd Stuttard, Marcus Pinto.

tl;dr

For those who don't have patience, the bug is race condition between a financial transaction using peer to peer, so basically, I can "make" money.


-> I will not disclose the names of the target due to program policy.


First, if you don't know what a peer to peer transaction is, I recommend a quick read, here [https://en.wikipedia.org/wiki/Peer-to-peer_transaction](https://en.wikipedia.org/wiki/Peer-to-peer_transaction). Generally, this functionality is used when you have an account at bank A, and you transfer money to another person who also has an account at bank A. But what is it for? generally, this "transactional" peer to peer is used to make the transaction "faster" between users of the same bank (as far as I know, I could be wrong). So the next step was to make a request for another user who have an account at the same bank as me (My friend fx0). 

The following request shows **my** account balance before the attack:


![](/static/images/my_balance_01.png)



Then the following request illustrates my friend's bank balance:


![friend_balance.png](/static/images/friend_balance.png)


The next test was to send a minimum amount to my friend, the following request illustrates the fact:


![req_amount.png](/static/images/req_amount.png)


To prove that there was indeed a transfer, the following images illustrate the balance of the accounts:


* My account:


![my_balance_after.png](/static/images/my_balance_after.png)


* FX Account:

![fxo_balance.png](/static/images/fxo_balance.png)


One of the techniques i learned doing the labs [https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-infinite-money](https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-infinite-money) is that you can generate null payloads in the intruder, to test a functionality, or request, usually the null payload is used for a variety of attacks, for example, harvesting cookies for sequencing analysis, application-layer denial-of-service attacks where requests are repeatedly sent which initiate high-workload tasks on the server, or keeping alive a session token that is being used in other intermittent tests or in that case **race conditions**, if you dont know what is race conditions 👉 [https://book.hacktricks.xyz/pentesting-web/race-condition](https://book.hacktricks.xyz/pentesting-web/race-condition) and start the lab at portswigger academy 👉 [https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-race-condition](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-race-condition). The next step was to configure the intruder to perform the race condition attack.


![race_05.png](/static/images/race_05.png)

This attack can also be used, using the turbo intruder. Check the blog 👉 [https://portswigger.net/research/turbo-intruder-embracing-the-billion-request-attack](https://portswigger.net/research/turbo-intruder-embracing-the-billion-request-attack)

The next image illustrates the attack using the previously used configurations. Some requests were accepted, using the same transactional request, also note that other Transactions and payment UUIDs were generated:

![intruder_req_07.png](/static/images/intruder_req_07.png)

As can be seen in the previous image, 9 requests were approved, out of 100. So, in practice, I would have to have a balance of 0.00 in my account, and my friend would have to have 0.10. However, to my surprise, 0.09 was not withdrawn from my account, but only 0.03. And my friend won 0.09, that is, we make money like 🧙‍♂️ ✨ (in fact, he would be rich and I would be poor hahaha). 


The following images illustrate account balances after the attack:


* My account:

![final_01.png](/static/images/final_01.png)

* FX account:

![final_02.png](/static/images/final_02.png)


And in the end, the cents turned into bountys.


![bb.png](/static/images/bb.png)

A few notes, the devices UUID was used to bypass some application protections, to access my account and my friend's account. The X-Mobile header had to be spoofed.

But that's it. I hope I have contributed a little.