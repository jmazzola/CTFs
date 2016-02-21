# misc50 - 'The hidden message'
    My friend really can't remember passwords. 
    So he uses some kind of obfuscation. 
    Can you restore the plaintext?

> Attachment: Download [misc50.zip](./misc50.zip)

After opening up misc50.zip we're greeted with these series of 'obfuscated' numbers.

```
0000000 126 062 126 163 142 103 102 153 142 062 065 154 111 121 157 113
0000020 122 155 170 150 132 172 157 147 123 126 144 067 124 152 102 146
0000040 115 107 065 154 130 062 116 150 142 154 071 172 144 104 102 167
0000060 130 063 153 167 144 130 060 113 012
0000071
```

Right off the bat I notice that the number format is in threes, most of the them start with zeroes. I guess that it's just octal.

We can confirm this by seeing how the columns are going by 020. 020 in decimal is 16. There IS 16 numbers vertically each row.

So we throw this into a [octal to ascii converter](http://www.unit-conversion.info/texttools/octal/) and get this:

```
V2VsbCBkb25lIQoKRmxhZzogSVd7TjBfMG5lX2Nhbl9zdDBwX3kwdX0K
```

Well this is most likely a Base64-encoded string with no padding (= signs).

So let's just take the risk and [try it](http://www.freeformatter.com/base64-encoder.html)

And we get our flag :3

```
Well done!

Flag: IW{N0_0ne_can_st0p_y0u}
```

