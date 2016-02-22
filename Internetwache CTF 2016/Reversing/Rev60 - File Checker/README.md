# rev60 - 'File Checker'

>   My friend sent me this file. He told that if I manage to reverse it, I'll
>   have access to all his devices. My misfortune that I don't know anything
>   about reversing :/

>   Attachment: [rev60.zip](./rev60.zip)

Once we open up [rev60.zip](./rev60.zip) we notice that we get a file called 'filechecker'. No extension. 

So I threw it into IDA and noticed that it's a 64-bit ELF file.

Looking around I found a 'main' function. And what do you know, it's literally a file checker.

Pressing F5 to decompile this function makes it a hell of a lot easier to see what is going on.

![Decompiled](http://i.imgur.com/SSk7BNp.png)

That **sub_400760** is just a check to see if the file of **.password** exists.Returns 1 if it does, 0 if it doesn't, whatever.

So on to the actual flow of the program:
---
* First we see if the stream is valid, if it is we do a for loop 15 times and make a temp variable.

* Next we grab each character in the .password file with **fgetc**
* Check if we're at the end of the file with **feof**
    * If we are, we take the temp variable and or it with **0x1337** and break out of the loop.

* Do something with the current character we have and the current loop counter with the function **sub_40079C**
* and or the temp variable with the character read in.

* If our temp variable is less than or equal to 0 we win. If not, we lose.

Aight. So what does that function **sub_40079C** do?

![sub_40079c](http://i.imgur.com/TxuivEy.png)

Oh wow. It's a table! It's the table[i] + character and mod'ing with 0x1337 and using the character we took in from the file as an output paramater.

So let's make a little C++ snippet to set this up.

```c
void func(int i, int* v3)
{
    int array[15] =
    {
        0x12EE, 0x12E0, 0x12BC, 0x12F1, 0x12EE,
        0x12EB, 0x12F2, 0x12D8, 0x12F4, 0x12EF,
        0x12D2, 0x12F4, 0x12EC, 0x12D6, 0x12BA,
    };

    *v3 = (array[i] + *v3) % 0x1337;
}

int main(int argc, char *argv[], char *envp[])
{
    FILE* stream = fopen("password.txt", "r");
    if (stream)
    {
        int v7 = 0;

        for (int i = 0; i < 15; i++)
        {
            int v3 = fgetc(stream);

            if (feof(stream))
            {
                v7 |= 0x1337;
                break;
            }

            func(i, &v3);
            v7 |= v3;
        }

        if (v7 <= 0)
        {
            fclose(stream);
            puts("Congrats!\n");
        }
        else
        {
            puts("Error: Wrong characters\n");
        }
    }
    else
    {
        printf("Fatal error: File does not exist\n");
    }

    system("pause");
}
```

Since the flag has to start with **IW{** let's write that in our password.txt (Windows doesn't allow empty names with extensions)

Stepping through I noticed that temp variable of v7 is **0**. 

Easily I realized that we need to make a little algorithm to solve for every character with the corresponding table:

```csharp
/*
    0x12EE = I  //0x49  0 = (0x12EE + 0x49) % 0x1337;  // character = (0x1337 - array[i]) % 0x1337

    0x12E0 = W  //0x57  0 = (0x12E0 + 0x57) % 0x1337
*/

int[] array =
{
    0x12EE, 0x12E0, 0x12BC, 0x12F1, 0x12EE,
    0x12EB, 0x12F2, 0x12D8, 0x12F4, 0x12EF,
    0x12D2, 0x12F4, 0x12EC, 0x12D6, 0x12BA,
};

for(int i = 0; i < 15; i++)
{
    int solved = (0x1337 - array[i]) % 0x1337;
    Console.Write((char)solved);
}
```

Running that will give us the flag: 

```
IW{FILE_CHeCKa}
```

:3

