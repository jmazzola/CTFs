# rev70 - 'ServerfARM'

>   Someone handed me this and told me that to pass the exam, I have to 
>   extract a secret string. I know cheating is bad, but once does not count. 
>   So are you willing to help me?

>   Attachment: [rev70.zip](./rev70.zip)

Once we open up [rev70.zip](./rev70.zip) we'll see there's a file called **serverfarm** with no extension.

Pop that into IDA and we get that it's an ARM ELF file. Well in my current position, whether it be too lazy or incapable, I won't be able to run this ARM ELF file on this machine. So let's look around.

There's a little segment that allows for input (**scanf**) to be passed into a function called **handle_task**

![mainfunc](http://i.imgur.com/gFtvvtV.png)

Let's check out that function.

It's a switch-case woo!~

![switchcasespaghetti](http://i.imgur.com/gtEcxbY.png)

Now unfortunately I'm not rich so I don't have the newest version to use it's ARM64 Decompiler. Luckily there's an[ awesome website that can do decompilation with all the important/well-known processors for FREE :D](https://retdec.com/decompilation/) (I like free o3o)

Running that through gives us some awesome pictures and psuedocode.

This one being the most interesting, the layout of handle_task:

![retdec](http://i.imgur.com/IW9JIlt.png)

Well I was right when I said it was a switch-case statement. So first we do the first 'block' notified by the string saying so: **Here's your 1. block**

We have:

```c
printf("%s", "IW{");    // Start of flag: "IW{"
putchar(83);            // putchar writes a character to stdout: 83 = 'S'
printf("%c%c\n", 46, 69); // printf of two characters: 46, 69 = '.E'
```

Onto the next task and block by case 1:
via execution flow
```c
printf("%s", ".R.");         // write ".R."
putchar(86);                 // write 'V'
printf("%c%c\n", 46, 69);    // write ".E"
```

The next task/block by case 2:

```c
puts(".R>=F:");             // Write ".R>=F:"
```

And the final one lol:

```c
printf("%c%s%c\n", 65, ":R:M", 125);    // Write "A:R:M}"
```

And there's our flag

```
IW{S.E.R.V.E.R>=F:A:R:M}
```

:3