# misc80 - '404 flag not found'

>   I tried to download the flag, but somehow received only 404 errors :(

>   Attachment: [misc80.zip](./misc80.zip)

Once we open up the .zip, we're greeted with yet another **.pcapng file** . Let's open that up.

![pcap](http://i.imgur.com/gbzY52b.png)

All of these packets are really really odd. In two ways:

* The URLs have a subdomain of '2015' and not '2016'
* They look like blobs of hexidecimals.

So the first thing I did was see if one of the links was indeed a hexidecimal string. Easily do this by right clicking the packet's **Queries/Name** and pick **Copy>Value**

```
496e2074686520656e642c206974277320616c6c2061626f757420666c61
```

The first hexidecimal string came out to be:

```
In the end, it's all about fla
```

So each of these URLs ARE just encoded hexidecimal strings.

I took the time to lay out my notes like so:

```
496e2074686520656e642c206974277320616c6c2061626f757420666c61
67732e0a5768657468657220796f752077696e206f72206c6f736520646f
65736e2774206d61747465722e0a7b4f66632c2077696e6e696e67206973
20636f6f6c65720a44696420796f752066696e64206f7468657220666c61
67733f0a4e6f626f62792066696e6473206f7468657220666c616773210a
53757065726d616e206973206d79206865726f2e0a5f4845524f2121215f
0a48656c70206d65206d7920667269656e642c2049276d206c6f73742069
6e206d79206f776e206d696e642e0a416c776179732c20616c776179732c
20666f72206576657220616c6f6e652e0a437279696e6720756e74696c20
49276d206479696e672e0a4b696e6773206e65766572206469652e0a536f
20646f20492e0a7d210a
```

Decoding this hex-string gives us this:

```
In the end, it's all about flags..Whether you win or lose doesn't matter..{Ofc, winning is cooler.Did you find other flags?.Noboby finds other flags!.Superman is my hero.._HERO!!!_.Help me my friend, I'm lost in my own mind..Always, always, for ever alone..Crying until I'm dying..Kings never die..So do I..}!.
```

Weird. I'd be lying if I wasn't stumped until I took a shower and got a clean mindset to realize that these groups of text held a hidden message.

Notice how there's barely any capital letters? And the first ones that are read 'IW'?

Well taking this theory, eliminating everything but the capital letters, I got this:

```
IW{ODNSHEROHIACIKSI}
```

Weird. Whatever I'll just submit it for the lawls and ohhh..it's not right. Well it doesn't look right. So I redid the process, allowing the underscores:

```
IW{ODNS_HERO_HIACIKSI}
```

Nope. Still not right.

Okay maybe there's some sort of rule that is applied on top of the actual capital letter rule.

I noticed that there is one distinctive byte (0x0A - linefeed) between each 'phrase'. So I split them up like so:

```
In the end, it's all about flags.
Whether you win or lose doesn't matter.
{Ofc, winning is cooler.
Did you find other flags?.
Noboby finds other flags!.
Superman is my hero.
_HERO!!!_.
Help me my friend, I'm lost in my own mind.
Always, always, for ever alone.
Crying until I'm dying.
Kings never die.
So do I.
}!.
```

Oh wow. The answer was literally right in front of me the whole time. It only takes the first capital letter or symbol before detecting another period. Reading that vertically gives the correct flag:

```
IW{DNS_HACKS}
```

:3