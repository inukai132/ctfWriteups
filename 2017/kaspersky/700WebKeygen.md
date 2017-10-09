# Web Keygen Write-up
---

### Initial Exposure

The first thing I did was download the html file as `webKeygen.html` so I could look at the source code locally. Looking at the Javascript I noticed that many of the variables and function names were replaced with strings containing the word `Co` repeated different numbers of times. This effectively obfuscates the code, so we will have to clean that up bfeore we can do any code analysis. Pressing the button calls onSubmit() which calls getFlag(). It looks like pressing submit creates an aleart with either `Failed!` with the wrong password or, presumably, the flag with the right password. Then I tried passwords of verying lengths and discovered that alerts don't generate unless the input is 45 characters or less. The input looked like it could be any ASCII character. This was about all of the information I could get without cleaning up the code. 

### Deobfuscation

The names of variables and functions had been replaced with different repititions of `Co`. To start with I ran the following python script to try and clean it up so that, for example, `CoCoCoCoCoCo` would be replaced with `x6x`. This will make it much easier to quickly scan the document. 

```python
#!/usr/bin/python2
f = open("webKeygen.html","r")
fileData = f.read()
f.close()
f = open("webKeygen.html","w")
r = list(range(100))
r.reverse()
for i in r:
	co = "Co"*i
    xx = "x",i,"x"
	fileData = fileData.replace(co,xx)
f.write(fileData)
f.close()
```

This created the following file. It's much easier to read to understand what's happening. 

```javascript
class x1x
{
    constructor(x2x)
    {
        this.x3x = x2x.x3x;
        this.x4x = x2x.x5x();        
        this.x6x = x2x.x5x();        
        this.x7x = x2x.x5x();
    }
    
    
    
    x8x()
    {
        switch( this.x6x )
        {
            case 1:
                return this.x3x.getUint8(this.x4x)*this.x7x;
            case 2:
                return this.x3x.getUint16(this.x4x)*this.x7x;
            case 4:
                return this.x3x.getUint32(this.x4x)*this.x7x;
        }     
    }
    
    x9x(x10x)
    {        
        switch( this.x6x )
        {
            case 1:
               this.x3x.setUint8(this.x4x,x10x);
               break;
            case 2:
               this.x3x.setUint16(this.x4x,x10x);
               break;
            case 4:
               this.x3x.setUint32(this.x4x,x10x);
               break;
        }
        this.x7x = 1;
    }    
}

class x11x
{
    constructor(x2x)
    {
        this.x10x = x2x.x12x();
    }
    

    x8x()
    {
        return this.x10x;       
    }
    
    x9x(x10x)
    {
    }
}

class x13x
{
    constructor(x2x)
    {      
        this.x4x = 0;
        
        var x14x = x2x.x5x(); 
        
        while( x14x-- )
        {
            this.x4x += x2x.x15x().x8x();            
        }
        
        this.x4x &= 0xFFFFFFFF;
        
        this.x16x = x2x.x16x;
        this.x3x = x2x.x3x;
    }
}

class x17x extends x13x
{


    x8x()
    {          
        return this.x16x.getUint8(this.x4x);
    }
    
    x9x(x10x)
    {
        this.x16x.setUint8(this.x4x,x10x);
    }        
}

class x18x extends x13x
{



    x8x()
    {          
        return this.x16x.getUint32(this.x4x);
    }
    
    x9x(x10x)
    {
        this.x16x.setUint32(this.x4x,x10x);
    }        
}

class x19x
{
    constructor(x20x)
    {
        this.x16x = new DataView(x20x.buffer);
        this.x21x = 0;
        this.x3x = new DataView(new ArrayBuffer(32));
        var x22x = x20x.length;
        this.x3x.setInt32(16,x22x);
        
        this.x23x = false;
        this.x24x = false;        
    }
    
    
    x12x()
    {
        var x10x = this.x16x.getUint32(this.x21x);
        this.x21x += 4;
        return x10x;        
    }
    
    x5x()
    {
        var x10x = this.x16x.getUint8(this.x21x);
        this.x21x++;
        return x10x;
    }
    
    x15x()
    {
        var x25x = this.x5x();
        switch(x25x )
        {
            case 1:
                return new x1x(this);                
            case 2:
                return new x11x(this);
            case 3:
                return new x17x(this);
            case 4:
                return new x18x(this);
        }
        return null;
    }
    
    
    x26x(x10x)
    {
        var x22x = this.x3x.getInt32(16) - 4;        
        this.x3x.setInt32(16,x22x);
        this.x16x.setUint32(x22x,x10x)
        return x22x;
    }
    
    x27x()
    {
        var x22x = this.x3x.getInt32(16);
        var x10x = this.x16x.getUint32(x22x);
        x22x += 4;
        this.x3x.setInt32(16,x22x);        
        return x10x;
    }


    x28x()
    {
        var x29x = this.x5x();
        switch(x29x)
        {
            case 4:
                var x30x = this.x15x();                
                var x31x = x30x.x8x();
                
                this.x26x(this.x21x);
                this.x21x = x31x;
                break;
            case 2:
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                x30x.x9x(x32x.x8x());
                break;
            case 8:
                var x30x = this.x15x();                
                
                this.x21x = x30x.x8x();
                break;
            case 10:            
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                x30x.x9x(x32x.x8x()>>>0);
                                
                break;
            case 5:
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                var x31x = x30x.x8x() + x32x.x8x();
                                
                this.x24x = new Boolean(x31x>>32);

                x30x.x9x(x31x);
                break;
            case 18:
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                var x31x = x30x.x8x() >>> x32x.x8x();
                x30x.x9x(x31x);     
                break;
            case 11:
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                var x31x = x30x.x8x() ^ x32x.x8x();
                x30x.x9x(x31x);             
                break;
            case 3:        
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                var x31x = x30x.x8x() + ((~x32x.x8x()) + 1);
                this.x24x = new Boolean(x31x>>32);
                    
                x30x.x9x(x31x);
                break;
            case 15:
                var x30x = this.x15x();
                var x31x = this.x27x();
                
                this.x21x = x31x;
                break;
            case 14:
                var x30x = this.x15x();
                
                var x31x = this.x27x();                                
                x30x.x9x(x31x);
                break;
            case 17:
                var x30x = this.x15x();
                var x32x = this.x15x();
                
                var x31x = x30x.x8x() & x32x.x8x();                
                x30x.x9x(x31x);
                break;
            case 9:        
                var x30x = this.x15x();     

                
                if(this.x24x == false)                
                {
                    this.x21x = x30x.x8x();    
                }                
                break;
            case 1:
                var x30x = this.x15x();                
                
                this.x26x(x30x.x8x());
                break;
            case 6:
                var x30x = this.x15x();
                var x32x = this.x15x();                
                
                this.x23x = (x30x.x8x() == x32x.x8x());
                
                var x31x = x30x.x8x() + ((~x32x.x8x()) + 1);
                this.x24x = new Boolean(x31x>>32);
                break;
            case 7:                                
                var x30x = this.x15x();  
                
                if(this.x23x == false)                
                {
                    this.x21x = x30x.x8x();    
                }
                break;
            case 13:
                var x30x = this.x15x();
                var x32x = this.x15x();
                
                var x31x = x30x.x8x() | x32x.x8x();                
                x30x.x9x(x31x);
                break;
            case 16:                
                var x30x = this.x15x();
                
                if(this.x23x == true)          
                {
                    this.x21x = x30x.x8x();    
                }            
                break;
            case 12:
                var x30x = this.x15x();
                
                var x10x = (this.x3x.getInt32(8) << 32) | this.x3x.getInt32(0);
                var x22x = x10x / x30x.x8x();
                this.x3x.setInt32(0,x22x);
                var x22x = x10x % x30x.x8x();
                this.x3x.setInt32(8,x22x);                
                break;
        }
    }
}

function GetFlag(x33x)
{
    var x20x = new Uint8Array(/*Removed to reduce length*/);
    var x2x = new x19x(x20x);
    var x22x = x2x.x3x.getInt32(16) - 38;        
    x2x.x3x.setInt32(16,x22x);
    var x34x = x22x;
    var x22x = x2x.x3x.getInt32(16) - 8;        
    x2x.x3x.setInt32(16,x22x);
    var x35x = x22x;

    x20x.set(new TextEncoder("utf-8").encode(x33x),x35x);

    x2x.x26x(x34x);
    x2x.x26x(x35x);
    x2x.x26x(0xFFFFFFFF);

    try
    {
        while(x2x.x21x != 0xFFFFFFFF)
        {
            x2x.x28x();
        }
    }
    catch(e)
    {    
    }

    if ( x2x.x3x.getInt32(0) >= 0 )
        return new TextDecoder("utf-8").decode(x20x.subarray(x34x,x34x + 37));
    
    return "Failed";
}


function OnSubmit()
{
    alert(GetFlag(document.getElementById("password").value));
    return False;    
}
```

### Code Analysis

Analysing the code, we can see that there's a large switch statement in `x19x.x28x()` that performs different functions based on the return value of `x19x.x5x()`. That function gets its value by looking in an array of 8 bit numbers named `x16x` and getting the `x21x`th one, then incrementing `x21x`. Looking to see where `x16x` gets created takes us to the constructor. `x16x` is a DataView for an array passed into the constructor. Looking in `GetFlag()` we see that the array that is passed in is the large 8-bit array. 

So we have an object that looks at a number in an array and performs some task depending on that number. Then the object looks at the next number in the list, and continues on until it reaches 0xFFFFFFFF. What's more is that the list of numbers has a large amount of 0's at the end. Putting this together, my hypothesis was that the `x28x()` function is acting as a CPU, with the array as the memory (The 0's at the end are acting as a stack) and `x21x` as the program counter. At the end it checks `x2x.x3x.getInt32(0)` to see if it's positive and if it is it returns the data in `x20x` from `x34x` to `x34x+37` which is probably the location of the flag in memory. 

If you're following along, I would highly recommend Find and Replacing the `xXXx` strings with more descriptive names. Here is my renamed file.

```javascript
class Register
{
    constructor(arrayReader)
    {
        this.registers = arrayReader.registers;
        this.location = arrayReader.getInt8FromHeap();        
        this.byteLength = arrayReader.getInt8FromHeap();        
        this.multiplier = arrayReader.getInt8FromHeap();
    }
    
    
    
    get()
    {
        switch( this.byteLength )
        {
            case 1:
                return this.registers.getUint8(this.location)*this.multiplier;
            case 2:
                return this.registers.getUint16(this.location)*this.multiplier;
            case 4:
                return this.registers.getUint32(this.location)*this.multiplier;
        }     
    }
    
    set(val)
    {        
        switch( this.byteLength )
        {
            case 1:
               this.registers.setUint8(this.location,val);
               break;
            case 2:
               this.registers.setUint16(this.location,val);
               break;
            case 4:
               this.registers.setUint32(this.location,val);
               break;
        }
        this.multiplier = 1;
    }    
}

class Immediate
{
    constructor(arrayReader)
    {
        this.val = arrayReader.getInt32FromHeap();
    }
    

    get()
    {
        return this.val;       
    }
    
    set(val)
    {
    }
}

class Pointer
{
    constructor(arrayReader)
    {      
        this.location = 0;
        
        var valcount = arrayReader.getInt8FromHeap(); 
        //Sums up the next valcount numbers and makes it 32 bits. This is now a Register to the real data
        while( valcount-- )
        {
            this.location += arrayReader.tinySwitch().get();            
        }
        
        this.location &= 0xFFFFFFFF;
        
        this.bytearrayView = arrayReader.bytearrayView;
        this.registers = arrayReader.registers;
    }
}

class int8Pointer extends Pointer
{


    get()
    {          
        return this.bytearrayView.getUint8(this.location);
    }
    
    set(val)
    {
        this.bytearrayView.setUint8(this.location,val);
    }        
}

class int32Pointer extends Pointer
{



    get()
    {          
        return this.bytearrayView.getUint32(this.location);
    }
    
    set(val)
    {
        this.bytearrayView.setUint32(this.location,val);
    }        
}

class ArrayReader
{
    constructor(bytearray)
    {
        this.bytearrayView = new DataView(bytearray.buffer);
        this.programCounter = 0;
        this.registers = new DataView(new ArrayBuffer(32));//Set to empty 32 length array
        var arraySize = bytearray.length;
        this.registers.setInt32(16,arraySize);//Set registers[16] to size of array
        
        this.isEqual = false;
        this.isZero = false;        
    }
    
    
    getInt32FromHeap()
    {
        var val = this.bytearrayView.getUint32(this.programCounter);
        this.programCounter += 4;
        return val;        
    }
    
    getInt8FromHeap()
    {
        var val = this.bytearrayView.getUint8(this.programCounter);
        this.programCounter++;
        return val;
    }
    
    tinySwitch()
    {
        var byte = this.getInt8FromHeap();
        switch(byte )
        {
            case 1:
                return new Register(this);                
            case 2:
                return new Immediate(this);
            case 3:
                return new int8Pointer(this);
            case 4:
                return new int32Pointer(this);
        }
        return null;
    }
    
    
    pushInt32ToStack(val)
    {
        var temp = this.registers.getInt32(16) - 4;        
        this.registers.setInt32(16,temp);
        this.bytearrayView.setUint32(temp,val)
        return temp;
    }
    
    popFromStack()
    {
        var temp = this.registers.getInt32(16);
        var val = this.bytearrayView.getUint32(temp);
        temp += 4;
        this.registers.setInt32(16,temp);        
        return val;
    }


    execute()
    {
        var switchVal = this.getInt8FromHeap();
        switch(switchVal)
        {
            case 4:
                var dataGrabber = this.tinySwitch();                
                var dataGrabberVal = dataGrabber.get();
                
                this.pushInt32ToStack(this.programCounter);
                this.programCounter = dataGrabberVal;
                break;
            case 2:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                dataGrabber.set(dataGrabber2.get());
                break;
            case 8:
                var dataGrabber = this.tinySwitch();                
                
                this.programCounter = dataGrabber.get();
                break;
            case 10:            
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                dataGrabber.set(dataGrabber2.get()>>>0);
                                
                break;
            case 5:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                var dataGrabberVal = dataGrabber.get() + dataGrabber2.get();
                                
                this.isZero = new Boolean(dataGrabberVal>>32);

                dataGrabber.set(dataGrabberVal);
                break;
            case 18:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                var dataGrabberVal = dataGrabber.get() >>> dataGrabber2.get();
                dataGrabber.set(dataGrabberVal);     
                break;
            case 11:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                var dataGrabberVal = dataGrabber.get() ^ dataGrabber2.get();
                dataGrabber.set(dataGrabberVal);             
                break;
            case 3:        
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                var dataGrabberVal = dataGrabber.get() + ((~dataGrabber2.get()) + 1);
                this.isZero = new Boolean(dataGrabberVal>>32);
                    
                dataGrabber.set(dataGrabberVal);
                break;
            case 15:
                var dataGrabber = this.tinySwitch();
                var dataGrabberVal = this.popFromStack();
                
                this.programCounter = dataGrabberVal;
                break;
            case 14:
                var dataGrabber = this.tinySwitch();
                
                var dataGrabberVal = this.popFromStack();                                
                dataGrabber.set(dataGrabberVal);
                break;
            case 17:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();
                
                var dataGrabberVal = dataGrabber.get() & dataGrabber2.get();                
                dataGrabber.set(dataGrabberVal);
                break;
            case 9:        
                var dataGrabber = this.tinySwitch();     

                
                if(this.isZero == false)                
                {
                    this.programCounter = dataGrabber.get();    
                }                
                break;
            case 1:
                var dataGrabber = this.tinySwitch();                
                
                this.pushInt32ToStack(dataGrabber.get());
                break;
            case 6:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();                
                
                this.isEqual = (dataGrabber.get() == dataGrabber2.get());
                
                var dataGrabberVal = dataGrabber.get() + ((~dataGrabber2.get()) + 1);
                this.isZero = new Boolean(dataGrabberVal>>32);
                break;
            case 7:                                
                var dataGrabber = this.tinySwitch();  
                
                if(this.isEqual == false)                
                {
                    this.programCounter = dataGrabber.get();    
                }
                break;
            case 13:
                var dataGrabber = this.tinySwitch();
                var dataGrabber2 = this.tinySwitch();
                
                var dataGrabberVal = dataGrabber.get() | dataGrabber2.get();                
                dataGrabber.set(dataGrabberVal);
                break;
            case 16:                
                var dataGrabber = this.tinySwitch();
                
                if(this.isEqual == true)          
                {
                    this.programCounter = dataGrabber.get();    
                }            
                break;
            case 12:
                var dataGrabber = this.tinySwitch();
                
                var val = (this.registers.getInt32(8) << 32) | this.registers.getInt32(0);
                var temp = val / dataGrabber.get();
                this.registers.setInt32(0,temp);
                var temp = val % dataGrabber.get();
                this.registers.setInt32(8,temp);                
                break;
        }
    }
}

function GetFlag(password)
{
    var bytearray = new Uint8Array(/*Still Removed*/);
    var arrayReader = new ArrayReader(bytearray);
    var temp = arrayReader.registers.getInt32(16) - 38;        
    arrayReader.registers.setInt32(16,temp);
    var posFlag = temp;
    var temp = arrayReader.registers.getInt32(16) - 8;        
    arrayReader.registers.setInt32(16,temp);
    var posPass = temp;

    bytearray.set(new TextEncoder("utf-8").encode(password),posPass);

    arrayReader.pushInt32ToStack(posFlag);
    arrayReader.pushInt32ToStack(posPass);
    arrayReader.pushInt32ToStack(0xFFFFFFFF);

    try
    {
        while(arrayReader.programCounter != 0xFFFFFFFF)
        {
            arrayReader.execute();
        }
    }
    catch(e)
    {    
    }

    if ( arrayReader.registers.getInt32(0) >= 0 )
        return new TextDecoder("utf-8").decode(bytearray.subarray(posFlag,posFlag + 37));
    
    return "Failed";
}

function OnSubmit()
{
    alert(GetFlag(document.getElementById("password").value));
    return False;    
}
```

Here we can see why the maximum length of the imput field was 45. Anything longer and the string would be out of bounds of the memory. The last 45 bytes of the memory will end up looking like:  `PPPPPPPPFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF` where P is the password (length 8) and F is the flag. The large switch statement, now in the function named `execute()` acts as our instruction decoder. 

There are also 4 data retrieval objects: Register (this also lets the value be multiplied), Immediate, int32 Pointer, int8 Pointer. It's important to note that the pointers allow for multiple values to be added up and the sum to be dereferenced. Here's a reference table for each of the data types with the opcodes.

Type|Opcode|Parameter 1|Parameter 2|Parameter 3
-|-|-|-|-
Register|1|Register Number|Byte Length|Multiplier
Immediate|2|Value|N/A|N/A
*Int32|3|# Of Values to Add|Value to Add|More Values...
*Int8|4|# Of Values to Add|Value to Add|More Values...

### Instruction Decoding

Starting at the top of the switch statement, the opcodes can be determined by seeing what each one does

```javascript
case 4:
    var dataGrabber = this.tinySwitch();                
    var dataGrabberVal = dataGrabber.get();
    
    this.pushInt32ToStack(this.programCounter);
    this.programCounter = dataGrabberVal;
    break;
```
This takes one value, pushed the program counter to the stack and sets the program counter to the value. 
In other words, this is `call`
```javascript
case 2:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    dataGrabber.set(dataGrabber2.get());
    break;
```
This takes two values, and sets one to the value of the other. 
In other words, this is `mov`
```javascript
case 8:
    var dataGrabber = this.tinySwitch();                
    
    this.programCounter = dataGrabber.get();
    break;
```
This takes one value, and sets the program counter to that value without pushing it to the stack. 
In other words, this is `jmp`
```javascript
case 10:            
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    dataGrabber.set(dataGrabber2.get()>>>0);
                    
    break;
```
This takes two values, and sets one to the sign extended value of the other.
In other words, this is `movsx`
```javascript
case 5:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    var dataGrabberVal = dataGrabber.get() + dataGrabber2.get();
                    
    this.isZero = new Boolean(dataGrabberVal>>32);

    dataGrabber.set(dataGrabberVal);
    break;
```
This takes two values, and sets one to itself plus the other.
It also sets the isZero flag as appropriate
In other words, this is `add`
```javascript
case 18:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    var dataGrabberVal = dataGrabber.get() >>> dataGrabber2.get();
    dataGrabber.set(dataGrabberVal);     
    break;
```
This takes two values, and arithmetically shifts one right by the value of the other.
In other words, this is `sar`
```javascript
case 11:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    var dataGrabberVal = dataGrabber.get() ^ dataGrabber2.get();
    dataGrabber.set(dataGrabberVal);             
    break;
```
This takes two values, and sets one to itself xor'd with the other.
In other words, this is `xor`
```javascript
case 3:        
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    var dataGrabberVal = dataGrabber.get() + ((~dataGrabber2.get()) + 1);
    this.isZero = new Boolean(dataGrabberVal>>32);
        
    dataGrabber.set(dataGrabberVal);
    break;
```
This takes two values, and sets one to itself minus the other.
It also sets the isZero flag as appropriate
In other words, this is `sub`
```javascript
case 15:
    var dataGrabber = this.tinySwitch();
    var dataGrabberVal = this.popFromStack();
    
    this.programCounter = dataGrabberVal;
    break;
```
This takes one value, and sets the program counter to a popped number from the stack.
In other words, this is `ret`
```javascript
case 14:
    var dataGrabber = this.tinySwitch();
    
    var dataGrabberVal = this.popFromStack();                                
    dataGrabber.set(dataGrabberVal);
    break;
```
This takes one value, and sets its value to the top of the stack.
In other words, this is `pop`
```javascript
case 17:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();
    
    var dataGrabberVal = dataGrabber.get() & dataGrabber2.get();                
    dataGrabber.set(dataGrabberVal);
    break;
```
This takes two values, and sets one to itself and other.
In other words, this is `and`
```javascript
case 9:        
    var dataGrabber = this.tinySwitch();     

    
    if(this.isZero == false)                
    {
        this.programCounter = dataGrabber.get();    
    }                
    break;
```
This takes one value, and if the zero flag is not set, the program counter is set to the input value.
In other words, this is `brnz`
```javascript
case 1:
    var dataGrabber = this.tinySwitch();                
    
    this.pushInt32ToStack(dataGrabber.get());
    break;
```
This takes one values, and pushes it onto the stack.
In other words, this is `push`
```javascript
case 6:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();                
    
    this.isEqual = (dataGrabber.get() == dataGrabber2.get());
    
    var dataGrabberVal = dataGrabber.get() + ((~dataGrabber2.get()) + 1);
    this.isZero = new Boolean(dataGrabberVal>>32);
    break;
```
This takes two values, and sets isEqual and isZero flags as applicable
In other words, this is `tst`
```javascript
case 7:                                
    var dataGrabber = this.tinySwitch();  
    
    if(this.isEqual == false)                
    {
        this.programCounter = dataGrabber.get();    
    }
    break;
```
This takes one value, and if the equal flag is not set, the program counter is set to the input value.
In other words, this is `brne`
```javascript
case 13:
    var dataGrabber = this.tinySwitch();
    var dataGrabber2 = this.tinySwitch();
    
    var dataGrabberVal = dataGrabber.get() | dataGrabber2.get();                
    dataGrabber.set(dataGrabberVal);
    break;
```
This takes two values, and sets one to itself or other.
In other words, this is `or`
```javascript
case 16:                
    var dataGrabber = this.tinySwitch();
    
    if(this.isEqual == true)          
    {
        this.programCounter = dataGrabber.get();    
    }            
    break;
```
This takes one value, and if the zero flag is not set, the program counter is set to the input value.
In other words, this is `breq`
```javascript
case 12:
    var dataGrabber = this.tinySwitch();
    
    var val = (this.registers.getInt32(8) << 32) | this.registers.getInt32(0);
    var temp = val / dataGrabber.get();
    this.registers.setInt32(0,temp);
    var temp = val % dataGrabber.get();
    this.registers.setInt32(8,temp);                
    break;
```
This takes one value, then divides the value in a 64 bit register `(this.registers.getInt32(8) << 32) | this.registers.getInt32(0)` by that value. The result goes in 0 and the remainder goes in 8. 
In other words, this is `div`

Opcode|Name|#Params
-|-|-
1|push|1
2|mov|2
3|sub|2
4|call|1
5|add|2
6|tst|2
7|brne|1
8|brk|1
9|brnz|1
10|movsx|2
11|xor|2
12|div|1
13|or|2
14|pop|1
15|ret|1
16|breq|1
17|and|2
18|sar|2

### Conversion into Assembly

So now that we know each opcode, how many parameters they consume, and how the data retrieval objects work, we can convert the long byte string into pseudo-assembly. To do this I stored the byte array in ba.txt and wrote a python script to step through the bytes and figure out whe assembly from the bytes. It also adds program counter numbers on the left so we can better synchronise it with the running program. The byte-code that translates into the assembly is on the right

ba.txt
```
1,1,20,4,1,2,1,20,4,1,1,16,4,1,3,1,16,4,1,2,0,0,0,44,1,1,24,4,1,2,3,2,2,255,255,255,212,1,20,4,1,2,0,0,0,115,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,1,2,0,0,0,20,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,2,2,0,0,0,32,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,3,2,0,0,0,23,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,4,2,0,0,0,2,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,5,2,0,0,0,98,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,6,2,0,0,0,42,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,7,2,0,0,0,119,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,8,2,0,0,0,121,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,9,2,0,0,0,29,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,10,2,0,0,0,33,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,11,2,0,0,0,113,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,12,2,0,0,0,112,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,13,2,0,0,0,103,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,14,2,0,0,0,94,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,15,2,0,0,0,6,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,16,2,0,0,0,0,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,17,2,0,0,0,30,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,18,2,0,0,0,91,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,19,2,0,0,0,113,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,20,2,0,0,0,125,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,21,2,0,0,0,103,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,22,2,0,0,0,95,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,23,2,0,0,0,113,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,24,2,0,0,0,123,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,25,2,0,0,0,111,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,26,2,0,0,0,80,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,27,2,0,0,0,2,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,28,2,0,0,0,119,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,29,2,0,0,0,103,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,30,2,0,0,0,90,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,31,2,0,0,0,115,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,32,2,0,0,0,9,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,33,2,0,0,0,25,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,34,2,0,0,0,39,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,35,2,0,0,0,1,2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,36,2,0,0,0,5,1,2,0,0,0,8,2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1,1,1,0,4,1,1,2,18,52,86,120,4,2,0,0,4,199,5,1,16,4,1,2,0,0,0,12,6,1,0,4,1,2,51,229,174,64,7,2,0,0,4,164,2,4,2,2,255,255,255,252,1,20,4,1,2,0,0,0,0,2,4,2,2,255,255,255,252,1,20,4,1,2,0,0,0,0,8,2,0,0,3,217,2,1,4,4,1,4,2,2,255,255,255,252,1,20,4,1,5,1,4,4,1,2,0,0,0,1,2,4,2,2,255,255,255,252,1,20,4,1,1,4,4,1,6,4,2,2,255,255,255,252,1,20,4,1,2,0,0,0,37,9,2,0,0,4,149,2,1,8,4,1,4,2,2,255,255,255,252,1,20,4,1,10,1,4,4,1,3,3,2,255,255,255,212,1,20,4,1,1,8,4,1,2,1,0,4,1,4,2,2,255,255,255,252,1,20,4,1,11,1,8,4,1,1,8,4,1,2,1,24,4,1,2,0,0,0,8,12,1,24,4,1,2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1,10,1,8,4,1,3,2,1,0,4,1,1,8,4,1,11,1,4,4,1,1,8,4,1,2,1,0,4,1,4,2,2,0,0,0,12,1,20,4,1,5,1,0,4,1,4,2,2,255,255,255,252,1,20,4,1,2,3,1,1,0,4,1,1,7,1,1,8,2,0,0,3,175,11,1,0,4,1,1,0,4,1,8,2,0,0,4,174,13,1,0,4,1,2,255,255,255,255,14,1,24,4,1,2,1,16,4,1,1,20,4,1,14,1,20,4,1,15,2,0,0,0,0,1,1,20,4,1,2,1,20,4,1,1,16,4,1,3,1,16,4,1,2,0,0,4,8,2,4,2,2,255,255,251,248,1,20,4,1,2,0,0,0,0,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,4,2,119,7,48,150,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,8,2,238,14,97,44,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,12,2,153,9,81,186,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,16,2,7,109,196,25,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,20,2,112,106,244,143,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,24,2,233,99,165,53,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,28,2,158,100,149,163,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,32,2,14,219,136,50,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,36,2,121,220,184,164,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,40,2,224,213,233,30,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,44,2,151,210,217,136,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,48,2,9,182,76,43,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,52,2,126,177,124,189,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,56,2,231,184,45,7,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,60,2,144,191,29,145,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,64,2,29,183,16,100,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,68,2,106,176,32,242,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,72,2,243,185,113,72,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,76,2,132,190,65,222,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,80,2,26,218,212,125,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,84,2,109,221,228,235,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,88,2,244,212,181,81,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,92,2,131,211,133,199,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,96,2,19,108,152,86,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,100,2,100,107,168,192,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,104,2,253,98,249,122,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,108,2,138,101,201,236,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,112,2,20,1,92,79,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,116,2,99,6,108,217,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,120,2,250,15,61,99,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,124,2,141,8,13,245,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,128,2,59,110,32,200,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,132,2,76,105,16,94,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,136,2,213,96,65,228,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,140,2,162,103,113,114,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,144,2,60,3,228,209,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,148,2,75,4,212,71,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,152,2,210,13,133,253,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,156,2,165,10,181,107,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,160,2,53,181,168,250,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,164,2,66,178,152,108,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,168,2,219,187,201,214,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,172,2,172,188,249,64,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,176,2,50,216,108,227,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,180,2,69,223,92,117,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,184,2,220,214,13,207,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,188,2,171,209,61,89,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,192,2,38,217,48,172,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,196,2,81,222,0,58,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,200,2,200,215,81,128,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,204,2,191,208,97,22,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,208,2,33,180,244,181,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,212,2,86,179,196,35,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,216,2,207,186,149,153,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,220,2,184,189,165,15,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,224,2,40,2,184,158,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,228,2,95,5,136,8,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,232,2,198,12,217,178,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,236,2,177,11,233,36,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,240,2,47,111,124,135,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,244,2,88,104,76,17,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,248,2,193,97,29,171,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,252,2,182,102,45,61,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,0,2,118,220,65,144,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,4,2,1,219,113,6,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,8,2,152,210,32,188,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,12,2,239,213,16,42,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,16,2,113,177,133,137,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,20,2,6,182,181,31,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,24,2,159,191,228,165,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,28,2,232,184,212,51,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,32,2,120,7,201,162,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,36,2,15,0,249,52,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,40,2,150,9,168,142,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,44,2,225,14,152,24,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,48,2,127,106,13,187,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,52,2,8,109,61,45,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,56,2,145,100,108,151,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,60,2,230,99,92,1,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,64,2,107,107,81,244,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,68,2,28,108,97,98,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,72,2,133,101,48,216,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,76,2,242,98,0,78,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,80,2,108,6,149,237,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,84,2,27,1,165,123,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,88,2,130,8,244,193,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,92,2,245,15,196,87,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,96,2,101,176,217,198,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,100,2,18,183,233,80,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,104,2,139,190,184,234,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,108,2,252,185,136,124,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,112,2,98,221,29,223,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,116,2,21,218,45,73,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,120,2,140,211,124,243,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,124,2,251,212,76,101,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,128,2,77,178,97,88,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,132,2,58,181,81,206,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,136,2,163,188,0,116,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,140,2,212,187,48,226,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,144,2,74,223,165,65,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,148,2,61,216,149,215,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,152,2,164,209,196,109,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,156,2,211,214,244,251,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,160,2,67,105,233,106,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,164,2,52,110,217,252,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,168,2,173,103,136,70,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,172,2,218,96,184,208,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,176,2,68,4,45,115,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,180,2,51,3,29,229,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,184,2,170,10,76,95,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,188,2,221,13,124,201,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,192,2,80,5,113,60,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,196,2,39,2,65,170,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,200,2,190,11,16,16,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,204,2,201,12,32,134,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,208,2,87,104,181,37,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,212,2,32,111,133,179,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,216,2,185,102,212,9,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,220,2,206,97,228,159,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,224,2,94,222,249,14,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,228,2,41,217,201,152,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,232,2,176,208,152,34,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,236,2,199,215,168,180,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,240,2,89,179,61,23,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,244,2,46,180,13,129,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,248,2,183,189,92,59,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,252,2,192,186,108,173,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,0,2,237,184,131,32,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,4,2,154,191,179,182,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,8,2,3,182,226,12,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,12,2,116,177,210,154,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,16,2,234,213,71,57,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,20,2,157,210,119,175,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,24,2,4,219,38,21,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,28,2,115,220,22,131,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,32,2,227,99,11,18,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,36,2,148,100,59,132,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,40,2,13,109,106,62,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,44,2,122,106,90,168,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,48,2,228,14,207,11,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,52,2,147,9,255,157,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,56,2,10,0,174,39,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,60,2,125,7,158,177,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,64,2,240,15,147,68,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,68,2,135,8,163,210,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,72,2,30,1,242,104,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,76,2,105,6,194,254,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,80,2,247,98,87,93,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,84,2,128,101,103,203,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,88,2,25,108,54,113,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,92,2,110,107,6,231,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,96,2,254,212,27,118,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,100,2,137,211,43,224,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,104,2,16,218,122,90,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,108,2,103,221,74,204,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,112,2,249,185,223,111,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,116,2,142,190,239,249,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,120,2,23,183,190,67,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,124,2,96,176,142,213,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,128,2,214,214,163,232,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,132,2,161,209,147,126,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,136,2,56,216,194,196,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,140,2,79,223,242,82,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,144,2,209,187,103,241,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,148,2,166,188,87,103,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,152,2,63,181,6,221,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,156,2,72,178,54,75,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,160,2,216,13,43,218,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,164,2,175,10,27,76,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,168,2,54,3,74,246,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,172,2,65,4,122,96,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,176,2,223,96,239,195,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,180,2,168,103,223,85,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,184,2,49,110,142,239,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,188,2,70,105,190,121,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,192,2,203,97,179,140,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,196,2,188,102,131,26,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,200,2,37,111,210,160,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,204,2,82,104,226,54,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,208,2,204,12,119,149,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,212,2,187,11,71,3,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,216,2,34,2,22,185,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,220,2,85,5,38,47,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,224,2,197,186,59,190,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,228,2,178,189,11,40,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,232,2,43,180,90,146,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,236,2,92,179,106,4,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,240,2,194,215,255,167,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,244,2,181,208,207,49,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,248,2,44,217,158,139,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,252,2,91,222,174,29,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,0,2,155,100,194,176,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,4,2,236,99,242,38,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,8,2,117,106,163,156,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,12,2,2,109,147,10,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,16,2,156,9,6,169,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,20,2,235,14,54,63,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,24,2,114,7,103,133,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,28,2,5,0,87,19,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,32,2,149,191,74,130,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,36,2,226,184,122,20,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,40,2,123,177,43,174,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,44,2,12,182,27,56,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,48,2,146,210,142,155,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,52,2,229,213,190,13,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,56,2,124,220,239,183,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,60,2,11,219,223,33,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,64,2,134,211,210,212,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,68,2,241,212,226,66,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,72,2,104,221,179,248,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,76,2,31,218,131,110,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,80,2,129,190,22,205,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,84,2,246,185,38,91,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,88,2,111,176,119,225,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,92,2,24,183,71,119,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,96,2,136,8,90,230,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,100,2,255,15,106,112,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,104,2,102,6,59,202,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,108,2,17,1,11,92,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,112,2,143,101,158,255,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,116,2,248,98,174,105,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,120,2,97,107,255,211,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,124,2,22,108,207,69,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,128,2,160,10,226,120,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,132,2,215,13,210,238,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,136,2,78,4,131,84,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,140,2,57,3,179,194,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,144,2,167,103,38,97,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,148,2,208,96,22,247,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,152,2,73,105,71,77,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,156,2,62,110,119,219,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,160,2,174,209,106,74,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,164,2,217,214,90,220,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,168,2,64,223,11,102,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,172,2,55,216,59,240,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,176,2,169,188,174,83,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,180,2,222,187,158,197,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,184,2,71,178,207,127,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,188,2,48,181,255,233,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,192,2,189,189,242,28,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,196,2,202,186,194,138,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,200,2,83,179,147,48,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,204,2,36,180,163,166,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,208,2,186,208,54,5,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,212,2,205,215,6,147,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,216,2,84,222,87,41,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,220,2,35,217,103,191,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,224,2,179,102,122,46,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,228,2,196,97,74,184,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,232,2,93,104,27,2,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,236,2,42,111,43,148,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,240,2,180,11,190,55,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,244,2,195,12,142,161,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,248,2,90,5,223,27,2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,252,2,45,2,239,141,2,1,0,4,1,4,2,2,0,0,0,12,1,20,4,1,2,4,2,2,255,255,255,252,1,20,4,1,1,0,4,1,2,1,4,4,1,4,2,2,0,0,0,8,1,20,4,1,11,1,4,4,1,2,255,255,255,255,2,4,2,2,0,0,0,8,1,20,4,1,1,4,4,1,2,1,8,4,1,4,2,2,0,0,0,16,1,20,4,1,2,4,2,2,255,255,255,248,1,20,4,1,1,8,4,1,2,1,0,4,1,4,2,2,0,0,0,16,1,20,4,1,3,1,0,4,1,2,0,0,0,1,2,4,2,2,0,0,0,16,1,20,4,1,1,0,4,1,6,4,2,2,255,255,255,248,1,20,4,1,2,0,0,0,0,16,2,0,0,28,40,2,1,4,4,1,4,2,2,255,255,255,252,1,20,4,1,10,1,8,4,1,3,1,1,4,4,1,11,1,8,4,1,4,2,2,0,0,0,8,1,20,4,1,17,1,8,4,1,2,0,0,0,255,2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1,18,1,0,4,1,2,0,0,0,8,11,1,0,4,1,4,3,2,255,255,251,248,1,20,4,1,1,8,4,4,2,4,2,2,0,0,0,8,1,20,4,1,1,0,4,1,2,1,4,4,1,4,2,2,255,255,255,252,1,20,4,1,5,1,4,4,1,2,0,0,0,1,2,4,2,2,255,255,255,252,1,20,4,1,1,4,4,1,8,2,0,0,27,36,2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1,11,1,0,4,1,2,255,255,255,255,2,1,16,4,1,1,20,4,1,14,1,20,4,1,15,2,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0
```

assembler.py
```python
#!/usr/bin/python2
bytecode = [("push",1),("mov",2),("sub",2),("call",1),("add",2),("tst",2),("brne",1),("brk",1),("brnz",1),("movsx",2),("xor",2),("div",1),("or",2),("pop",1),("ret",1),("breq",1),("and",2),("sar",2)]
dataHandlers = [("Register",3),("Immediate32",4),("*Int8",-1),("*Int32",-1)]

f = open("ba.txt","r")
code = f.read()
c = code.split(',')
out = ""
i = 0
byteCnt = 0

def getArgs(type):
	global i, byteCnt
	if type[0] == dataHandlers[1][0]:
		o=0
		nn = type[1]
		while nn:
			nn-=1
			i+=1
			byteCnt +=1
			o+=pow(2,8*nn)*int(c[i])
		if hex(o & 0xFFFFFFFF)[-1] == 'L':
			return hex(o & 0xFFFFFFFF)[:-1]
		else:
			return hex(o & 0xFFFFFFFF)	

	o = type[0]+"("
	nn = type[1]
	if nn == -1:
		i+=1
		byteCnt +=1
		nn = int(c[i])
		while nn:
			i+=1
			byteCnt +=1
			o+=getArgs(dataHandlers[int(c[i])-1])+", "
			nn-=1
	else:
		while nn:
			i+=1
			byteCnt +=1
			o+=c[i]+", "
			nn-=1
	o = o[:-2]+")"
	return o

while i < len(c):
	if c[i]==0:
		i+=1
		continue
	line =  "{0:#06x} ".format(i)
	inst = bytecode[int(c[i])-1]
	i+=1
	byteCnt = 1
	line += inst[0]+" "
	n = inst[1]
	
	while n:
		line += getArgs(dataHandlers[int(c[i])-1])+", "
		n-=1
		i+=1
		byteCnt+=1
	line = line[:-2]
	print line,"\t"*((100-len(line))/4),",".join(c[i-byteCnt:i])
	if i>=7254:
		break
```
Output
```
0x0000 push Register(20, 4, 1) 																	1,1,20,4,1
0x0005 mov Register(20, 4, 1), Register(16, 4, 1) 												2,1,20,4,1,1,16,4,1
0x000e sub Register(16, 4, 1), 0x2c 																3,1,16,4,1,2,0,0,0,44
0x0018 push Register(24, 4, 1) 																	1,1,24,4,1
0x001d mov *Int8(0xffffffd4, Register(20, 4, 1)), 0x73 											2,3,2,2,255,255,255,212,1,20,4,1,2,0,0,0,115
0x002e mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1), 0x14 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,1,2,0,0,0,20
0x0044 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x2), 0x20 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,2,2,0,0,0,32
0x005a mov *Int8(0xffffffd4, Register(20, 4, 1), 0x3), 0x17 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,3,2,0,0,0,23
0x0070 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x4), 0x2 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,4,2,0,0,0,2
0x0086 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x5), 0x62 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,5,2,0,0,0,98
0x009c mov *Int8(0xffffffd4, Register(20, 4, 1), 0x6), 0x2a 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,6,2,0,0,0,42
0x00b2 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x7), 0x77 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,7,2,0,0,0,119
0x00c8 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x8), 0x79 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,8,2,0,0,0,121
0x00de mov *Int8(0xffffffd4, Register(20, 4, 1), 0x9), 0x1d 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,9,2,0,0,0,29
0x00f4 mov *Int8(0xffffffd4, Register(20, 4, 1), 0xa), 0x21 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,10,2,0,0,0,33
0x010a mov *Int8(0xffffffd4, Register(20, 4, 1), 0xb), 0x71 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,11,2,0,0,0,113
0x0120 mov *Int8(0xffffffd4, Register(20, 4, 1), 0xc), 0x70 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,12,2,0,0,0,112
0x0136 mov *Int8(0xffffffd4, Register(20, 4, 1), 0xd), 0x67 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,13,2,0,0,0,103
0x014c mov *Int8(0xffffffd4, Register(20, 4, 1), 0xe), 0x5e 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,14,2,0,0,0,94
0x0162 mov *Int8(0xffffffd4, Register(20, 4, 1), 0xf), 0x6 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,15,2,0,0,0,6
0x0178 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x10), 0x0 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,16,2,0,0,0,0
0x018e mov *Int8(0xffffffd4, Register(20, 4, 1), 0x11), 0x1e 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,17,2,0,0,0,30
0x01a4 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x12), 0x5b 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,18,2,0,0,0,91
0x01ba mov *Int8(0xffffffd4, Register(20, 4, 1), 0x13), 0x71 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,19,2,0,0,0,113
0x01d0 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x14), 0x7d 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,20,2,0,0,0,125
0x01e6 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x15), 0x67 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,21,2,0,0,0,103
0x01fc mov *Int8(0xffffffd4, Register(20, 4, 1), 0x16), 0x5f 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,22,2,0,0,0,95
0x0212 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x17), 0x71 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,23,2,0,0,0,113
0x0228 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x18), 0x7b 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,24,2,0,0,0,123
0x023e mov *Int8(0xffffffd4, Register(20, 4, 1), 0x19), 0x6f 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,25,2,0,0,0,111
0x0254 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1a), 0x50 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,26,2,0,0,0,80
0x026a mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1b), 0x2 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,27,2,0,0,0,2
0x0280 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1c), 0x77 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,28,2,0,0,0,119
0x0296 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1d), 0x67 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,29,2,0,0,0,103
0x02ac mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1e), 0x5a 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,30,2,0,0,0,90
0x02c2 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x1f), 0x73 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,31,2,0,0,0,115
0x02d8 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x20), 0x9 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,32,2,0,0,0,9
0x02ee mov *Int8(0xffffffd4, Register(20, 4, 1), 0x21), 0x19 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,33,2,0,0,0,25
0x0304 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x22), 0x27 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,34,2,0,0,0,39
0x031a mov *Int8(0xffffffd4, Register(20, 4, 1), 0x23), 0x1 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,35,2,0,0,0,1
0x0330 mov *Int8(0xffffffd4, Register(20, 4, 1), 0x24), 0x5 										2,3,3,2,255,255,255,212,1,20,4,1,2,0,0,0,36,2,0,0,0,5
0x0346 push 0x8 																					1,2,0,0,0,8
0x034c mov Register(0, 4, 1), *Int32(0x8, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1
0x035c push Register(0, 4, 1) 																	1,1,0,4,1
0x0361 push 0x12345678 																			1,2,18,52,86,120
0x0367 call 0x4c7 																				4,2,0,0,4,199
0x036d add Register(16, 4, 1), 0xc 																5,1,16,4,1,2,0,0,0,12
0x0377 tst Register(0, 4, 1), 0x33e5ae40 															6,1,0,4,1,2,51,229,174,64
0x0381 brne 0x4a4 																				7,2,0,0,4,164
0x0387 mov *Int32(0xfffffffc, Register(20, 4, 1)), 0x0 											2,4,2,2,255,255,255,252,1,20,4,1,2,0,0,0,0
0x0398 mov *Int32(0xfffffffc, Register(20, 4, 1)), 0x0 											2,4,2,2,255,255,255,252,1,20,4,1,2,0,0,0,0
0x03a9 brk 0x3d9 																					8,2,0,0,3,217
0x03af mov Register(4, 4, 1), *Int32(0xfffffffc, Register(20, 4, 1)) 								2,1,4,4,1,4,2,2,255,255,255,252,1,20,4,1
0x03bf add Register(4, 4, 1), 0x1 																5,1,4,4,1,2,0,0,0,1
0x03c9 mov *Int32(0xfffffffc, Register(20, 4, 1)), Register(4, 4, 1) 								2,4,2,2,255,255,255,252,1,20,4,1,1,4,4,1
0x03d9 tst *Int32(0xfffffffc, Register(20, 4, 1)), 0x25 											6,4,2,2,255,255,255,252,1,20,4,1,2,0,0,0,37
0x03ea brnz 0x495 																				9,2,0,0,4,149
0x03f0 mov Register(8, 4, 1), *Int32(0xfffffffc, Register(20, 4, 1)) 								2,1,8,4,1,4,2,2,255,255,255,252,1,20,4,1
0x0400 movsx Register(4, 4, 1), *Int8(0xffffffd4, Register(20, 4, 1), Register(8, 4, 1)) 			10,1,4,4,1,3,3,2,255,255,255,212,1,20,4,1,1,8,4,1
0x0414 mov Register(0, 4, 1), *Int32(0xfffffffc, Register(20, 4, 1)) 								2,1,0,4,1,4,2,2,255,255,255,252,1,20,4,1
0x0424 xor Register(8, 4, 1), Register(8, 4, 1) 													11,1,8,4,1,1,8,4,1
0x042d mov Register(24, 4, 1), 0x8 																2,1,24,4,1,2,0,0,0,8
0x0437 div Register(24, 4, 1) 																	12,1,24,4,1
0x043c mov Register(0, 4, 1), *Int32(0x8, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1
0x044c movsx Register(8, 4, 1), *Int8(Register(0, 4, 1), Register(8, 4, 1)) 						10,1,8,4,1,3,2,1,0,4,1,1,8,4,1
0x045b xor Register(4, 4, 1), Register(8, 4, 1) 													11,1,4,4,1,1,8,4,1
0x0464 mov Register(0, 4, 1), *Int32(0xc, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,12,1,20,4,1
0x0474 add Register(0, 4, 1), *Int32(0xfffffffc, Register(20, 4, 1)) 								5,1,0,4,1,4,2,2,255,255,255,252,1,20,4,1
0x0484 mov *Int8(Register(0, 4, 1)), Register(7, 1, 1) 											2,3,1,1,0,4,1,1,7,1,1
0x048f brk 0x3af 																					8,2,0,0,3,175
0x0495 xor Register(0, 4, 1), Register(0, 4, 1) 													11,1,0,4,1,1,0,4,1
0x049e brk 0x4ae 																					8,2,0,0,4,174
0x04a4 or Register(0, 4, 1), 0xffffffff 															13,1,0,4,1,2,255,255,255,255
0x04ae pop Register(24, 4, 1) 																	14,1,24,4,1
0x04b3 mov Register(16, 4, 1), Register(20, 4, 1) 												2,1,16,4,1,1,20,4,1
0x04bc pop Register(20, 4, 1) 																	14,1,20,4,1
0x04c1 ret 0x0 																					15,2,0,0,0,0
0x04c7 push Register(20, 4, 1) 																	1,1,20,4,1
0x04cc mov Register(20, 4, 1), Register(16, 4, 1) 												2,1,20,4,1,1,16,4,1
0x04d5 sub Register(16, 4, 1), 0x408 																3,1,16,4,1,2,0,0,4,8
0x04df mov *Int32(0xfffffbf8, Register(20, 4, 1)), 0x0 											2,4,2,2,255,255,251,248,1,20,4,1,2,0,0,0,0
0x04f0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x4), 0x77073096 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,4,2,119,7,48,150
0x0506 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x8), 0xee0e612c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,8,2,238,14,97,44
0x051c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xc), 0x990951ba 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,12,2,153,9,81,186
0x0532 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x10), 0x76dc419 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,16,2,7,109,196,25
0x0548 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x14), 0x706af48f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,20,2,112,106,244,143
0x055e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x18), 0xe963a535 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,24,2,233,99,165,53
0x0574 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1c), 0x9e6495a3 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,28,2,158,100,149,163
0x058a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x20), 0xedb8832 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,32,2,14,219,136,50
0x05a0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x24), 0x79dcb8a4 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,36,2,121,220,184,164
0x05b6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x28), 0xe0d5e91e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,40,2,224,213,233,30
0x05cc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2c), 0x97d2d988 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,44,2,151,210,217,136
0x05e2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x30), 0x9b64c2b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,48,2,9,182,76,43
0x05f8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x34), 0x7eb17cbd 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,52,2,126,177,124,189
0x060e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x38), 0xe7b82d07 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,56,2,231,184,45,7
0x0624 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3c), 0x90bf1d91 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,60,2,144,191,29,145
0x063a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x40), 0x1db71064 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,64,2,29,183,16,100
0x0650 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x44), 0x6ab020f2 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,68,2,106,176,32,242
0x0666 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x48), 0xf3b97148 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,72,2,243,185,113,72
0x067c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x4c), 0x84be41de 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,76,2,132,190,65,222
0x0692 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x50), 0x1adad47d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,80,2,26,218,212,125
0x06a8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x54), 0x6ddde4eb 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,84,2,109,221,228,235
0x06be mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x58), 0xf4d4b551 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,88,2,244,212,181,81
0x06d4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x5c), 0x83d385c7 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,92,2,131,211,133,199
0x06ea mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x60), 0x136c9856 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,96,2,19,108,152,86
0x0700 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x64), 0x646ba8c0 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,100,2,100,107,168,192
0x0716 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x68), 0xfd62f97a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,104,2,253,98,249,122
0x072c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x6c), 0x8a65c9ec 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,108,2,138,101,201,236
0x0742 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x70), 0x14015c4f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,112,2,20,1,92,79
0x0758 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x74), 0x63066cd9 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,116,2,99,6,108,217
0x076e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x78), 0xfa0f3d63 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,120,2,250,15,61,99
0x0784 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x7c), 0x8d080df5 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,124,2,141,8,13,245
0x079a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x80), 0x3b6e20c8 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,128,2,59,110,32,200
0x07b0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x84), 0x4c69105e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,132,2,76,105,16,94
0x07c6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x88), 0xd56041e4 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,136,2,213,96,65,228
0x07dc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x8c), 0xa2677172 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,140,2,162,103,113,114
0x07f2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x90), 0x3c03e4d1 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,144,2,60,3,228,209
0x0808 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x94), 0x4b04d447 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,148,2,75,4,212,71
0x081e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x98), 0xd20d85fd 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,152,2,210,13,133,253
0x0834 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x9c), 0xa50ab56b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,156,2,165,10,181,107
0x084a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xa0), 0x35b5a8fa 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,160,2,53,181,168,250
0x0860 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xa4), 0x42b2986c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,164,2,66,178,152,108
0x0876 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xa8), 0xdbbbc9d6 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,168,2,219,187,201,214
0x088c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xac), 0xacbcf940 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,172,2,172,188,249,64
0x08a2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xb0), 0x32d86ce3 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,176,2,50,216,108,227
0x08b8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xb4), 0x45df5c75 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,180,2,69,223,92,117
0x08ce mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xb8), 0xdcd60dcf 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,184,2,220,214,13,207
0x08e4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xbc), 0xabd13d59 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,188,2,171,209,61,89
0x08fa mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xc0), 0x26d930ac 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,192,2,38,217,48,172
0x0910 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xc4), 0x51de003a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,196,2,81,222,0,58
0x0926 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xc8), 0xc8d75180 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,200,2,200,215,81,128
0x093c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xcc), 0xbfd06116 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,204,2,191,208,97,22
0x0952 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xd0), 0x21b4f4b5 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,208,2,33,180,244,181
0x0968 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xd4), 0x56b3c423 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,212,2,86,179,196,35
0x097e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xd8), 0xcfba9599 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,216,2,207,186,149,153
0x0994 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xdc), 0xb8bda50f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,220,2,184,189,165,15
0x09aa mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xe0), 0x2802b89e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,224,2,40,2,184,158
0x09c0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xe4), 0x5f058808 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,228,2,95,5,136,8
0x09d6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xe8), 0xc60cd9b2 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,232,2,198,12,217,178
0x09ec mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xec), 0xb10be924 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,236,2,177,11,233,36
0x0a02 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xf0), 0x2f6f7c87 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,240,2,47,111,124,135
0x0a18 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xf4), 0x58684c11 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,244,2,88,104,76,17
0x0a2e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xf8), 0xc1611dab 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,248,2,193,97,29,171
0x0a44 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0xfc), 0xb6662d3d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,0,252,2,182,102,45,61
0x0a5a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x100), 0x76dc4190 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,0,2,118,220,65,144
0x0a70 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x104), 0x1db7106 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,4,2,1,219,113,6
0x0a86 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x108), 0x98d220bc 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,8,2,152,210,32,188
0x0a9c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x10c), 0xefd5102a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,12,2,239,213,16,42
0x0ab2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x110), 0x71b18589 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,16,2,113,177,133,137
0x0ac8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x114), 0x6b6b51f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,20,2,6,182,181,31
0x0ade mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x118), 0x9fbfe4a5 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,24,2,159,191,228,165
0x0af4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x11c), 0xe8b8d433 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,28,2,232,184,212,51
0x0b0a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x120), 0x7807c9a2 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,32,2,120,7,201,162
0x0b20 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x124), 0xf00f934 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,36,2,15,0,249,52
0x0b36 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x128), 0x9609a88e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,40,2,150,9,168,142
0x0b4c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x12c), 0xe10e9818 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,44,2,225,14,152,24
0x0b62 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x130), 0x7f6a0dbb 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,48,2,127,106,13,187
0x0b78 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x134), 0x86d3d2d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,52,2,8,109,61,45
0x0b8e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x138), 0x91646c97 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,56,2,145,100,108,151
0x0ba4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x13c), 0xe6635c01 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,60,2,230,99,92,1
0x0bba mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x140), 0x6b6b51f4 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,64,2,107,107,81,244
0x0bd0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x144), 0x1c6c6162 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,68,2,28,108,97,98
0x0be6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x148), 0x856530d8 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,72,2,133,101,48,216
0x0bfc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x14c), 0xf262004e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,76,2,242,98,0,78
0x0c12 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x150), 0x6c0695ed 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,80,2,108,6,149,237
0x0c28 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x154), 0x1b01a57b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,84,2,27,1,165,123
0x0c3e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x158), 0x8208f4c1 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,88,2,130,8,244,193
0x0c54 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x15c), 0xf50fc457 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,92,2,245,15,196,87
0x0c6a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x160), 0x65b0d9c6 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,96,2,101,176,217,198
0x0c80 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x164), 0x12b7e950 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,100,2,18,183,233,80
0x0c96 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x168), 0x8bbeb8ea 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,104,2,139,190,184,234
0x0cac mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x16c), 0xfcb9887c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,108,2,252,185,136,124
0x0cc2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x170), 0x62dd1ddf 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,112,2,98,221,29,223
0x0cd8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x174), 0x15da2d49 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,116,2,21,218,45,73
0x0cee mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x178), 0x8cd37cf3 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,120,2,140,211,124,243
0x0d04 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x17c), 0xfbd44c65 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,124,2,251,212,76,101
0x0d1a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x180), 0x4db26158 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,128,2,77,178,97,88
0x0d30 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x184), 0x3ab551ce 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,132,2,58,181,81,206
0x0d46 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x188), 0xa3bc0074 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,136,2,163,188,0,116
0x0d5c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x18c), 0xd4bb30e2 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,140,2,212,187,48,226
0x0d72 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x190), 0x4adfa541 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,144,2,74,223,165,65
0x0d88 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x194), 0x3dd895d7 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,148,2,61,216,149,215
0x0d9e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x198), 0xa4d1c46d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,152,2,164,209,196,109
0x0db4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x19c), 0xd3d6f4fb 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,156,2,211,214,244,251
0x0dca mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1a0), 0x4369e96a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,160,2,67,105,233,106
0x0de0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1a4), 0x346ed9fc 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,164,2,52,110,217,252
0x0df6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1a8), 0xad678846 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,168,2,173,103,136,70
0x0e0c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1ac), 0xda60b8d0 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,172,2,218,96,184,208
0x0e22 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1b0), 0x44042d73 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,176,2,68,4,45,115
0x0e38 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1b4), 0x33031de5 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,180,2,51,3,29,229
0x0e4e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1b8), 0xaa0a4c5f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,184,2,170,10,76,95
0x0e64 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1bc), 0xdd0d7cc9 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,188,2,221,13,124,201
0x0e7a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1c0), 0x5005713c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,192,2,80,5,113,60
0x0e90 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1c4), 0x270241aa 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,196,2,39,2,65,170
0x0ea6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1c8), 0xbe0b1010 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,200,2,190,11,16,16
0x0ebc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1cc), 0xc90c2086 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,204,2,201,12,32,134
0x0ed2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1d0), 0x5768b525 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,208,2,87,104,181,37
0x0ee8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1d4), 0x206f85b3 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,212,2,32,111,133,179
0x0efe mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1d8), 0xb966d409 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,216,2,185,102,212,9
0x0f14 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1dc), 0xce61e49f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,220,2,206,97,228,159
0x0f2a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1e0), 0x5edef90e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,224,2,94,222,249,14
0x0f40 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1e4), 0x29d9c998 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,228,2,41,217,201,152
0x0f56 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1e8), 0xb0d09822 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,232,2,176,208,152,34
0x0f6c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1ec), 0xc7d7a8b4 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,236,2,199,215,168,180
0x0f82 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1f0), 0x59b33d17 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,240,2,89,179,61,23
0x0f98 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1f4), 0x2eb40d81 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,244,2,46,180,13,129
0x0fae mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1f8), 0xb7bd5c3b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,248,2,183,189,92,59
0x0fc4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x1fc), 0xc0ba6cad 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,1,252,2,192,186,108,173
0x0fda mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x200), 0xedb88320 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,0,2,237,184,131,32
0x0ff0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x204), 0x9abfb3b6 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,4,2,154,191,179,182
0x1006 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x208), 0x3b6e20c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,8,2,3,182,226,12
0x101c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x20c), 0x74b1d29a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,12,2,116,177,210,154
0x1032 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x210), 0xead54739 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,16,2,234,213,71,57
0x1048 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x214), 0x9dd277af 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,20,2,157,210,119,175
0x105e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x218), 0x4db2615 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,24,2,4,219,38,21
0x1074 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x21c), 0x73dc1683 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,28,2,115,220,22,131
0x108a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x220), 0xe3630b12 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,32,2,227,99,11,18
0x10a0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x224), 0x94643b84 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,36,2,148,100,59,132
0x10b6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x228), 0xd6d6a3e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,40,2,13,109,106,62
0x10cc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x22c), 0x7a6a5aa8 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,44,2,122,106,90,168
0x10e2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x230), 0xe40ecf0b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,48,2,228,14,207,11
0x10f8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x234), 0x9309ff9d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,52,2,147,9,255,157
0x110e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x238), 0xa00ae27 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,56,2,10,0,174,39
0x1124 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x23c), 0x7d079eb1 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,60,2,125,7,158,177
0x113a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x240), 0xf00f9344 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,64,2,240,15,147,68
0x1150 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x244), 0x8708a3d2 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,68,2,135,8,163,210
0x1166 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x248), 0x1e01f268 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,72,2,30,1,242,104
0x117c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x24c), 0x6906c2fe 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,76,2,105,6,194,254
0x1192 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x250), 0xf762575d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,80,2,247,98,87,93
0x11a8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x254), 0x806567cb 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,84,2,128,101,103,203
0x11be mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x258), 0x196c3671 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,88,2,25,108,54,113
0x11d4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x25c), 0x6e6b06e7 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,92,2,110,107,6,231
0x11ea mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x260), 0xfed41b76 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,96,2,254,212,27,118
0x1200 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x264), 0x89d32be0 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,100,2,137,211,43,224
0x1216 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x268), 0x10da7a5a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,104,2,16,218,122,90
0x122c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x26c), 0x67dd4acc 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,108,2,103,221,74,204
0x1242 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x270), 0xf9b9df6f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,112,2,249,185,223,111
0x1258 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x274), 0x8ebeeff9 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,116,2,142,190,239,249
0x126e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x278), 0x17b7be43 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,120,2,23,183,190,67
0x1284 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x27c), 0x60b08ed5 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,124,2,96,176,142,213
0x129a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x280), 0xd6d6a3e8 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,128,2,214,214,163,232
0x12b0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x284), 0xa1d1937e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,132,2,161,209,147,126
0x12c6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x288), 0x38d8c2c4 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,136,2,56,216,194,196
0x12dc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x28c), 0x4fdff252 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,140,2,79,223,242,82
0x12f2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x290), 0xd1bb67f1 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,144,2,209,187,103,241
0x1308 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x294), 0xa6bc5767 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,148,2,166,188,87,103
0x131e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x298), 0x3fb506dd 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,152,2,63,181,6,221
0x1334 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x29c), 0x48b2364b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,156,2,72,178,54,75
0x134a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2a0), 0xd80d2bda 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,160,2,216,13,43,218
0x1360 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2a4), 0xaf0a1b4c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,164,2,175,10,27,76
0x1376 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2a8), 0x36034af6 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,168,2,54,3,74,246
0x138c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2ac), 0x41047a60 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,172,2,65,4,122,96
0x13a2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2b0), 0xdf60efc3 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,176,2,223,96,239,195
0x13b8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2b4), 0xa867df55 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,180,2,168,103,223,85
0x13ce mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2b8), 0x316e8eef 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,184,2,49,110,142,239
0x13e4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2bc), 0x4669be79 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,188,2,70,105,190,121
0x13fa mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2c0), 0xcb61b38c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,192,2,203,97,179,140
0x1410 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2c4), 0xbc66831a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,196,2,188,102,131,26
0x1426 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2c8), 0x256fd2a0 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,200,2,37,111,210,160
0x143c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2cc), 0x5268e236 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,204,2,82,104,226,54
0x1452 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2d0), 0xcc0c7795 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,208,2,204,12,119,149
0x1468 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2d4), 0xbb0b4703 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,212,2,187,11,71,3
0x147e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2d8), 0x220216b9 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,216,2,34,2,22,185
0x1494 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2dc), 0x5505262f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,220,2,85,5,38,47
0x14aa mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2e0), 0xc5ba3bbe 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,224,2,197,186,59,190
0x14c0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2e4), 0xb2bd0b28 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,228,2,178,189,11,40
0x14d6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2e8), 0x2bb45a92 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,232,2,43,180,90,146
0x14ec mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2ec), 0x5cb36a04 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,236,2,92,179,106,4
0x1502 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2f0), 0xc2d7ffa7 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,240,2,194,215,255,167
0x1518 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2f4), 0xb5d0cf31 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,244,2,181,208,207,49
0x152e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2f8), 0x2cd99e8b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,248,2,44,217,158,139
0x1544 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x2fc), 0x5bdeae1d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,2,252,2,91,222,174,29
0x155a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x300), 0x9b64c2b0 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,0,2,155,100,194,176
0x1570 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x304), 0xec63f226 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,4,2,236,99,242,38
0x1586 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x308), 0x756aa39c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,8,2,117,106,163,156
0x159c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x30c), 0x26d930a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,12,2,2,109,147,10
0x15b2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x310), 0x9c0906a9 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,16,2,156,9,6,169
0x15c8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x314), 0xeb0e363f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,20,2,235,14,54,63
0x15de mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x318), 0x72076785 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,24,2,114,7,103,133
0x15f4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x31c), 0x5005713 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,28,2,5,0,87,19
0x160a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x320), 0x95bf4a82 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,32,2,149,191,74,130
0x1620 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x324), 0xe2b87a14 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,36,2,226,184,122,20
0x1636 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x328), 0x7bb12bae 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,40,2,123,177,43,174
0x164c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x32c), 0xcb61b38 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,44,2,12,182,27,56
0x1662 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x330), 0x92d28e9b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,48,2,146,210,142,155
0x1678 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x334), 0xe5d5be0d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,52,2,229,213,190,13
0x168e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x338), 0x7cdcefb7 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,56,2,124,220,239,183
0x16a4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x33c), 0xbdbdf21 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,60,2,11,219,223,33
0x16ba mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x340), 0x86d3d2d4 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,64,2,134,211,210,212
0x16d0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x344), 0xf1d4e242 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,68,2,241,212,226,66
0x16e6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x348), 0x68ddb3f8 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,72,2,104,221,179,248
0x16fc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x34c), 0x1fda836e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,76,2,31,218,131,110
0x1712 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x350), 0x81be16cd 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,80,2,129,190,22,205
0x1728 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x354), 0xf6b9265b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,84,2,246,185,38,91
0x173e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x358), 0x6fb077e1 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,88,2,111,176,119,225
0x1754 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x35c), 0x18b74777 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,92,2,24,183,71,119
0x176a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x360), 0x88085ae6 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,96,2,136,8,90,230
0x1780 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x364), 0xff0f6a70 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,100,2,255,15,106,112
0x1796 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x368), 0x66063bca 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,104,2,102,6,59,202
0x17ac mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x36c), 0x11010b5c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,108,2,17,1,11,92
0x17c2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x370), 0x8f659eff 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,112,2,143,101,158,255
0x17d8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x374), 0xf862ae69 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,116,2,248,98,174,105
0x17ee mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x378), 0x616bffd3 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,120,2,97,107,255,211
0x1804 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x37c), 0x166ccf45 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,124,2,22,108,207,69
0x181a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x380), 0xa00ae278 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,128,2,160,10,226,120
0x1830 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x384), 0xd70dd2ee 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,132,2,215,13,210,238
0x1846 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x388), 0x4e048354 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,136,2,78,4,131,84
0x185c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x38c), 0x3903b3c2 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,140,2,57,3,179,194
0x1872 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x390), 0xa7672661 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,144,2,167,103,38,97
0x1888 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x394), 0xd06016f7 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,148,2,208,96,22,247
0x189e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x398), 0x4969474d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,152,2,73,105,71,77
0x18b4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x39c), 0x3e6e77db 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,156,2,62,110,119,219
0x18ca mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3a0), 0xaed16a4a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,160,2,174,209,106,74
0x18e0 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3a4), 0xd9d65adc 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,164,2,217,214,90,220
0x18f6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3a8), 0x40df0b66 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,168,2,64,223,11,102
0x190c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3ac), 0x37d83bf0 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,172,2,55,216,59,240
0x1922 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3b0), 0xa9bcae53 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,176,2,169,188,174,83
0x1938 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3b4), 0xdebb9ec5 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,180,2,222,187,158,197
0x194e mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3b8), 0x47b2cf7f 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,184,2,71,178,207,127
0x1964 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3bc), 0x30b5ffe9 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,188,2,48,181,255,233
0x197a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3c0), 0xbdbdf21c 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,192,2,189,189,242,28
0x1990 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3c4), 0xcabac28a 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,196,2,202,186,194,138
0x19a6 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3c8), 0x53b39330 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,200,2,83,179,147,48
0x19bc mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3cc), 0x24b4a3a6 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,204,2,36,180,163,166
0x19d2 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3d0), 0xbad03605 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,208,2,186,208,54,5
0x19e8 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3d4), 0xcdd70693 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,212,2,205,215,6,147
0x19fe mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3d8), 0x54de5729 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,216,2,84,222,87,41
0x1a14 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3dc), 0x23d967bf 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,220,2,35,217,103,191
0x1a2a mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3e0), 0xb3667a2e 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,224,2,179,102,122,46
0x1a40 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3e4), 0xc4614ab8 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,228,2,196,97,74,184
0x1a56 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3e8), 0x5d681b02 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,232,2,93,104,27,2
0x1a6c mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3ec), 0x2a6f2b94 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,236,2,42,111,43,148
0x1a82 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3f0), 0xb40bbe37 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,240,2,180,11,190,55
0x1a98 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3f4), 0xc30c8ea1 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,244,2,195,12,142,161
0x1aae mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3f8), 0x5a05df1b 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,248,2,90,5,223,27
0x1ac4 mov *Int32(0xfffffbf8, Register(20, 4, 1), 0x3fc), 0x2d02ef8d 								2,4,3,2,255,255,251,248,1,20,4,1,2,0,0,3,252,2,45,2,239,141
0x1ada mov Register(0, 4, 1), *Int32(0xc, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,12,1,20,4,1
0x1aea mov *Int32(0xfffffffc, Register(20, 4, 1)), Register(0, 4, 1) 								2,4,2,2,255,255,255,252,1,20,4,1,1,0,4,1
0x1afa mov Register(4, 4, 1), *Int32(0x8, Register(20, 4, 1)) 									2,1,4,4,1,4,2,2,0,0,0,8,1,20,4,1
0x1b0a xor Register(4, 4, 1), 0xffffffff 															11,1,4,4,1,2,255,255,255,255
0x1b14 mov *Int32(0x8, Register(20, 4, 1)), Register(4, 4, 1) 									2,4,2,2,0,0,0,8,1,20,4,1,1,4,4,1
0x1b24 mov Register(8, 4, 1), *Int32(0x10, Register(20, 4, 1)) 									2,1,8,4,1,4,2,2,0,0,0,16,1,20,4,1
0x1b34 mov *Int32(0xfffffff8, Register(20, 4, 1)), Register(8, 4, 1) 								2,4,2,2,255,255,255,248,1,20,4,1,1,8,4,1
0x1b44 mov Register(0, 4, 1), *Int32(0x10, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,16,1,20,4,1
0x1b54 sub Register(0, 4, 1), 0x1 																3,1,0,4,1,2,0,0,0,1
0x1b5e mov *Int32(0x10, Register(20, 4, 1)), Register(0, 4, 1) 									2,4,2,2,0,0,0,16,1,20,4,1,1,0,4,1
0x1b6e tst *Int32(0xfffffff8, Register(20, 4, 1)), 0x0 											6,4,2,2,255,255,255,248,1,20,4,1,2,0,0,0,0
0x1b7f breq 0x1c28 																				16,2,0,0,28,40
0x1b85 mov Register(4, 4, 1), *Int32(0xfffffffc, Register(20, 4, 1)) 								2,1,4,4,1,4,2,2,255,255,255,252,1,20,4,1
0x1b95 movsx Register(8, 4, 1), *Int8(Register(4, 4, 1)) 											10,1,8,4,1,3,1,1,4,4,1
0x1ba0 xor Register(8, 4, 1), *Int32(0x8, Register(20, 4, 1)) 									11,1,8,4,1,4,2,2,0,0,0,8,1,20,4,1
0x1bb0 and Register(8, 4, 1), 0xff 																17,1,8,4,1,2,0,0,0,255
0x1bba mov Register(0, 4, 1), *Int32(0x8, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1
0x1bca sar Register(0, 4, 1), 0x8 																18,1,0,4,1,2,0,0,0,8
0x1bd4 xor Register(0, 4, 1), *Int32(0xfffffbf8, Register(20, 4, 1), Register(8, 4, 4)) 			11,1,0,4,1,4,3,2,255,255,251,248,1,20,4,1,1,8,4,4
0x1be8 mov *Int32(0x8, Register(20, 4, 1)), Register(0, 4, 1) 									2,4,2,2,0,0,0,8,1,20,4,1,1,0,4,1
0x1bf8 mov Register(4, 4, 1), *Int32(0xfffffffc, Register(20, 4, 1)) 								2,1,4,4,1,4,2,2,255,255,255,252,1,20,4,1
0x1c08 add Register(4, 4, 1), 0x1 																5,1,4,4,1,2,0,0,0,1
0x1c12 mov *Int32(0xfffffffc, Register(20, 4, 1)), Register(4, 4, 1) 								2,4,2,2,255,255,255,252,1,20,4,1,1,4,4,1
0x1c22 brk 0x1b24 																				8,2,0,0,27,36
0x1c28 mov Register(0, 4, 1), *Int32(0x8, Register(20, 4, 1)) 									2,1,0,4,1,4,2,2,0,0,0,8,1,20,4,1
0x1c38 xor Register(0, 4, 1), 0xffffffff 															11,1,0,4,1,2,255,255,255,255
0x1c42 mov Register(16, 4, 1), Register(20, 4, 1) 												2,1,16,4,1,1,20,4,1
0x1c4b pop Register(20, 4, 1) 																	14,1,20,4,1
0x1c50 ret 0x0 																					15,2,0,0,0,0
```

### Setting up the Javascript/Assembly Debugger

Now we can properly begin debugging the assembly code. I'm using Vivaldi which uses the same debugger as Chrome. First load the local copy of the challenge with the Javascript deobfuscated. Now open the Inspect console and go to the Sources tab to get to the Javascript debugger. Place a breakpoint on line 341. You'll also want the programCounter as a Watch variable. I also put some of the registers as Watches. 

![](./img/700WebKeygen-1.jpg)

Breakpoints in the assembly can be set by Editing the line 341 breakpoint to only trigger when programCounter is a specific value. There are two primary sections of the program that check the password. One, starting at `0x04c7`, verifies that the password meets certain requirements. The second, starting at `0x03a9` uses that password as an XOR key to decrypt the flag. 

### Password Validation
The password validation function starting at `0x04c7` starts by loading 255 32-bit numbers into the memory array. We will refer to this as the pad. It initializes the blockchain to `0xEDCBA987` Then it loops through each character (c) of the password and does the following steps.
1. Takes the last byte of the current block (`0x87` on the first loop)
1. XOR's that byte with the current char of the password
1. Uses that byte as an index into the pad to get a key 
1. Shifts the block 8 bits to the right (`0xEDCBA987` becomes `0x00EDCBA9`)
1. XOR's the block with the key from step 3

Just before the function returns the block is XOR'd with 0xFFFFFFFF to get the inverse
After the function completes and returns, the block is stored in register 0. On line `0x0377` it is tested with `0x33e5ae40` and if it is not equal, the program enters its fail state. We can work backwards to get a relationship for the last 4 blocks.  
We know the last block must be `0x33e5ae40 ^ 0xffffffff = 0xcc1a51bf`. Since each block is right shifted, the first byte is always 0. This means the key from the pad must start with `0xcc`. The index of that key is `0xDF`, and the key is `0xcc0c7795`. We can find the last 3 bytes of the previous block by XOR'ing the current block with the key. `0xcc0c7795 ^ 0xcc1a51bf = 0x0016262a`. We can work backwards to find the last 4 strings, but beyond that we need more information. Since we know the string indexes, once we know the first 4 characters, we can easily solve for the last 4. I set up a Google Sheets spreadsheet to help me since Google Sheets can XOR numbers, index into a list, and easily set up complex relationships. 

![](./img/700WebKeygen-2.jpg)

We can find the missing values by looking at the second part of the program.  

### Flag Recovery

Flag recovery happens on line `0x03a9`, but before that the encoded flag is loaded into memory starting on line `0x001d`. The first four bytes of the encoded flag are `73 14 20 17`. Knowing that the flag will start with `KLCTF` we can XOR to get the first 4 characters `8XcC`. Plugging those into the spreadsheet gives us the key `8XcCDUhG`

![](./img/700WebKeygen-3.jpg)

Typing that into the box reveals the flag. 

![](./img/700WebKeygen-4.jpg)

###### Tylor Childers