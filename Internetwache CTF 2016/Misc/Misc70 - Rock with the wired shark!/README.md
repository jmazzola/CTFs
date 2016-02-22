# misc70 - 'Rock with the wired shark'

>   Sniffing traffic is fun. 
>   I saw a wired shark. Isn't that strange?

>   Attachment: [misc70.zip](./misc70.zip)

After opening up misc70.zip we're greeted with **dump.pcapng**, a dump of sniffed traffic to be opened in Wireshark (who would've guessed from the challenge name~)

![Image of Wireshark](http://i.imgur.com/qsAJHxf.png)

After seeing what we're capturing, it's time to go hunting/sifting through packets! I did this easily by sorting the Protocol like so:

![Image of Sorted](http://i.imgur.com/ejfedsE.png)

There's some interesting requests like "flag.zip". It's too bad the destinations were on a local server/client. (192.168.1.x addresses), so we can't personally experiment on the links.

So that must mean we have the .zip data somewhere right?

This one packet (#83) looks really interesting as it got a response of **200 OK** and with an **octet-stream**.

For all of those that know what this means, it means we're actually grabbing some binary data/file. Who knows it could be the .zip! o.o

![Image of .zip](http://i.imgur.com/UrEMkC7.png)

It totes is! I've seen that PK header before~. That's totally the flag.zip contents with our flag.txt! Alright so let's just download that.

You can do that by right-clicking on the **'Media Type'** attribute and pick **'Export Packet Bytes'**.

Alright, let's see what's in here. 
Oh...a password protected file..

![PasswordProtected](http://i.imgur.com/lh9s9ch.png)

Well the password has to be somewhere.. There's authorization/authentication somehow, Internetwache wouldn't just give us a broken CTF lmao.

First thing I noticed is in the information of the packets. We first get **401 Unauthorized** with our **GET** requests and then we're **200 OK**. So there must be some HTTP-Authentication somewhere in these packets before that OK request..

![Password](http://i.imgur.com/BtkxfKL.png)

Well that's a basic HTTP auth! 

```
flag:azulcrema
```

Could that be our password to the .txt in the .zip file o.o?

![flag](http://i.imgur.com/v2kkTgC.png)

It is!

```
IW{HTTP_BASIC_AUTH_IS_EASY}
```

was the flag :3

