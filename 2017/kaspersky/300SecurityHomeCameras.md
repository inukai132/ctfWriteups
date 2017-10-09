# Security Home Cameras Write-up
---

### Initial Exposure

The first step is to download the encoded png file `secret_encrypted.png`. Notice that the file doesn't open in any image viewer, which menas that the header has been changed. Open the png in a hex editor and look at the first few bytes.   

The first 8 bytes are  
`76 AF B1 B8 F2 F5 E5 F5`

### PNG Basics

Here it helps to know a little bit about how png files work. [See this webpage for more info.](https://www.w3.org/TR/PNG-Structure.html) According to the spec all png files should start with:  
`89 50 4E 47 0D 0A 1A 0A`  

Now we can compare the bytes to get some clues as to how the file is encrypted.

### Breaking the Encryption

`76 AF B1 B8 F2 F5 E5 F5` | Encrypted Header  
`89 50 4E 47 0D 0A 1A 0A` | Real Header

Notice a few things, `0` in the real header always turns into `F` in the encrypted header. Similarly `A` always maps to `5` and `4` to `B`. If we convert these nibbles into binary and compare them the relationship becomes obvious.

0000|0001|0010|0100|0101|0110|0111|1000|1010|1011|1101|1110|Encoded
----|----|----|----|----|----|----|----|----|----|----|----|-
1111|1110|1101|1011|1010|1001|1000|0111|0101|0100|0010|0001|Decoded

Every bit is inverted. Now we just have to write a small Python script to flip all of the bits of the program.

```python
#!/usr/bin/python2

infile = open("secret_encrypted.png","rb")
outfile = open("decrypted.png","wb")
byte = infile.read(1)
while byte:
    byte = ~ord(byte)&0xFF
    outfile.write(chr(byte))
    byte = infile.read(1)
infile.close()
outfile.close()
```

This gives us the following image with the flag.
![](./img/300SecurityCamera-1.png)


###### Tylor Childers